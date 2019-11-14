---
title: Конечные точки для API Bing для поиска изображений
titleSuffix: Azure Cognitive Services
description: Поиск изображений API содержит три конечных точки. Конечная точка 1 возвращает изображения из Интернета. Конечная точка 2 Возвращает Имажеинсигхтс. Конечная точка 3 возвращает популярные изображения.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 38416f6a580d270aefc287de0c198bd418a44db9
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072631"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Конечные точки для API Bing для поиска изображений

**API для поиска изображений** включает три конечные точки.  Конечная точка 1 возвращает изображения из Интернета на основе запроса. Конечная точка 2 возвращает [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) (аналитические сведения об изображениях).  Конечная точка 3 возвращает популярные изображения.

## <a name="endpoints"></a>конечные точки;

Чтобы получить результаты поиска изображений с помощью API Bing, отправьте запрос на одну из конечных точек, приведенных ниже. Используйте заголовки и параметры URL-адреса для определения других спецификаций.

**Конечная точка 1** возвращает результаты поиска изображений, соответствующие поисковому запросу пользователя, который определен с помощью `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Конечная точка 2** возвращает аналитические сведения об изображении, используя `GET` или `POST`.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Запрос GET возвращает аналитические сведения об изображении, например веб-страницы, содержащие данное изображение. Включите в запрос [ параметр ](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)insightsToken`GET`.

Вы также можете включить в текст запроса `POST` двоичный файл изображения и задать для параметра [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) значение `RecognizedEntities`. При этом будет возвращено значение [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken), используемое в качестве параметра в следующем запросе `GET`, который возвращает сведения о людях на данном изображении.  Задайте для параметра `modules` значение `All`, чтобы получить в результатах `RecognizedEntities` все аналитические сведения, кроме `POST`. При этом не потребуется выполнять второй вызов, используя параметр `insightsToken`.


**Конечная точка 3** возвращает изображения, которые набирают популярность. За основу берутся результаты поисковых запросов, выполненных другими пользователями. Изображения разделяются на различные категории, например, если в них фигурируют примечательные люди или события.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Список рынков, для которых поддерживается поиск популярных изображений, см. в статье [Получение изображений, набирающих популярность](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Дополнительные сведения о заголовках, параметрах, кодах рынков, объектах ответов, ошибках и т. п. вы найдете в справочнике [по API Bing для поиска изображений версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
## <a name="response-json"></a>Ответ JSON
Ответ на запрос на поиск изображений содержит результаты в виде объектов JSON. Примеры синтаксического анализа результатов см. в этом [руководстве](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) и [исходном коде](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Дополнительная информация
API-интерфейсы **Bing** поддерживают действия поиска, которые возвращают результаты определенного типа. Все конечные точки поиска возвращают результаты в виде объектов ответа JSON.  Все конечные точки поддерживают запросы, возвращающие конкретный язык и (или) расположение по долготе, широте и поиску RADIUS.

Полные сведения о параметрах, поддерживаемых каждой конечной точкой, см. в справочной документации по каждому типу.
Простые примеры запросов к API для поиска изображений см. в [кратких руководствах по поиску изображений](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
