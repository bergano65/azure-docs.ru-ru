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
ms.date: 03/04/2020
ms.author: juliako
ms.openlocfilehash: 41b2d0ad1e072fb2bf5860ae80f8f25f886b37f7
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582672"
---
# <a name="streaming-locators"></a>Указатели потоковой передачи

Чтобы видео в выходном ресурсе стали доступны для воспроизведения в клиентах, необходимо создать [указатель потоковой передачи](https://docs.microsoft.com/rest/api/media/streaminglocators), а затем сформировать URL-адреса потоковой передачи. Чтобы создать URL-адрес, необходимо сцепить имя узла конечной точки потоковой передачи и путь указателя потоковой передачи. Пример .NET приведен в разделе [Создание указателя потоковой передачи](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Процесс создания **потокового локатора** называется публикацией. По умолчанию **потоковый локатор** действителен сразу после выполнения вызовов API и длится до тех пор, пока он не будет удален, если только вы не наверстируете дополнительное время начала и окончания. 

При создании **потокового locator**необходимо указать имя **актива** и имя **политики потоковой передачи.** Дополнительные сведения см. в следующих разделах:

* [Активы](assets-concept.md)
* [Политики потоковой передачи](streaming-policy-concept.md)
* [Политики ключа содержимого](content-key-policy-concept.md)

Вы также можете указать время начала и окончания на вашем потоковом Locator, который позволит пользователю воспроизводить содержимое только между этими временами (например, между 5/1/2019 до 5/5/2019).  

## <a name="considerations"></a>Рекомендации

* **Потоковые локаторы** не являются updatable. 
* Свойства **указателей потоковой передачи** типа Datetime всегда задаются в формате UTC.
* Следует разработать ограниченный набор политик для учетной записи Служб мультимедиа и повторно использовать их для указателей потоковой передачи каждый раз, когда требуются те же параметры. Для получения дополнительной [информации см.](limits-quotas-constraints.md)

## <a name="create-streaming-locators"></a>Создание потоковых локаторов  

### <a name="not-encrypted"></a>Не зашифровано

Если вы хотите передавать файл в ясном (незашифрованном) установите предопределенную четкую политику потоковой передачи: к "Predefined_ClearStreamingOnly" (в .NET можно использовать PredefinedStreamingPolicy.ClearStreamingOnly enum).

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

Если вам необходимо зашифровать содержимое с помощью шифрования CENC, установите политику на "Predefined_MultiDrmCencStreaming". Шифрование Widevine будет применено к потоку DASH и PlayReady to Smooth. Ключ будет доставлен клиенту воспроизведения на основе настроенных лицензий DRM.

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

Если вы также хотите зашифровать поток HLS с помощью CBCS (FairPlay), используйте "Predefined_MultiDrmStreaming".

> [!NOTE]
> Widevine — это служба, которая предоставляется компанией Google Inc. и подпадает под условия предоставления услуг и политику конфиденциальности Google Inc.

## <a name="associate-filters-with-streaming-locators"></a>Ассоциированные фильтры с потоковыми локаторами

Смотрите [фильтры: ассоциируется с потоковыми локаторами](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Фильтр, порядок, объекты потоковой передачи страниц

Ознакомьтесь с разделом [Фильтрация, упорядочивание и разбиение по страницам сущностей Служб мультимедиа](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Список потоковых локаторов по имени активов

Чтобы получить потоковые локаторы на основе связанного имени актива, используйте следующие операции:

|Язык|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams список активов-поток-локаторы](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>См. также

* [Активы](assets-concept.md)
* [Политики потоковой передачи](streaming-policy-concept.md)
* [Политики ключа содержимого](content-key-policy-concept.md)
* [Учебник: Загружать, кодировать и передавать видео с помощью .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Следующие шаги

[Как создать потоковое локатор и создать URL-адреса](create-streaming-locator-build-url.md)
