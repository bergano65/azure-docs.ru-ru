---
title: Масштабирование с несколькими экземплярами - Служба сигнала Azure
description: Во многих сценариях масштабирования клиенту часто приходится предоставлять несколько экземпляров и настраивать их вместе, чтобы создать крупномасштабное развертывание. Например, осколки требуют поддержки нескольких экземпляров.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158159"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Как масштабировать службу SignalR с несколькими экземплярами?
Последняя служба SignalR SDK поддерживает несколько конечных точек для экземпляров службы SignalR. Эту функцию можно использовать для масштабирования одновременных соединений или для межрегионевого обмена сообщениями.

## <a name="for-aspnet-core"></a>Для ASP.NET ядра

### <a name="how-to-add-multiple-endpoints-from-config"></a>Как добавить несколько конечных точек из конфигурации?

Config с `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` ключом или для строки соединения службы SignalR.

Если ключ `Azure:SignalR:ConnectionString:`начинается с, он `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`должен `Name` `EndpointType` быть в `ServiceEndpoint` формате, где и являются свойствами объекта, и доступны из кода.

Можно добавить несколько строк соединения `dotnet` экземпляра, используя следующие команды:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Как добавить несколько конечных точек из кода?

Класс `ServicEndpoint` вводится для описания свойств конечной точки службы Azure SignalR.
Можно настроить несколько конечных точек экземпляра при использовании сервиса Azure SignalR SDK через:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Как настроить маршрутизатор конечных точек?

По умолчанию SDK использует [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) для выбора конечных точек.

#### <a name="default-behavior"></a>Поведение по умолчанию 
1. Разгром запроса клиента

    Когда `/negotiate` клиент с сервером приложения. По умолчанию SDK **случайным образом выбирает** одну конечную точку из набора доступных конечных точек обслуживания.

2. Разгром сообщений сервера

    Когда «отправка сообщения на определенный «соединение» и целевое соединение направляется на текущий сервер, сообщение переходит непосредственно к этой подключенной конечной точке. В противном случае сообщения передаются на каждую конечную точку Azure SignalR.

#### <a name="customize-routing-algorithm"></a>Настройка алгоритма разгрома
Вы можете создать свой собственный маршрутизатор, когда у вас есть специальные знания, чтобы определить, какие конечные точки сообщения должны идти.

Пользовательский маршрутизатор определяется ниже в `east-` качестве примера, когда `east`группы, начиная с всегда перейти к конечной точке с именем:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Другой приведенный ниже пример, который переопределяет поведение переговоров по умолчанию, выбор конечных точек зависит от того, где находится сервер приложения.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Не забудьте зарегистрировать маршрутизатор в контейнер DI с помощью:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>Для ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Как добавить несколько конечных точек из конфигурации?

Config с `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` ключом или для строки соединения службы SignalR.

Если ключ `Azure:SignalR:ConnectionString:`начинается с, он `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`должен `Name` `EndpointType` быть в `ServiceEndpoint` формате, где и являются свойствами объекта, и доступны из кода.

Можно добавить несколько строк `web.config`подключения экземпляра к:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Как добавить несколько конечных точек из кода?

Класс `ServicEndpoint` вводится для описания свойств конечной точки службы Azure SignalR.
Можно настроить несколько конечных точек экземпляра при использовании сервиса Azure SignalR SDK через:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Как настроить маршрутизатор?

Единственное различие между ASP.NET SignalR и ASP.NET Core `GetNegotiateEndpoint`SignalR является тип контекста http для . Для ASP.NET SignalR, он [iOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) типа.

Ниже приведен пользовательский пример переговоров для ASP.NET SignalR:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Не забудьте зарегистрировать маршрутизатор в контейнер DI с помощью:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Конфигурация в сценариях межрегионем

Объект `ServiceEndpoint` имеет `EndpointType` свойство `primary` со `secondary`значением или .

`primary`конечные точки являются предпочтительными конечными точками для получения клиентского трафика и считаются более надежными сетевыми соединениями; `secondary` конечные точки считаются менее надежными сетевыми соединениями и используются только для передачи сервера клиентского трафика, например, для передачи сообщений, а не для принятия клиента на серверный трафик.

В межрегионах сеть может быть нестабильной. Для одного сервера приложений, расположенного в *Восточной части США,* конечная `primary` точка службы SignalR, `secondary`расположенная в том же восточном регионе *США,* может быть настроена как и конечные точки в других регионах, отмеченных как . В этой конфигурации конечные точки обслуживания в других регионах могут **получать** сообщения с этого сервера приложений *в Восточной ЧАСТИ США,* но на этот сервер приложения не будет клиентов, перенаправляемых по **перекрестным регионам.** Архитектура отображается на диаграмме ниже:

![Кросс-Гео Инфра](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Когда клиент `/negotiate` пытается с сервером приложения, с маршрутизатором по умолчанию, SDK `primary` случайным образом **выбирает** одну конечную точку из набора доступных конечных точек. Когда основная конечная точка недоступна, SDK случайным образом `secondary` выбирает из всех **доступных конечных** точек. Конечная точка помечается как **доступная** при подключении между сервером и конечная точка службы.

В кросс-регионе, когда клиент `/negotiate` пытается с сервером приложения, размещенным в `primary` *Восточной части США,* по умолчанию он всегда возвращает конечную точку, расположенную в том же регионе. Когда все конечные точки *Восточной США* недоступны, клиент перенаправляется в конечные точки в других регионах. Раздел Fail-over ниже подробно описывает сценарий.

![Нормальные переговоры](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Неудача за

Когда `primary` все конечные точки недоступны, клиент `/negotiate` выбирает `secondary` из доступных конечных точек. Этот механизм сбоя требует, чтобы каждая конечная точка служила `primary` конечным пунктом, по крайней мере, одному серверу приложения.

![Неудача за](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали о том, как настроить несколько экземпляров в одном приложении для масштабирования, осколков и кросс-регионов сценариев.

Несколько конечных точек поддержки также могут быть использованы в сценариях высокой доступности и аварийного восстановления.

> [!div class="nextstepaction"]
> [Настройка сигнальной службы для аварийного восстановления и высокой доступности](./signalr-concept-disaster-recovery.md)
