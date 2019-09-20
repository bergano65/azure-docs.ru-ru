---
title: Использование внедрения зависимостей в функциях Azure .NET
description: Узнайте, как использовать внедрение зависимостей для регистрации и использования служб в функциях .NET.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: azure functions, functions, serverless architecture
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: e1cf67abcc44a3ca134e5435137869d4fff1a7eb
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162359"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Использование внедрения зависимостей в функциях Azure .NET

Функции Azure поддерживают шаблон проектирования программного обеспечения внедрения зависимостей (DI), который является методом для обеспечения [инверсии управления (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.

- Внедрение зависимостей в функции Azure основано на функциях внедрения зависимостей .NET Core. Рекомендуется ознакомиться с [внедрением зависимостей .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) . Однако существуют различия между переопределением зависимостей и считыванием значений конфигурации с помощью функций Azure в плане потребления.

- Поддержка внедрения зависимостей начинается с функций Azure 2. x.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем можно будет использовать внедрение зависимостей, необходимо установить следующие пакеты NuGet:

- [Microsoft. Azure. functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Пакет Microsoft. NET. SDK. functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) Version 1.0.28 или более поздней версии

## <a name="register-services"></a>Регистрация служб

Чтобы зарегистрировать службы, создайте метод для настройки и добавления компонентов в `IFunctionsHostBuilder` экземпляр.  Узел функций Azure создает экземпляр `IFunctionsHostBuilder` и передает его непосредственно в метод.

Чтобы зарегистрировать метод, добавьте `FunctionsStartup` атрибут Assembly, указывающий имя типа, используемое при запуске.

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

Последовательность действий по регистрации выполняется до и после того, как среда выполнения обработает класс Startup. Поэтому следует учитывать следующие элементы:

- *Класс Startup предназначен только для установки и регистрации.* Не используйте службы, зарегистрированные при запуске в процессе запуска. Например, не пытайтесь зарегистрировать сообщение в средстве ведения журнала, регистрируемом во время запуска. Эта точка процесса регистрации слишком ранняя, чтобы службы были доступны для использования. После выполнения `Configure` метода среда выполнения функций продолжит регистрировать дополнительные зависимости, что может повлиять на работу служб.

- *Контейнер внедрения зависимостей содержит только явно зарегистрированные типы*. Единственными службами, доступными в качестве подставляемых типов, являются настройки `Configure` в методе. В результате такие `BindingContext` типы функций, как и `ExecutionContext` , недоступны во время установки или в качестве типов для вставки.

## <a name="use-injected-dependencies"></a>Использовать внедренные зависимости

Внедрение конструктора используется для обеспечения доступности зависимостей в функции. Использование внедрения конструктора требует, чтобы не использовались статические классы.

В следующем примере показано, как `IMyService` зависимости `HttpClient` и вставляются в функцию, активируемую HTTP. В этом примере используется пакет [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) , необходимый для `HttpClient` регистрации во время запуска.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
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

## <a name="service-lifetimes"></a>Время существования службы

Приложения функций Azure предоставляют те же времена жизни службы, что и [внедрение зависимостей ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Для приложения-функции различные времена жизни службы ведут себя следующим образом:

- **Временный**: Временные службы создаются при каждом запросе службы.
- С **областью**действия: Время существования службы в области соответствует времени существования выполнения функции. Службы с заданной областью создаются один раз для каждого выполнения. Последующие запросы для этой службы во время выполнения повторно используют существующий экземпляр службы.
- **Singleton**: Время существования одноэлементной службы соответствует времени существования узла и повторно используется в ходе выполнения функций на этом экземпляре. Для соединений и клиентов, например `SqlConnection` или `HttpClient` экземпляров, рекомендуется использовать службы с жизненным циклом Singleton.

Просмотр или Загрузка [образца различных жизненных циклов обслуживания](https://aka.ms/functions/di-sample) на GitHub.

## <a name="logging-services"></a>Службы ведения журналов

Если вам нужен собственный поставщик ведения журнала, зарегистрируйте пользовательский тип в качестве `ILoggerProvider` экземпляра. Application Insights автоматически добавляется функциями Azure.

> [!WARNING]
> - Не добавляйте `AddApplicationInsightsTelemetry()` в коллекцию служб, так как она регистрирует службы, конфликтующие со службами, предоставляемыми средой.
> - Не зарегистрируйте собственный `TelemetryConfiguration` объект или `TelemetryClient` при использовании встроенных функций Application Insights.

## <a name="function-app-provided-services"></a>Службы, предоставляемые приложением функции

Узел функции регистрирует множество служб. Следующие службы являются надежными для использования в качестве зависимости в приложении:

|Тип службы|Срок действия|Описание|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Единый|Конфигурация среды выполнения|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Единый|Отвечает за предоставление идентификатора экземпляра узла|

Если существуют другие службы, от которых требуется зависимость, [Создайте вопрос и предложите их на GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Переопределение служб узла

Переопределение служб, предоставляемых узлом, в настоящее время не поддерживается.  Если имеются службы, которые необходимо переопределить, [создайте ошибку и предложите их на GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Работа с параметрами и параметрами

Значения, определенные в [параметрах приложения](./functions-how-to-use-azure-function-app-settings.md#settings) , доступны `IConfiguration` в экземпляре служб, который позволяет считывать значения параметров приложения в классе Startup.

Значения из `IConfiguration` экземпляра можно извлечь в пользовательский тип. Копирование значений параметров приложения в пользовательский тип упрощает тестирование служб, делая эти значения внедренными. Рассмотрим следующий класс, включающий свойство с именем consistent и параметром приложения.

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

В методе можно извлечь значения `IConfiguration` из экземпляра в пользовательский тип с помощью следующего кода: `Startup.Configure`

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.Bind(settings);
                                           });
```

Вызов `Bind` копирует значения, которые соответствуют именам свойств из конфигурации, в пользовательский экземпляр. Экземпляр Options теперь доступен в контейнере IoC для внедрения в функцию.

Объект Options внедряется в функцию как экземпляр универсального `IOptions` интерфейса. `Value` Используйте свойство для доступа к значениям, найденным в конфигурации.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _service = service;
        _settings = options.Value;
    }
}
```

Дополнительные сведения о работе с параметрами см. в статье [шаблон параметров в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) .

> [!WARNING]
> Старайтесь не пытаться считывать значения из таких файлов, как *Local. Settings. JSON* или *appSettings. { Environment}. JSON* в плане потребления. Значения, считанные из этих файлов, связанных с триггерами, недоступны при масштабировании приложения, так как инфраструктура размещения не имеет доступа к сведениям о конфигурации.

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительных сведений см. следующие ресурсы:

- [Мониторинг приложения функции](functions-monitoring.md)
- [Рекомендации по функциям](functions-best-practices.md)
