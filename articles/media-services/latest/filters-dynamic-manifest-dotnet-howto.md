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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 3517a9c0aabf9e8ec029405f14461626d32335a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322742"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Создание фильтров с помощью пакета SDK Служб мультимедиа для .NET

При распространении содержимого пользователям (потоковой трансляции событий или видео по запросу) клиенту может потребоваться больше возможностей, чем определено в файле манифеста ресурса-контейнера по умолчанию. Службы мультимедиа Azure позволяют определять фильтры учетной записи и фильтры ресурсов для содержимого. Чтобы узнать больше, ознакомьтесь со [Фильтры и динамические манифесты](filters-dynamic-manifest-overview.md).

В этой статье показано, как использовать пакет SDK Служб мультимедиа для .NET, чтобы определить фильтр для ресурса-контейнера видео по запросу и создать [фильтры учетных записей](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) и [фильтры ресурсов-контейнеров](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

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

## <a name="next-steps"></a>Дальнейшие действия

[Потоковая передача видео](stream-files-tutorial-with-api.md) 


