---
title: Масштабирование с несколькими экземплярами для службы Azure SignalR
description: Во многих сценариях масштабирования клиента часто необходимо подготовить несколько экземпляров и настроить, чтобы использовать их вместе, чтобы создать крупномасштабное развертывание. Например сегментирование требуется поддерживать несколько экземпляров.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: e284a0492774e02cab79db6d9006c1718a7fcfc9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809227"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Как масштабировать службы SignalR с несколькими экземплярами?
Последний пакет SDK службы SignalR поддерживает несколько конечных точек для экземпляров служб SignalR. Можно использовать эту функцию для масштабирования параллельных соединений, или использовать его для обмена сообщениями между регионами.

## <a name="for-aspnet-core"></a>Для ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Как добавить несколько конечных точек из файла конфигурации?

Конфигурации с ключом `Azure:SignalR:ConnectionString` или `Azure:SignalR:ConnectionString:` строки подключения к службе SignalR.

Если ключ начинается с `Azure:SignalR:ConnectionString:`, он должен быть в формате `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, где `Name` и `EndpointType` являются свойствами `ServiceEndpoint` объекта и доступны из кода.

Можно добавить несколько строк соединения экземпляра, используя следующие `dotnet` команды:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Как добавить несколько конечных точек из кода?

Объект `ServicEndpoint` класс введен для описания свойств конечной точки служба Azure SignalR.
Можно настроить несколько конечных точек экземпляра при использовании пакета SDK службы Azure SignalR с помощью:
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

### <a name="how-to-customize-endpoint-router"></a>Как настроить маршрутизатор конечной точки?

По умолчанию в пакете SDK используется [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) овладеть конечных точек.

#### <a name="default-behavior"></a>Поведение по умолчанию 
1. Маршрутизация запросов клиента

    Когда клиент `/negotiate` с сервера приложений. По умолчанию пакет SDK **случайным образом выбирает** одну конечную точку из набора доступных конечных точек служб.

2. Сервер маршрутизации сообщений

    Когда * Отправка сообщения с определенным ** подключения *** и целевое подключение направляется на текущем сервере, сообщение отправляется напрямую, связанных конечных точек. В противном случае сообщения являются широковещательная рассылка на каждой конечной точки Azure SignalR.

#### <a name="customize-routing-algorithm"></a>Настраивать алгоритм маршрутизации
Можно создать собственные маршрутизатора при наличии специальных знаний для определения, какие конечные точки, должно передаваться сообщения.

В качестве примера ниже определены пользовательских маршрутизаторе при группы, начиная с `east-` всегда перейдите к конечной точке с именем `east`:

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

В приведенном ниже, который переопределяет значение по умолчанию negotiate поведение, для выбора конечных точек зависит от того, на котором размещен сервер приложений.

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

Не забудьте зарегистрировать маршрутизатора, с помощью контейнера внедрения Зависимостей:

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

### <a name="how-to-add-multiple-endpoints-from-config"></a>Как добавить несколько конечных точек из файла конфигурации?

Конфигурации с ключом `Azure:SignalR:ConnectionString` или `Azure:SignalR:ConnectionString:` строки подключения к службе SignalR.

Если ключ начинается с `Azure:SignalR:ConnectionString:`, он должен быть в формате `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, где `Name` и `EndpointType` являются свойствами `ServiceEndpoint` объекта и доступны из кода.

Можно добавить несколько строк подключения экземпляра к `web.config`:

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

Объект `ServicEndpoint` класс введен для описания свойств конечной точки служба Azure SignalR.
Можно настроить несколько конечных точек экземпляра при использовании пакета SDK службы Azure SignalR с помощью:

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

Единственное различие между ASP.NET SignalR и ASP.NET Core SignalR — это тип контекста http для `GetNegotiateEndpoint`. Для ASP.NET SignalR, он имеет [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) типа.

Ниже приведен настраиваемый согласовывать примере ASP.NET SignalR.

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

Не забудьте зарегистрировать маршрутизатора, с помощью контейнера внедрения Зависимостей:

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

## <a name="configuration-in-cross-region-scenarios"></a>Конфигурации в сценариях между регионами

`ServiceEndpoint` Объект имеет `EndpointType` свойство со значением `primary` или `secondary`.

`primary` конечные точки — это предпочтительный конечные точки для приема трафика клиента и считается более надежные сетевые подключения; `secondary` конечные точки считается менее надежные сетевые подключения и используются только для ведения сервера для клиентского трафика, например, широковещательная рассылка сообщений, но не для ведения клиенту трафика сервера.

В случае между регионами сеть может быть нестабильным. Для одного приложения сервера, расположенного в *восточной части США*, конечную точку службы SignalR, расположенной в той же *восточной части США* регион может быть настроен в качестве `primary` и конечных точек в других регионах с пометкой `secondary`. В этой конфигурации конечных точек службы в других регионах можно **получать** сообщений из этого *восточной части США* сервера приложений, но будет не **между регионами** направляются клиентов Этот сервер приложения. Архитектура показана на следующей схеме:

![Кросс-инфраструктуры географического](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Когда клиент пытается `/negotiate` с сервером приложений, по умолчанию маршрутизатором, пакет SDK **случайным образом выбирает** одну конечную точку из набора доступных `primary` конечных точек. Если конечная точка доступна, затем SDK **случайным образом выбирает** из всех доступных `secondary` конечных точек. Конечная точка помечается как **доступных** соединение между сервером и конечной точки службы при активном состоянии.

В сценарии между регионами, когда клиент пытается `/negotiate` с сервера приложений, размещенных в *восточной части США*, по умолчанию он всегда возвращает `primary` конечной точкой, расположенной в одном регионе. Когда все *восточной части США* недоступны конечные точки, он перенаправляется к конечным точкам в других регионах. Раздел отработки отказа ниже описан сценарий подробно.

![Согласование норм.](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Отработки отказа

Когда все `primary` конечные точки могут быть недоступны, клиента `/negotiate` выбирает из доступных `secondary` конечных точек. Этот механизм отработки отказа требует, что каждая конечная точка должна использоваться в качестве `primary` конечной точки к серверу по крайней мере одно приложение.

![Отработки отказа](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали о том, как настроить несколько экземпляров одного приложения для сегментирования, масштабирование и сценарии между регионами.

Поддерживает несколько конечных точек также могут использоваться в сценариях высокого уровня доступности и аварийного восстановления.

> [!div class="nextstepaction"]
> [Служба установки SignalR для высокого уровня доступности и аварийного восстановления](./signalr-concept-disaster-recovery.md)
