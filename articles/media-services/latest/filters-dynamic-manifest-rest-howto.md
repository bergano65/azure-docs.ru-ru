---
title: Создание фильтров с помощью служб мультимедиа Azure v3 REST API
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: f9134dd3bc926e6e2f454e5187e03365e91ed22a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75780340"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Создание фильтров с помощью REST API Служб мультимедиа

При распространении содержимого пользователям (потоковой трансляции событий или видео по запросу) клиенту может потребоваться больше возможностей, чем определено в файле манифеста ресурса-контейнера по умолчанию. Службы мультимедиа Azure позволяют определять фильтры учетной записи и фильтры ресурсов для содержимого. 

Подробное описание этой функции и сценариев, в которых она используется, см. в разделе [динамические манифесты](filters-dynamic-manifest-overview.md) и [фильтры](filters-concept.md).

В этой статье показано, как определить фильтр для ресурса-контейнера видео по запросу и использовать REST API, чтобы создать [фильтры учетных записей](https://docs.microsoft.com/rest/api/media/accountfilters) и [фильтры ресурсов-контейнеров](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Обязательно ознакомьтесь с [пресентатионтимеранже](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Предварительные требования 

Чтобы выполнить действия, описанные в этом разделе, необходимо сделать следующее:

- См. дополнительные сведения о [фильтрах и динамических манифестах](filters-dynamic-manifest-overview.md).
- [Настройте Postman для вызовов REST API Служб мультимедиа Azure](media-rest-apis-with-postman.md).

    Не забудьте выполнить последний шаг, указанный в разделе [Получение токена Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Определение фильтра  

Ниже приведен пример **текста запроса**, определяющего условия выбора дорожки, которые добавляются в манифест. Этот фильтр включает любые аудиодорожки в формате EC3 и любые видеодорожки со скоростью в диапазоне 0–1 000 000.

```json
{
    "properties": {
        "tracks": [
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
                        "operation": "Equal"
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
     }
}
```

## <a name="create-account-filters"></a>Создание фильтров учетной записи

В скачанной коллекции POST щелкните-> **фильтры учетной записи****создать или обновить фильтр учетной записи**.

Метод HTTP-запроса **PUT** аналогичен следующему:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Выберите вкладку **Текст** и вставьте код json, который был [определен ранее](#define-a-filter).

Выберите **Отправить**. 

Фильтр создан.

Дополнительные сведения см. в статье о [создании или обновлении фильтров учетной записи](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Также см. [примеры JSON для фильтров](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Создание фильтров ресурсов  

В скачанной коллекции после загрузки служб мультимедиа v3 выберите **активы**->**создать или обновить фильтр активов**.

Метод HTTP-запроса **PUT** аналогичен следующему:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Выберите вкладку **Текст** и вставьте код json, который был [определен ранее](#define-a-filter).

Выберите **Отправить**. 

Фильтр ресурса-контейнера создан.

Дополнительные сведения см. в статье о [создании или обновлении фильтров ресурсов-контейнеров](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Также см. [примеры JSON для фильтров](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Связывание фильтров с указателем потоковой передачи

Можно указать список фильтров активов или учетных записей, которые будут применяться к указателю потоковой передачи. [Динамический упаковщик (конечная точка потоковой передачи)](dynamic-packaging-overview.md) применяет этот список фильтров вместе с тем, что ваш клиент указывает в URL-адресе. Это сочетание создает [динамический манифест](filters-dynamic-manifest-overview.md), основанный на фильтрах в URL-адресах и фильтрах, указанных при указателе потоковой передачи. Рекомендуется использовать эту функцию, если вы хотите применить фильтры, но не хотите предоставлять имена фильтров в URL-адресе.

Чтобы создать и связать фильтры с указателем потоковой передачи с помощью функции RESTFUL, используйте API [указателей Streaming-Create](https://docs.microsoft.com/rest/api/media/streaminglocators/create) и укажите `properties.filters` в [тексте запроса](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Поток с использованием фильтров

После определения фильтров клиенты могут использовать их в URL-адресах потоковой передачи. Фильтры можно применять к протоколам потоковой передачи с переменной скоростью: Apple HTTP Live Streaming (HLS), MPEG-DASH и Smooth Streaming.

В представленной ниже таблице приводятся некоторые примеры URL-адресов с фильтрами.

|Протокол|Пример|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Дальнейшие действия

[Потоковая передача видео](stream-files-tutorial-with-rest.md) 
