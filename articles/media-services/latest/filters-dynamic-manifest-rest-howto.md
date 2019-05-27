---
title: Создание фильтров с помощью REST API служб мультимедиа | Документация Майкрософт
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
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 69399513291a47f7109003e825052314f447125a
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002327"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Создание фильтров с помощью REST API Служб мультимедиа

При распространении содержимого пользователям (потоковой трансляции событий или видео по запросу) клиенту может потребоваться больше возможностей, чем определено в файле манифеста ресурса-контейнера по умолчанию. Службы мультимедиа Azure позволяют определять фильтры учетной записи и фильтры ресурсов для содержимого. Дополнительные сведения см. в разделе [фильтры](filters-concept.md) и [динамические манифесты](filters-dynamic-manifest-overview.md).

В этой статье показано, как определить фильтр для ресурса-контейнера видео по запросу и использовать REST API, чтобы создать [фильтры учетных записей](https://docs.microsoft.com/rest/api/media/accountfilters) и [фильтры ресурсов-контейнеров](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Не забудьте проверить [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Технические условия 

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

В загруженной ранее коллекции Postman последовательно выберите **Account Filters (Фильтры учетной записи)**->**Create or update an Account Filter (Создать или обновить фильтр учетной записи)**.

Метод HTTP-запроса **PUT** аналогичен следующему:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Выберите вкладку **Текст** и вставьте код json, который был [определен ранее](#define-a-filter).

Нажмите кнопку **Отправить**. 

Фильтр создан.

Дополнительные сведения см. в статье о [создании или обновлении фильтров учетной записи](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Также см. [примеры JSON для фильтров учетной записи](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Создание фильтров ресурсов  

В загруженной ранее коллекции Postman "Службы мультимедиа версии 3" выберите **Ресурсы-контейнеры**->**Создать или обновить фильтр ресурса-контейнера**.

Метод HTTP-запроса **PUT** аналогичен следующему:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Выберите вкладку **Текст** и вставьте код json, который был [определен ранее](#define-a-filter).

Нажмите кнопку **Отправить**. 

Фильтр ресурса-контейнера создан.

Дополнительные сведения см. в статье о [создании или обновлении фильтров ресурсов-контейнеров](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Также см. [примеры JSON для фильтров](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter). 

## <a name="next-steps"></a>Дальнейшие действия

[Потоковая передача видео](stream-files-tutorial-with-rest.md) 
