---
title: Использование ранжирования для отображения ответов (Поиск сущностей Bing)
titleSuffix: Azure Cognitive Services
description: Сведения об использовании ранжирования для отображения ответов, возвращаемых API Bing для поиска сущностей.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b63c2e53f34ac86f8ddf5ad300c2465ee2d9f032
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365641"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Использование ранжирования для отображения результатов поиска сущностей  

> [!WARNING]
> API-интерфейсы поиска Bing перемещаются из Cognitive Services в Поиск Bing службы. Начиная с **30 октября 2020** , все новые экземпляры Поиск Bing должны быть подготовлены, следуя описанному [здесь](/bing/search-apis/bing-web-search/create-bing-search-service-resource)процессу.
> API-интерфейсы поиска Bing, подготовленные с помощью Cognitive Services, будут поддерживаться в течение следующих трех лет или до конца Соглашение Enterprise, в зависимости от того, что происходит раньше.
> Инструкции по миграции см. в разделе [Поиск Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Каждый ответ поиска сущностей содержит объект [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse), который описывает требуемый формат отображения результатов поиска API для поиска сущностей Bing. Ответ ранжирования объединяет результаты в содержимое для заголовка, основного поля и боковой панели. Результат для заголовка считается самым важным или самым значимым, и его необходимо отображать первым. Если вы не используете для отображения результатов стандартный формат основного поля и боковой панели, нужно по меньшей мере обеспечить более высокую заметность для содержимого основного поля. 
  
В каждой группе есть массив [Items](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items), который определяет порядок отображения содержимого. Каждый элемент предоставляет два метода для идентификации результатов.  
 

|Поле | Description  |
|---------|---------|
|`answerType` и `resultIndex` | Поле `answerType` идентифицирует тип ответа (Entity или Place), а поле `resultIndex` идентифицирует результат в этом ответе (например, обнаруженную сущность). Индекс начинается с 0.|
|`value`    | Поле `value` содержит идентификатор, который обозначает некоторый ответ или результат в этом ответе. Идентификатор может встречаться только в ответе или только в результате. |
  
Процесс использования `answerType` и `resultIndex` включает два шага. Сначала с помощью `answerType` можно найти ответ, который содержит результаты для отображения. Затем с помощью `resultIndex` можно определить порядок результатов в этом ответе и создать отображаемые результаты. ( `answerType` Значение является именем поля в объекте [сеарчреспонсе](/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) .) Если предполагается отображать все результаты ответа, элемент ранжирования не включает в себя `resultIndex` поле.

Использование идентификатора требует согласования между идентификатором ранжирования и идентификатором определенного ответа или результата. Если объект ответа содержит поле `id`, при отображении объедините все результаты из этого ответа. Например, если объект `Entities` содержит поле `id`, отобразите вместе все содержащиеся в нем статьи. Если объект `Entities` не содержит поле `id`, тогда поле `id` должно включаться во все объекты, и ответ ранжирования будет описывать порядок их расположения по результатам Places.  
  
## <a name="ranking-response-example"></a>Пример ответа ранжирования

Ниже приведен пример элемента [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Боковая панель с учетом ответа ранжирования отображает две сущности из результатов, имеющие отношение к Джимми Хендриксу.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](tutorial-bing-entities-search-single-page-app.md)