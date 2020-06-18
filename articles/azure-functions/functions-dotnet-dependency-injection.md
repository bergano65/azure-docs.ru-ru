---
title: Использование внедрения зависимостей в Функциях Azure .NET
description: Узнайте, как использовать внедрение зависимостей для регистрации и использования служб в функциях .NET
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 97e8a34f3b8639990f8de736a8f1f7429ebfd448
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739147"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Использование внедрения зависимостей в Функциях Azure .NET

Функции Azure поддерживают шаблон разработки программного обеспечения с внедрением зависимостей (DI), который является приемом [инверсии управления (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.

- Внедрение зависимостей в Функциях Azure основано на функциях внедрения зависимостей .NET Core. Рекомендуется ознакомиться с [Внедрением зависимостей .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Существуют различия в переопределении зависимостей и считывании значений конфигурации, если служба Функций Azure использует план потребления.

- Поддержка внедрения зависимостей появилась в Функциях Azure 2.x.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем можно будет использовать внедрение зависимостей, необходимо установить следующие пакеты NuGet:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/);

- пакет [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) версии 1.0.28 или более поздней.

## <a name="register-services"></a>Регистрация служб

Чтобы зарегистрировать службы, создайте метод для настройки и добавления компонентов в экземпляр `IFunctionsHostBuilder`.  Узел функций Azure создает экземпляр `IFunctionsHostBuilder` и передает его непосредственно в метод.

Чтобы зарегистрировать метод, добавьте атрибут сборки `FunctionsStartup`, указывающий имя типа, используемого во время запуска.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>Предупреждения

Последовательность действий по регистрации выполняется до и после того, как среда выполнения обработает класс запуска. Поэтому помните следующие важные моменты.

- *Класс запуска предназначен только для установки и регистрации.* Не используйте службы, зарегистрированные при запуске, в процессе запуска. Например, не пытайтесь зарегистрировать сообщение в средстве ведения журнала, регистрируемом во время запуска. Эта точка процесса регистрации слишком ранняя, чтобы службы были доступны для использования. После выполнения метода `Configure` среда выполнения Функций продолжит регистрировать дополнительные зависимости, что может повлиять на работу служб.

- *Контейнер внедрения зависимостей содержит только явно зарегистрированные типы*. Единственными службами, доступными в качестве внедряемых типов, являются те, что настроены в методе `Configure`. В результате такие относящиеся к Функциям типы, как `BindingContext` и `ExecutionContext`, недоступны во время установки или в качестве типов для внедрения.

## <a name="use-injected-dependencies"></a>Использование внедренных зависимостей

Внедрение конструктора используется для обеспечения доступности зависимостей в функции. Использование внедрения конструктора требует неиспользования статических классов.

В следующем примере показано, как зависимости `IMyService` и `HttpClient` вставляются в функцию, активируемую HTTP. В этом примере используется пакет [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/), необходимый для регистрации `HttpClient` при запуске.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient httpClient)
        {
            _service = service;
            _client = httpClient;
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>Время существования служб

Приложения Функций Azure предоставляют те же самые времена существования служб, что и [внедрение зависимостей ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Для приложения Функций различные времена существования службы ведут себя следующим образом.

- **Временная**: временные службы создаются при каждом запросе службы.
- **С заданной областью**: время существования службы с заданной областью соответствует времени выполнения функции. Службы с заданной областью создаются один раз для каждого выполнения. Последующие запросы к этой службе во время выполнения повторно используют существующий экземпляр службы.
- **Отдельная**: время существования отдельной службы соответствует времени существования узла. Она повторно используется при повторном выполнении функций на этом экземпляре. Для подключений и клиентов рекомендуется использовать службы со временем жизни «отдельная», например экземпляры `SqlConnection` или `HttpClient`.

Просмотрите или скачайте [образцы служб с различными временами существования](https://aka.ms/functions/di-sample) на GitHub.

## <a name="logging-services"></a>Службы ведения журналов

Если вам нужен собственный регистратор, зарегистрируйте пользовательский тип в качестве экземпляра `ILoggerProvider`. Application Insights автоматически добавляется Функциями Azure.

> [!WARNING]
> - Не добавляйте `AddApplicationInsightsTelemetry()` в коллекцию служб, поскольку он регистрирует службы, конфликтующие со службами, предоставляемыми средой.
> - Не регистрируйте собственную `TelemetryConfiguration` или `TelemetryClient`, если используются встроенные функции Application Insights. Если необходимо настроить собственный экземпляр `TelemetryClient`, создайте его с помощью внедренного `TelemetryConfiguration`, как показано в [Мониторинг Функций Azure](./functions-monitoring.md#version-2x-and-later-2).

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> и ILoggerFactory

Узел будет внедрять службы `ILogger<T>` и `ILoggerFactory` в конструкторы.  Однако по умолчанию эти новые фильтры ведения журнала будут отфильтрованы из журналов функций.  Необходимо будет внести изменения в файл `host.json`, чтобы выбрать дополнительные фильтры и категории.  В следующем примере показано добавление `ILogger<HttpTrigger>` с журналами, которые будут предоставляться узлом,

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

и файла `host.json`, который добавляет фильтр журнала.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>Службы, предоставляемые приложением-функцией

Узел функции регистрирует множество служб. Следующие службы можно уверенно использовать в качестве зависимости в приложении.

|Тип службы|Срок действия|Описание|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Отдельная|Конфигурация среды выполнения|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Отдельная|Отвечает за предоставление идентификатора экземпляра узла.|

Если существуют другие службы, от которых требуется зависимость, [создайте вопрос и предложите его на GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Переопределение служб узла

Переопределение служб, предоставляемых узлом, в настоящее время не поддерживается.  Если имеются службы, которые необходимо переопределить, [создайте вопрос и предложите его на GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Работа с настройками и параметрами

Значения, определенные в [параметрах приложения](./functions-how-to-use-azure-function-app-settings.md#settings), доступны в экземпляре `IConfiguration`, который позволяет считывать значения параметров приложения в классе запуска.

Значения можно извлечь из экземпляра `IConfiguration` в пользовательский тип. Копирование значений параметров приложения в пользовательский тип упрощает тестирование служб, делая эти значения внедряемыми. Параметры, считываемые в экземпляр конфигурации, должны быть простыми парами «ключ-значение».

Рассмотрим следующий класс, который включает свойство, именуемое consistent, с параметром приложения,

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

и файл `local.settings.json`, который может структурировать пользовательский параметр следующим образом.
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

Внутри метода `Startup.Configure` можно извлечь значения из экземпляра `IConfiguration` в пользовательский тип с помощью следующего кода.

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

Вызов `Bind` копирует значения, которые соответствуют именам свойств из конфигурации, в пользовательский экземпляр. Экземпляр параметров теперь доступен в контейнере IoC для внедрения в функцию.

Объект параметров внедряется в функцию как экземпляр универсального интерфейса `IOptions`. Используйте свойство `Value` для доступа к значениям, найденным в конфигурации.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Дополнительные сведения о работе с параметрами см. в [Шаблон параметров в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

> [!WARNING]
> Не пытайтесь считывать значения из таких файлов, как *local.settings.json* или *appsettings.{environment}.json*, в плане потребления. Значения, считанные из этих файлов, связанных с триггерами, недоступны при масштабировании приложения, так как инфраструктура размещения не имеет доступа к сведениям о конфигурации.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

- [Как выполнять мониторинг приложений-функций](functions-monitoring.md)
- [Рекомендации по функциям](functions-best-practices.md)
