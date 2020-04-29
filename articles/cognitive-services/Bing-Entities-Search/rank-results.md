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
ms.openlocfilehash: 110cef117683b20170649a231226c8193496edf3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "68423916"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Использование ранжирования для отображения результатов поиска сущностей  

Каждый ответ поиска сущностей содержит объект [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse), который описывает требуемый формат отображения результатов поиска API для поиска сущностей Bing. Ответ ранжирования объединяет результаты в содержимое для заголовка, основного поля и боковой панели. Результат для заголовка считается самым важным или самым значимым, и его необходимо отображать первым. Если вы не используете для отображения результатов стандартный формат основного поля и боковой панели, нужно по меньшей мере обеспечить более высокую заметность для содержимого основного поля. 
  
В каждой группе есть массив [Items](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items), который определяет порядок отображения содержимого. Каждый элемент предоставляет два метода для идентификации результатов.  
 

|Поле | Описание  |
|---------|---------|
|`answerType` и `resultIndex` | Поле `answerType` идентифицирует тип ответа (Entity или Place), а поле `resultIndex` идентифицирует результат в этом ответе (например, обнаруженную сущность). Индекс начинается с 0.|
|`value`    | Поле `value` содержит идентификатор, который обозначает некоторый ответ или результат в этом ответе. Идентификатор может встречаться только в ответе или только в результате. |
  
Процесс использования `answerType` и `resultIndex` включает два шага. Сначала с помощью `answerType` можно найти ответ, который содержит результаты для отображения. Затем с помощью `resultIndex` можно определить порядок результатов в этом ответе и создать отображаемые результаты. ( `answerType` Значение является именем поля в объекте [сеарчреспонсе](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) .) Если предполагается отображать все результаты ответа, элемент ранжирования не включает в `resultIndex` себя поле.

Использование идентификатора требует согласования между идентификатором ранжирования и идентификатором определенного ответа или результата. Если объект ответа содержит поле `id`, при отображении объедините все результаты из этого ответа. Например, если объект `Entities` содержит поле `id`, отобразите вместе все содержащиеся в нем статьи. Если объект `Entities` не содержит поле `id`, тогда поле `id` должно включаться во все объекты, и ответ ранжирования будет описывать порядок их расположения по результатам Places.  
  
## <a name="ranking-response-example"></a>Пример ответа ранжирования

Ниже приведен пример элемента [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
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

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](tutorial-bing-entities-search-single-page-app.md)
