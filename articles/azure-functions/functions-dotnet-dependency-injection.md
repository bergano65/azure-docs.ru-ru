---
title: Использовать внедрение зависимостей в функциях Azure .NET
description: Узнайте, как использовать внедрение зависимостей для регистрации и использования служб в функциях .NET
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, functions, serverless architecture
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: jehollan, glenga, cshoe
ms.openlocfilehash: b1a6751f0d788c26af60b28eee994dc9b3877f00
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693247"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Использовать внедрение зависимостей в функциях Azure .NET

Функции Azure поддерживают шаблон внедрения зависимостей (DI) программного обеспечения конструктора, который является способ достижения [Inversion of Control (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимости.

Функции Azure строится на основе функции внедрения зависимостей ASP.NET Core. Оповещения службы, время существования и шаблоны проектирования [внедрения зависимостей ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) перед использованием функции внедрения Зависимостей в функциях Azure рекомендуется использовать приложение.

## <a name="prerequisites"></a>Технические условия

Прежде чем использовать внедрение зависимостей, необходимо установить следующие пакеты NuGet:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Пакет Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) версии 1.0.28 или более поздней версии

## <a name="register-services"></a>Регистрация служб

Чтобы зарегистрировать службы, можно создать метод для настройки и добавить компоненты к списку `IFunctionsHostBuilder` экземпляра.  Узел функций Azure создает экземпляр класса `IFunctionsHostBuilder` и передает его непосредственно в методе.

Чтобы зарегистрировать метод, добавьте `FunctionsStartup` атрибута сборки, который задает имя типа, используемый при запуске системы.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>Использовать внедренный зависимости

ASP.NET Core использует внедрение через конструктор для создания зависимостей, доступных в функцию. В следующем образце показано как `IMyService` и `HttpClient` зависимостей внедряются в функцию, активируемую HTTP.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();;
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

Использование внедрение через конструктор означает, что не следует использовать статические функции Если вы хотите воспользоваться преимуществами внедрения зависимостей.

## <a name="service-lifetimes"></a>Время существования службы

Приложения функций Azure обеспечивают же времени существования службы как [внедрения зависимостей ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): временными, областью действия и singleton.

В приложение функций время существования ограниченная служба соответствует времени существования выполнения функции. Ограниченные службы создаются один раз для каждого выполнения. Последующие запросы для этой службы во время выполнения повторно использовать существующий экземпляр службы. Время существования службы одноэлементный соответствует времени существования узла и многоцелевое выполнений этой функции в этом экземпляре.

Одноэлементные службы времени существования рекомендуется использовать для подключения и клиентов, например `SqlConnection`, `CloudBlobClient`, или `HttpClient` экземпляров.

Просмотреть или скачать [образец существования другой службой](https://aka.ms/functions/di-sample) на сайте GitHub.

## <a name="logging-services"></a>Службы ведения журнала

Если вам нужна поставщика ведения журнала, рекомендуется зарегистрировать `ILoggerProvider` экземпляра. Application Insights будет автоматически добавляться функциями Azure.

> [!WARNING]
> Не добавляйте `AddApplicationInsightsTelemetry()` в коллекцию служб, так как он регистрирует службы конфликтующие со службами, предоставляемую средой.

## <a name="function-app-provided-services"></a>Службы приложений, которые предоставляются функции

Функция основное приложение регистрирует многие службы. Следующие службы можно безопасно получать в качестве зависимости в приложении:

|Тип службы|Срок действия|Описание|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|одноэлементный|Конфигурация среды выполнения|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|одноэлементный|Отвечает за предоставление идентификатор экземпляра узла|

При наличии других служб, которые вы хотите использовать зависимость от, [создать проблему и предложить их на сайте GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Переопределение размещения служб

Переопределение служб, предоставляемых ведущим приложением в настоящее время не поддерживается.  При наличии служб, необходимо переопределить, [создать проблему и предложить их на сайте GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

- [Как отслеживать ваше приложение-функция](functions-monitoring.md)
- [Рекомендации по функциям](functions-best-practices.md)
