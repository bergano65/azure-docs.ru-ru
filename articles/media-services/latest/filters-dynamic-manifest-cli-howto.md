---
title: Использование CLI для создания фильтров с помощью служб мультимедиа Azure | Документация Майкрософт
description: В этом разделе показано, как использовать CLI для создания фильтров с помощью служб мультимедиа.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c7415bfeadc978fe7b3b6a03265c0643b129afbf
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000170"
---
# <a name="creating-filters-with-cli"></a>Создание фильтров с помощью CLI 

При доставке содержимого пользователям (потоковой трансляции мероприятий или видео по запросу) клиенту может потребоваться больше возможностей, чем описано в файле манифеста ресурса по умолчанию. Службы мультимедиа Azure позволяют определять фильтры учетной записи и фильтры ресурсов для содержимого. Дополнительные сведения см. в разделе [фильтры](filters-concept.md) и [динамические манифесты](filters-dynamic-manifest-overview.md).

В этой статье показано, как настроить фильтр для ресурса-контейнера видео по запросу и использовать CLI для Служб мультимедиа версии 3, чтобы создать [фильтры учетных записей](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) и [фильтры ресурсов](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Не забудьте проверить [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Технические условия 

- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). Обязательно запомните имя группы ресурсов и имя учетной записи Служб мультимедиа. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Определение фильтра 

В следующем примере определяются условия выбора дорожки, которые добавляются в манифест. Этот фильтр включает любые аудиодорожки в формате EC3 и любые видеодорожки со скоростью в диапазоне 0–1 000 000.

> [!TIP]
> Если вы планируете определить **фильтры** в REST, обратите внимание на то, что вам потребуется включить объект JSON-оболочку «Свойства».  

```json
[
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Audio",
                "operation": "Equal"
            },
            {
                "property": "FourCC",
                "value": "EC-3",
                "operation": "NotEqual"
            }
        ]
    },
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Video",
                "operation": "Equal"
            },
            {
                "property": "Bitrate",
                "value": "0-1000000",
                "operation": "Equal"
            }
        ]
    }
]
```

## <a name="create-account-filters"></a>Создание фильтров учетной записи

Следующая команда [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) создает фильтр учетной записи с выбранными дорожками для отслеживания, которые были [определены ранее](#define-a-filter). 

Команде можно передать необязательный параметр `--tracks`, который содержит код JSON, представляющий выбранные элементы для отслеживания.  Чтобы загрузить JSON из файла, используйте синтаксис @{файл}. Если вы используете Azure CLI локально, укажите путь к файлу полностью.

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Также см. [примеры JSON для фильтров](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Создание фильтров ресурсов

Следующая команда [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) создает фильтр ресурса с выбранными дорожками для отслеживания, которые были [определены ранее](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Также см. [примеры JSON для фильтров](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).


## <a name="associate-filters-with-streaming-locator"></a>Связать фильтры с указатель потоковой передачи

Можно указать список фильтров активов или учетной записи, которые относятся к вашей указатель потоковой передачи. [Для работы динамического упаковщика (конечная точка потоковой передачи)](dynamic-packaging-overview.md) применяет этот список фильтров с соответствующими клиент указывает в URL-адрес. Создает это сочетание [динамического манифеста](filters-dynamic-manifest-overview.md), основанная на фильтры в URL-адрес + фильтры, укажите на указатель потоковой передачи. Мы рекомендуем использовать эту функцию, если вы хотите применить фильтры, но не требуется предоставлять имена фильтров в URL-адрес.

В следующем коде CLI показано, как создать указатель потоковой передачи и укажите `filters`. Это необязательное свойство, которое принимает разделенный пробелами список имен фильтров активов и/или фильтр имен учетных записей.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Stream с помощью фильтров

После определения фильтров клиенты могут использовать их в URL-адресах потоковой передачи. Фильтры можно применять к протоколам потоковой передачи с переменной скоростью: Apple HTTP Live Streaming (HLS), MPEG-DASH и Smooth Streaming.

В представленной ниже таблице приводятся некоторые примеры URL-адресов с фильтрами.

|Протокол|Пример|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Дальнейшие действия

[Потоковая передача видео](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>См. также

[Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
