---
title: Указатели потоковой передачи в Службах мультимедиа Azure | Документация Майкрософт
description: В этой статье приведены общие сведения об указателях потоковой передачи и их использовании в Службах мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/26/2019
ms.author: juliako
ms.openlocfilehash: 5897b7df2460257784c40eb974c473573ec4003d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299164"
---
# <a name="streaming-locators"></a>Указатели потоковой передачи

Чтобы видео в выходном ресурсе стали доступны для воспроизведения в клиентах, необходимо создать [указатель потоковой передачи](https://docs.microsoft.com/rest/api/media/streaminglocators), а затем сформировать URL-адреса потоковой передачи. Чтобы создать URL-адрес, необходимо объединить имя узла конечной точки потоковой передачи и указатель потоковой передачи путь. Пример .NET приведен в разделе [Создание указателя потоковой передачи](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Процесс создания **указателя потоковой передачи** называется публикацией. По умолчанию **указатель потоковой передачи** допустим сразу после выполнения вызова API и действует, пока не будет удален, если не настроить дополнительное начальное и конечное время. 

При создании **указатель потоковой передачи**, необходимо указать **активов** имя и **потоковой передачи политики** имя. Дополнительные сведения см. в следующих разделах:

* [Ресурсы](assets-concept.md)
* [Политики потоковой передачи](streaming-policy-concept.md)
* [Политики ключа содержимого](content-key-policy-concept.md)

Можно также указать время начала и окончания на вашей указатель потоковой передачи, который позволяет пользователю воспроизвести содержимое в указанное время (например, между 5/1/2019 для 5/5/2019 г.).  

## <a name="considerations"></a>Рекомендации

* **Указатели потоковой передачи** не являются обновляемыми. 
* Свойства **указателей потоковой передачи** типа Datetime всегда задаются в формате UTC.
* Следует разработать ограниченный набор политик для учетной записи Служб мультимедиа и повторно использовать их для указателей потоковой передачи каждый раз, когда требуются те же параметры. Дополнительные сведения см. в статье [Квоты и ограничения](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Создавать указатели потоковой передачи  

### <a name="not-encrypted"></a>Не зашифровано

Если нужно передавать потоком вашего файла в-clear (без шифрования), набор предопределенных четкую политику потоковой передачи: для «Predefined_ClearStreamingOnly» (в .NET, можно использовать перечисления PredefinedStreamingPolicy.ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>зашифрованные; 

Если требуется шифровать содержимое с шифрованием CENC, следует применять политику для «Predefined_MultiDrmCencStreaming». Шифрование Widevine будет назначен для потока DASH и PlayReady Smooth. Ключ будет доставлена клиенту воспроизведения, в зависимости от настроенных лицензий DRM.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Если вы хотите, чтобы шифровать поток HLS, используя CBCS (FairPlay), используйте «Predefined_MultiDrmStreaming».

## <a name="associate-filters-with-streaming-locators"></a>Связать фильтры с указатели потоковой передачи

См. в разделе [фильтры: связан с указатели потоковой передачи](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Фильтр, в порядке, страницу сущностей указатель потоковой передачи

Ознакомьтесь с разделом [Фильтрация, упорядочивание и разбиение по страницам сущностей Служб мультимедиа](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Указатели потоковой передачи списка по имени ресурса

Чтобы получить указатели потоковой передачи, на основе имени связанного ресурса, используйте следующие операции:

|Язык|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|Интерфейс командной строки|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[listStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/assets?view=azure-node-latest#liststreaminglocators-string--string--string--object-)|

## <a name="also-see"></a>См. также:

* [Ресурсы](assets-concept.md)
* [Политики потоковой передачи](streaming-policy-concept.md)
* [Политики ключа содержимого](content-key-policy-concept.md)

## <a name="next-steps"></a>Дальнейшие действия

[Учебник. Отправка, кодирование и потоковая передача видео с помощью .NET](stream-files-tutorial-with-api.md)
