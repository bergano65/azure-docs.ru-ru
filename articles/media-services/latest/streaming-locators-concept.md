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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80582672"
---
# <a name="streaming-locators"></a>Указатели потоковой передачи

Чтобы видео в выходном ресурсе стали доступны для воспроизведения в клиентах, необходимо создать [указатель потоковой передачи](https://docs.microsoft.com/rest/api/media/streaminglocators), а затем сформировать URL-адреса потоковой передачи. Чтобы создать URL-адрес, необходимо сцепить имя узла конечной точки потоковой передачи и путь указателя потоковой передачи. Пример .NET приведен в разделе [Создание указателя потоковой передачи](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Процесс создания **указателя потоковой передачи** называется публикацией. По умолчанию **указатель потоковой передачи** действителен сразу же после выполнения вызовов API и продолжается до тех пор, пока не будет настроено дополнительное время начала и окончания. 

При создании **указателя потоковой передачи**необходимо указать имя **ресурса** и имя **политики потоковой передачи** . Дополнительные сведения см. в следующих разделах:

* [Активы](assets-concept.md)
* [Политики потоковой передачи](streaming-policy-concept.md)
* [Политики ключей содержимого](content-key-policy-concept.md)

Можно также указать время начала и окончания для указателя потоковой передачи, что позволит пользователю воспроизводить содержимое в указанное время (например, от 5/1/2019 до 5/5/2019).  

## <a name="considerations"></a>Рекомендации

* Не удается обновить **указатели потоковой передачи** . 
* Свойства **указателей потоковой передачи** типа Datetime всегда задаются в формате UTC.
* Следует разработать ограниченный набор политик для учетной записи Служб мультимедиа и повторно использовать их для указателей потоковой передачи каждый раз, когда требуются те же параметры. Дополнительные сведения см. в разделе [квоты и ограничения](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Создание указателей потоковой передачи  

### <a name="not-encrypted"></a>Не зашифровано

Если вы хотите выполнить потоковую передачу файла в (не зашифрованном), задайте предопределенную политику очистки потоковой передачи: в "Predefined_ClearStreamingOnly" (в .NET можно использовать перечисление Предефинедстреамингполици. Клеарстреамингонли).

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

### <a name="encrypted"></a>С шифрованием 

Если необходимо зашифровать содержимое с помощью шифрования CENC, задайте для политики значение "Predefined_MultiDrmCencStreaming". Шифрование Widevine будет применяться к потоку ШТРИХа, а PlayReady — к Smooth. Ключ будет доставлен клиенту воспроизведения на основе настроенных лицензий DRM.

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

Если вы хотите также зашифровать поток HLS с помощью CBCS (FairPlay), используйте "Predefined_MultiDrmStreaming".

> [!NOTE]
> Widevine — это служба, которая предоставляется компанией Google Inc. и подпадает под условия предоставления услуг и политику конфиденциальности Google Inc.

## <a name="associate-filters-with-streaming-locators"></a>Связывание фильтров с указателями потоковой передачи

См. раздел [фильтры: связывание с указателями потоковой передачи](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Сущности «фильтр», «порядок», «указатель потоковой передачи»

Ознакомьтесь с разделом [Фильтрация, упорядочивание и разбиение по страницам сущностей Служб мультимедиа](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Перечисление указателей потоковой передачи по имени ресурса

Чтобы получить указатели потоковой передачи на основе имени связанного ресурса, используйте следующие операции:

|Язык|API|
|---|---|
|REST|[листстреаминглокаторс](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[AZ AMS Asset List-Streaming-Locator](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[листстреаминглокаторс](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[ассетстреаминглокатор](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>См. также раздел

* [Активы](assets-concept.md)
* [Политики потоковой передачи](streaming-policy-concept.md)
* [Политики ключей содержимого](content-key-policy-concept.md)
* [Учебник. Отправка, кодирование и потоковая передача видео с помощью .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Дальнейшие действия

[Создание указателя потоковой передачи и URL-адресов сборки](create-streaming-locator-build-url.md)
