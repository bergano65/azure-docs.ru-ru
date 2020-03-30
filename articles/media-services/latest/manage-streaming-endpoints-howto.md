---
title: Управление конечными точками потоковой передачи с помощью Службы мультимедиа Azure v3
description: В этой статье показано, как управлять конечными точками потоковой передачи с помощью Azure Media Services v3.
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
ms.openlocfilehash: 75ba2ad87eabd7ff6b0625ad95ab24a8ae58dd0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461050"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Управление потоковыми конечными точками с помощью Media Services v3

При создании учетной записи Media Services в вашей учетной записи в состоянии **остановленного** состояния добавляется [конечная точка потоковой передачи](streaming-endpoint-concept.md) **по умолчанию.** Чтобы начать потоковую передачу содержимого и воспользоваться [преимуществами динамической упаковки](dynamic-packaging-overview.md) и [динамического шифрования,](content-protection-overview.md)потоковая точка, из которой вы хотите передавать контент, должна находиться в состоянии **запуска.**

В этой статье показано, как выполнить [стартовую](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) команду на вашей конечной точке потоковой передачи с использованием различных технологий. 
 
> [!NOTE]
> Плата взимается, только когда конечная точка потоковой передачи используется.
    
## <a name="prerequisites"></a>Предварительные требования

Отзыв: 

* [Основные понятия служб мультимедиа Azure](concepts-overview.md)
* [Концепция потоковой передачи конечных точек](streaming-endpoint-concept.md)
* [Динамическая упаковка](dynamic-packaging-overview.md)

## <a name="use-rest"></a>Использование REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Дополнительные сведения см. в разделе: 

* [Запуск справочной документации StreamingEndpoint.](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* Запуск точки стримингового является асинхронной операцией. 

    Для получения информации о том, как контролировать длительные операции, [см.](media-services-apis-overview.md)
* Эта [коллекция Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) содержит примеры нескольких операций REST, в том числе о том, как запустить конечную точку потоковой передачи.

## <a name="use-the-azure-portal"></a>Использование портала Azure 
 
1. Войдите на [портал Azure](https://portal.azure.com/).
1. Перейдите на свою учетную запись Медиа Службы Azure.
1. В левой панели выберите **потоковые конечные точки.**
1. Выберите конечную точку потоковой передачи, которая требуется для запуска, а затем выберите **Start.**

## <a name="use-the-azure-cli"></a>Использование Azure CLI

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Для получения дополнительной [az ams streaming-endpoint start](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start)информации см.

## <a name="use-sdks"></a>Использование пакетов SDK

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Смотрите полный [образец кода Java](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Смотрите полный [образец кода .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Дальнейшие действия

* [Медиа Услуги v3 OpenAPI Спецификация (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Потоковые операции конечных точек](https://docs.microsoft.com/rest/api/media/streamingendpoints)
