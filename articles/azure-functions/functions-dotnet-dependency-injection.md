---
title: Использование внедрения зависимостей в Функциях Azure .NET
description: Узнайте, как использовать инъекцию зависимости для регистрации и использования служб в функциях .NET
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: f8f1eb353087c5121eaafb4c8789e7a2f7638b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475127"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Использование внедрения зависимостей в Функциях Azure .NET

Azure Functions поддерживает шаблон проектирования программного обеспечения для впрыски визы зависимости (DI), который является методом достижения [инверсии управления (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.

- Инъекция зависимости в функциях Azure построена на функциях инъекций зависимости ядра .NET. Рекомендуется знакомство с [инъекцией зависимости ядра .NET.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) Однако существуют различия в том, как переопределять зависимости и как значения конфигурации считываются с функциями Azure в плане потребления.

- Поддержка впрыска зависимости начинается с Azure Functions 2.x.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем использовать инъекцию зависимости необходимо установить следующие пакеты NuGet:

- [Microsoft.Azure.Functions.Расширения](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions версия пакета](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 1.0.28 или более поздняя

## <a name="register-services"></a>Регистрация служб

Для регистрации служб создайте метод настройки `IFunctionsHostBuilder` и добавления компонентов в экземпляр.  Хост Azure Functions `IFunctionsHostBuilder` создает экземпляр и передает его непосредственно в ваш метод.

Чтобы зарегистрировать метод, `FunctionsStartup` добавьте атрибут сборки, который определяет имя типа, используемое при запуске.

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

Серия этапов регистрации, запускаемых до и после выполнения процессов запуска класса. Поэтому имейте в виду следующие элементы:

- *Класс запуска предназначен только для настройки и регистрации.* Избегайте использования услуг, зарегистрированных в стартапе во время запуска. Например, не пытайтесь войти в зарегистрированное во время запуска сообщение. Этот пункт процесса регистрации слишком рано для ваших услуг, чтобы быть доступными для использования. После `Configure` запуска метода время выполнения функций продолжает регистрировать дополнительные зависимости, что может повлиять на работу служб.

- *Контейнер инъекций зависимости содержит только явно зарегистрированные типы.* Единственными услугами, доступными в качестве инъекционных типов, являются настройки в методе. `Configure` В результате, типы, `BindingContext` связанные с функциями, как и `ExecutionContext` не доступны во время установки или как инъекционные типы.

## <a name="use-injected-dependencies"></a>Использование инъекционных зависимостей

Инъекция конструктора используется для того, чтобы сделать ваши зависимости доступными в функции. Использование впрыска конструктора требует, чтобы вы не использовали статические классы.

Следующий пример показывает, `IMyService` `HttpClient` как и зависимостей вводятся в функцию HTTP-триггера. В этом примере используется пакет [Microsoft.Extensions.Http,](https://www.nuget.org/packages/Microsoft.Extensions.Http/) необходимый `HttpClient` для регистрации при запуске.

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

Приложения Azure Functions обеспечивают те же сроки [службы, что и ASP.NET инъекций зависимости.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes) Для приложения Функции различные сроки службы ведут себя следующим образом:

- **Переходные**: Переходные службы создаются по каждому запросу службы.
- **Область:** Срок службы с фертрядной сыт по срокам выполнения функций. Скопированные службы создаются один раз за выполнение. Позже запросы на эту службу во время выполнения повторно используют существующий экземпляр службы.
- **Singleton**: Продолжительность службы синглтона соответствует сроку службы хоста и повторно используется в выполнении функций в этом случае. Сервисы Singleton lifetime рекомендуются, `SqlConnection` например, `HttpClient` для подключений и клиентов.

Просмотр или загрузка [образца различных сроков службы](https://aka.ms/functions/di-sample) на GitHub.

## <a name="logging-services"></a>Услуги по ведению журнала

Если вам нужен собственный поставщик журналов, зарегистрируйте пользовательский тип в качестве экземпляра. `ILoggerProvider` Анализ приложений добавляется функциями Azure Functions автоматически.

> [!WARNING]
> - Не добавляйте `AddApplicationInsightsTelemetry()` в коллекцию услуг, так как он регистрирует услуги, которые противоречат услугам, предоставляемым окружающей средой.
> - Не регистрируйте `TelemetryConfiguration` свой `TelemetryClient` собственный или если вы используете встроенную функциональность Application Insights. Если вам нужно настроить `TelemetryClient` свой собственный экземпляр, `TelemetryConfiguration` создайте его с помощью впрыскиваемого, как показано в [функциях Monitor Azure.](./functions-monitoring.md#version-2x-and-later-2)

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> и ILoggerFactory

Хозяин будет `ILogger<T>` вводить и `ILoggerFactory` услуги в конструкторов.  Однако по умолчанию эти новые фильтры регистрации будут отфильтрованы из журналов функций.  Вам нужно будет `host.json` изменить файл, чтобы выбрать в дополнительные фильтры и категории.  Следующий пример демонстрирует `ILogger<HttpTrigger>` добавление с журналами, которые будут выставлены хостом.

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

И `host.json` файл, который добавляет фильтр журнала.

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

## <a name="function-app-provided-services"></a>Функциональное приложение предоставляло услуги

Функция хоста регистрирует множество служб. Следующие службы можно принять в качестве зависимости в приложении:

|Тип службы|Время существования|Описание|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Одноэлементный|Конфигурация времени выполнения|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Одноэлементный|Ответственный за предоставление идентификатора экземпляра хоста|

Если есть другие службы, на которые вы хотите получить зависимость, [создайте проблему и предложите их на GitHub.](https://github.com/azure/azure-functions-host)

### <a name="overriding-host-services"></a>Преобладающие услуги хоста

В настоящее время не поддерживается оказание услуг, предоставляемых хостом.  Если есть службы, которые вы хотите переопределить, [создайте проблему и предложите их на GitHub.](https://github.com/azure/azure-functions-host)

## <a name="working-with-options-and-settings"></a>Работа с параметрами и настройками

Значения, определенные в [настройках приложения,](./functions-how-to-use-azure-function-app-settings.md#settings) доступны в экземпляре, `IConfiguration` что позволяет читать значения параметров приложения в классе запуска.

Можно извлечь значения из `IConfiguration` экземпляра в пользовательский тип. Копирование значений параметров приложения в пользовательском типе позволяет легко проверить свои службы, сделав эти значения инъекционными. Настройки, считываемые в экземпляре конфигурации, должны быть простыми парами ключей/значений.

Рассмотрим следующий класс, который включает свойство, названное в соответствии с настройками приложения:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

И `local.settings.json` файл, который может структурировать пользовательские настройки следующим образом:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

Из метода `Startup.Configure` можно извлечь значения из `IConfiguration` экземпляра в пользовательский тип, используя следующий код:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

Вызов `Bind` значений копий, которые соответствуют именам свойств из конфигурации в пользовательский экземпляр. Экземпляр опций теперь доступен в контейнере IoC для впрыскивания в функцию.

Объект опций вводится в функцию в `IOptions` качестве экземпляра общего интерфейса. Используйте `Value` свойство для доступа к значениям, найденным в конфигурации.

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

Для получения более подробной информации о работе с опциями обратитесь к [шаблону Options в ASP.NET Core.](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)

> [!WARNING]
> Избегайте попыток чтения значений из файлов, таких как *local.settings.json* или *appsettings. окружающей среды.json* в плане потребления. Значения, считываемые из этих файлов, связанных с триггерными соединениями, недоступны, так как приложение масштабируется, поскольку инфраструктура хостинга не имеет доступа к информации о конфигурации.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих ресурсах:

- [Как контролировать приложение функции](functions-monitoring.md)
- [Рекомендации по функциям](functions-best-practices.md)
