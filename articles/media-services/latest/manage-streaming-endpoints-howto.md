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
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: d8b5f79d9c9050eeb7957ddcf28021e3c8b3dd94
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89010653"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Управление конечными точками потоковой передачи с помощью служб мультимедиа v3

При создании учетной записи служб мультимедиа [Конечная точка потоковой передачи](streaming-endpoint-concept.md) **по умолчанию** добавляется в учетную запись в **остановленном** состоянии. Чтобы начать потоковую передачу содержимого и воспользоваться [динамической упаковкой](dynamic-packaging-overview.md) и [динамическим шифрованием](content-protection-overview.md), конечная точка потоковой передачи, из которой требуется потоковая передача содержимого, должна находиться в состоянии **выполняется** .

В этой статье показано, как выполнить команду [запуска](/rest/api/media/streamingendpoints/start) в конечной точке потоковой передачи с помощью различных технологий. 
 
> [!NOTE]
> Плата взимается, только когда конечная точка потоковой передачи используется.
    
## <a name="prerequisites"></a>Обязательные условия

Обзор. 

* [Основные понятия служб мультимедиа Azure](concepts-overview.md)
* [Концепция конечной точки потоковой передачи](streaming-endpoint-concept.md)
* [Динамическая упаковка](dynamic-packaging-overview.md)

## <a name="use-rest"></a>Использование REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Дополнительные сведения см. в разделе: 

* Справочная документация по [запуску StreamingEndpoint](/rest/api/media/streamingendpoints/start) .
* Запуск конечной точки потоковой передачи является асинхронной операцией. 

    Сведения о мониторинге длительных операций см. в разделе [длительные операции](media-services-apis-overview.md).
* В этой [коллекции POST](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) содержатся примеры нескольких операций, включая запуск конечной точки потоковой передачи.

## <a name="use-the-azure-portal"></a>Использование портала Azure 
 
1. Войдите на [портал Azure](https://portal.azure.com/).
1. Перейдите к учетной записи служб мультимедиа Azure.
1. В левой области выберите  **конечные точки потоковой передачи**.
1. Выберите конечную точку потоковой передачи, которую необходимо запустить, и нажмите кнопку **запустить**.

## <a name="use-the-azure-cli"></a>Использование Azure CLI

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Дополнительные сведения см. в статье [AZ AMS Streaming-Endpoint Start](/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start).

## <a name="use-sdks"></a>Использование пакетов SDK

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

См. полный [пример кода Java](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

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

См. полный [пример кода .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Дальнейшие действия

* [Спецификация OpenAPI служб мультимедиа v3 (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Операции конечной точки потоковой передачи](/rest/api/media/streamingendpoints)
