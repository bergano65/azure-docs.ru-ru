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
ms.date: 05/28/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 1dbbb6e7bd88e08520225515c422529dc260e1b2
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377369"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Использование внедрения зависимостей в функциях Azure .NET

Функции Azure поддерживают шаблон проектирования программного обеспечения внедрения зависимостей (DI), который является методом для обеспечения [инверсии управления (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.

Функции Azure строятся на основе функций внедрения зависимостей ASP.NET Core. Прежде чем использовать функции DI в приложении "функции Azure", необходимо знать о службах, времени жизни и шаблонах разработки [ASP.NET Core внедрения зависимостей](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) .

Поддержка внедрения зависимостей начинается с функций Azure 2. x.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем можно будет использовать внедрение зависимостей, необходимо установить следующие пакеты NuGet:

- [Microsoft. Azure. functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Пакет Microsoft. NET. SDK. functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) Version 1.0.28 или более поздней версии

- Необязательно: [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) требуется только для регистрации HttpClient при запуске

## <a name="register-services"></a>Регистрация служб

Для регистрации служб можно создать метод для настройки и добавления компонентов в `IFunctionsHostBuilder` экземпляр.  Узел функций Azure создает экземпляр `IFunctionsHostBuilder` и передает его непосредственно в метод.

Чтобы зарегистрировать метод, добавьте `FunctionsStartup` атрибут Assembly, указывающий имя типа, используемое при запуске. Кроме того, код ссылается на предварительный выпуск [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) в NuGet.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;

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

## <a name="use-injected-dependencies"></a>Использовать внедренные зависимости

ASP.NET Core использует внедрение конструктора, чтобы сделать зависимости доступными для вашей функции. В следующем примере показано, как `IMyService` зависимости `HttpClient` и вставляются в функцию, активируемую HTTP.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

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

Использование внедрения конструктора означает, что не следует использовать статические функции, если вы хотите воспользоваться преимуществами внедрения зависимостей.

## <a name="service-lifetimes"></a>Время существования службы

Приложения функций Azure предоставляют те же времена жизни службы, что и [внедрение зависимостей ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): временные, с областями и одноэлементные.

В приложении-функции время существования службы соответствует времени существования для выполнения функции. Службы с заданной областью создаются один раз для каждого выполнения. Последующие запросы для этой службы во время выполнения повторно используют существующий экземпляр службы. Время существования одноэлементной службы соответствует времени существования узла и повторно используется в ходе выполнения функций на этом экземпляре.

Для подключений и клиентов рекомендуется использовать службы времени жизни Singleton, `SqlConnection` `CloudBlobClient`например экземпляры, или `HttpClient` .

Просмотр или Загрузка [образца различных жизненных циклов обслуживания](https://aka.ms/functions/di-sample) на GitHub.

## <a name="logging-services"></a>Службы ведения журналов

Если вам нужен собственный регистратор, рекомендуется зарегистрировать `ILoggerProvider` экземпляр. Application Insights автоматически добавляется функциями Azure.

> [!WARNING]
> Не добавляйте `AddApplicationInsightsTelemetry()` в коллекцию служб, так как она регистрирует службы, конфликтующие со службами, предоставляемыми средой.

## <a name="function-app-provided-services"></a>Службы, предоставляемые приложением функции

Узел функции регистрирует множество служб. Следующие службы являются надежными для использования в качестве зависимости в приложении:

|Тип службы|Срок действия|Описание|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Единый|Конфигурация среды выполнения|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Единый|Отвечает за предоставление идентификатора экземпляра узла|

Если существуют другие службы, от которых требуется зависимость, [Создайте вопрос и предложите их на GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Переопределение служб узла

Переопределение служб, предоставляемых узлом, в настоящее время не поддерживается.  Если имеются службы, которые необходимо переопределить, [создайте ошибку и предложите их на GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительных сведений см. следующие ресурсы:

- [Мониторинг приложения функции](functions-monitoring.md)
- [Рекомендации по функциям](functions-best-practices.md)
