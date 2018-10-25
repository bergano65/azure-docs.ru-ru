---
title: Конечная точка API Bing для поиска сущностей
titlesuffix: Azure Cognitive Services
description: Сводные сведения о конечной точке API для поиска сущностей.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: d781a4b3cd0119f5624b4dd20b514894ea339414
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816588"
---
# <a name="entity-search-endpoint"></a>Конечная точка для поиска сущностей
**API для поиска сущностей** включает одну конечную точку, которая возвращает сущности из Интернета на основе запроса.

## <a name="endpoint"></a>Конечная точка
Чтобы получить результаты поиска сущностей с помощью **API Bing**, отправьте запрос `GET` на следующую конечную точку. Для определения дополнительных спецификаций используйте заголовки и параметры URL-адреса.

**Конечная точка** возвращает результаты поиска сущностей, соответствующие поисковому запросу пользователя, который определен с помощью `?q=""`.
```
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Дополнительные сведения о заголовках, параметрах, кодах рынков, объектах ответов, ошибках и т. п. вы найдете в справочнике [по API Bing для поиска сущностей версии 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference).

## <a name="response-json"></a>Ответ в формате JSON
Ответ на запрос на поиск сущностей содержит результаты в виде объектов JSON. Примеры результатов см. в [этой статье](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start).

## <a name="next-steps"></a>Дополнительная информация
Интерфейсы API **Bing** поддерживают действия поиска, которые возвращают результаты определенного типа. Все конечные точки поиска возвращают результаты в виде объектов ответа JSON.  Все конечные точки поддерживают запросы, которые возвращают результаты с учетом языка и (или) местоположения по значениям долготы, широты и радиуса поиска.

Полные сведения о параметрах, поддерживаемых каждой конечной точкой, приведены в справочной документации по каждому типу.
Примеры использования API для поиска сущностей см. в статьях [Создание первого запроса для поиска сущностей](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start) и [Изменение размера и обрезка эскизов изображений](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/resize-and-crop-thumbnails).