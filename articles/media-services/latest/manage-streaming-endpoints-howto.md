---
title: Управление конечными точками потоковой передачи с помощью служб мультимедиа Azure v3
description: В этой статье показано, как управлять конечными точками потоковой передачи с помощью служб мультимедиа Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2020
ms.author: juliako
ms.openlocfilehash: 5dd3cc1efd25f7ec09f897c67bebebedb84d9570
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370551"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Управление конечными точками потоковой передачи с помощью служб мультимедиа v3

При создании учетной записи служб мультимедиа в нее добавляется конечная точка потоковой передачи **по умолчанию** в состоянии **Остановлена**. Чтобы начать потоковую передачу содержимого и воспользоваться [динамической упаковкой](dynamic-packaging-overview.md) и [динамическим шифрованием](content-protection-overview.md), конечная точка потоковой передачи, из которой требуется потоковая передача содержимого, должна находиться в состоянии **выполняется** .

В этой статье показано, как запустить конечную точку потоковой передачи.
 
> [!NOTE]
> Плата взимается, только когда конечная точка потоковой передачи используется.
    
## <a name="prerequisites"></a>Предварительные требования

Эксперт 

* [Основные понятия служб мультимедиа Azure](concepts-overview.md)
* [Концепция конечной точки потоковой передачи](streaming-endpoint-concept.md)
* [Динамическая упаковка](dynamic-packaging-overview.md)

## <a name="use-the-azure-portal"></a>Использование портала Azure

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Перейдите к учетной записи служб мультимедиа Azure.
1. В левой части экрана выберите **конечные точки потоковой передачи**.
1. Выберите конечную точку потоковой передачи, которую необходимо запустить, и нажмите кнопку **Пуск**.

## <a name="use-the-java-sdk"></a>Использование пакета SDK для Java

```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

См. полный [пример кода Java](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

## <a name="use-the-net-sdk"></a>Использование пакета SDK для .NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

См. полный [пример кода .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

## <a name="use-cli"></a>Использование CLI

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Дополнительные сведения см. в статье [AZ AMS Streaming-Endpoint Start](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start).

## <a name="use-rest"></a>Использование REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Дополнительные сведения см. в разделе: 

* Справочная документация по [запуску StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) .
* Запуск конечной точки потоковой передачи является асинхронной операцией. 

    Дополнительные сведения о мониторинге долго выполняющихся операций см. в статье [длительные операции](media-services-apis-overview.md) .
* Эта [коллекция POST](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) содержит примеры нескольких операций RESTful, в том числе сведения о запуске конечной точки потоковой передачи.

## <a name="next-steps"></a>Следующие шаги

* [Спецификация OpenAPI служб мультимедиа v3 (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Операции конечной точки потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingendpoints)
