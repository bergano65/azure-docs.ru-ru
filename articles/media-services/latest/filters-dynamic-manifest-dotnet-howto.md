---
title: Создание фильтров с помощью пакета SDK для .NET служб мультимедиа Azure
description: В этом разделе описывается создание фильтров, с помощью которых клиент может передавать определенные секции потока. Для достижения такой выборочной потоковой передачи службы мультимедиа создают динамические манифесты.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: 2bcb8762b94347f4409507fb89a18cb6c9d0dacd
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494300"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Создание фильтров с помощью пакета SDK Служб мультимедиа для .NET

При распространении содержимого пользователям (потоковой трансляции событий или видео по запросу) клиенту может потребоваться больше возможностей, чем определено в файле манифеста ресурса-контейнера по умолчанию. Службы мультимедиа Azure позволяют определять фильтры учетной записи и фильтры ресурсов для содержимого. 

Подробное описание этой функции и сценарии его использования см. в разделе [динамические манифесты](filters-dynamic-manifest-overview.md) и [фильтры](filters-concept.md).

В этой статье показано, как использовать пакет SDK Служб мультимедиа для .NET, чтобы определить фильтр для ресурса-контейнера видео по запросу и создать [фильтры учетных записей](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) и [фильтры ресурсов-контейнеров](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

> [!NOTE]
> Не забудьте проверить [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Технические условия 

- См. дополнительные сведения о [фильтрах и динамических манифестах](filters-dynamic-manifest-overview.md).
- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). Обязательно запомните имя группы ресурсов и имя учетной записи Служб мультимедиа. 
- Получите информацию, необходимую для [доступа к API-интерфейсам](access-api-cli-how-to.md).
- Ознакомьтесь со статьей об [отправке, кодировании и потоковой передаче видео с помощью Служб мультимедиа Azure](stream-files-tutorial-with-api.md), чтобы [начать использовать пакет SDK для .NET](stream-files-tutorial-with-api.md#start_using_dotnet).

## <a name="define-a-filter"></a>Определение фильтра  

В .NET выбранные дорожки настраиваются с помощью классов [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) и [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet). 

Приведенный ниже код определяет фильтр, который включает в себя любые звуковые дорожки с EC-3 и любые видеодорожки со скоростью в диапазоне 0–1 000 000.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Создание фильтров учетной записи

В приведенном ниже коде показано, как с помощью .NET создать фильтр учетной записи, который включает все выбранные дорожки, [определенные выше](#define-a-filter). 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Создание фильтров ресурса-контейнера

В приведенном ниже коде показано, как с помощью .NET создать фильтр ресурса-контейнера, который включает все выбранные дорожки, [определенные выше](#define-a-filter). 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Связать фильтры с указатель потоковой передачи

Можно указать список фильтров активов или учетной записи, которые относятся к вашей указатель потоковой передачи. [Для работы динамического упаковщика (конечная точка потоковой передачи)](dynamic-packaging-overview.md) применяет этот список фильтров с соответствующими клиент указывает в URL-адрес. Создает это сочетание [динамического манифеста](filters-dynamic-manifest-overview.md), основанная на фильтры в URL-адрес + фильтры, укажите на указатель потоковой передачи. Мы рекомендуем использовать эту функцию, если вы хотите применить фильтры, но не требуется предоставлять имена фильтров в URL-адрес.

Следующие C# кода показано, как создать указатель потоковой передачи и укажите `StreamingLocator.Filters`. Это необязательное свойство, которое принимает `IList<string>` фильтра имен.

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>Stream с помощью фильтров

После определения фильтров клиенты могут использовать их в URL-адресах потоковой передачи. Фильтры можно применять к протоколам потоковой передачи с переменной скоростью: Apple HTTP Live Streaming (HLS), MPEG-DASH и Smooth Streaming.

В представленной ниже таблице приводятся некоторые примеры URL-адресов с фильтрами.

|Протокол|Пример|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Дальнейшие действия

[Потоковая передача видео](stream-files-tutorial-with-api.md) 


