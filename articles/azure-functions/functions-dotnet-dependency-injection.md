---
title: Использовать внедрение зависимостей в функциях Azure .NET
description: Узнайте, как использовать внедрение зависимостей для регистрации и использования служб в функциях .NET
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions, serverless architecture
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2019
ms.author: jehollan
ms.openlocfilehash: 2044718d2ec7a7acc58e1e7ba9ba04ec5caf16b3
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408453"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Использовать внедрение зависимостей в функциях Azure .NET

Функции Azure поддерживают шаблон внедрения зависимостей (DI) программного обеспечения конструктора, который является методика для обеспечения [Inversion of Control (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимости.

Функции Azure строится на основе функции внедрения зависимостей ASP.NET Core.  Необходимо понимать службы, время существования и шаблоны проектирования [внедрения зависимостей ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) перед их использованием в функциях.

## <a name="installing-dependency-injection-packages"></a>Установка пакетов внедрения зависимостей

Чтобы использовать функции внедрения зависимостей, необходимо включить пакет NuGet, который предоставляет эти API-интерфейсы.

```powershell
Install-Package Microsoft.Azure.Functions.Extensions
```

## <a name="registering-services"></a>Регистрация служб

Чтобы зарегистрировать службы, можно создать метод configure и добавить компоненты к списку `IFunctionsHostBuilder` экземпляра.  Создает узел функций Azure `IFunctionsHostBuilder` и передает его непосредственно в настроенный метод.

Для регистрации вашей настроить метод, необходимо добавить атрибут сборки, которое указывает тип для вашей Настройка с помощью метода `FunctionsStartup` атрибут.

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

## <a name="service-lifetimes"></a>Время существования службы

Приложения-функции Azure предоставляют же времени существования службы как [внедрения зависимостей ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes), временных, областью действия и singleton.

В приложении-функции время существования ограниченная служба соответствует времени существования выполнения функции. Ограниченные службы создаются один раз для каждого выполнения.  Последующие запросы для этой службы во время выполнения повторно использовать этот экземпляр.  Время существования службы одноэлементный соответствует времени существования узла и многоцелевое выполнений этой функции в этом экземпляре.

Одноэлементные службы времени существования рекомендуется использовать для подключения и клиентов, например `SqlConnection`, `CloudBlobClient`, или `HttpClient`.

Просмотреть или скачать [образец существования другой службой](https://aka.ms/functions/di-sample).

## <a name="logging-services"></a>Службы ведения журнала

Если вам нужна поставщика ведения журнала, рекомендуется зарегистрировать `ILoggerProvider`.  Для Application Insights функции Application Insights автоматически добавляет автоматически.  

> [!WARNING]
> Не добавляйте `AddApplicationInsightsTelemetry()` к службам коллекции так, как он будет зарегистрирован служб, которые конфликтуют с что предоставляются средой. 
 
## <a name="function-app-provided-services"></a>Службы приложений, которые предоставляются функции

Узел функции будет зарегистрироваться многие службы.  Ниже приведены службы, которые являются безопасными для зависимость от.  Другие службы узла не поддерживаются для регистрации или зависят.  При наличии других служб, требуется ли зависимость от, пожалуйста [создать проблему и обсуждения в GitHub](https://github.com/azure/azure-functions-host).

|Тип службы|Срок действия|Описание|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|одноэлементный|Конфигурация среды выполнения|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|одноэлементный|Отвечает за предоставление идентификатор экземпляра узла|

### <a name="overriding-host-services"></a>Переопределение размещения служб

Переопределение служб, предоставляемых ведущим приложением в настоящее время не поддерживается.  Если не требуется переопределение службы, [создать проблему и обсуждения в GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

* [Как отслеживать ваше приложение-функция](functions-monitoring.md)
* [Рекомендации по функциям](functions-best-practices.md)