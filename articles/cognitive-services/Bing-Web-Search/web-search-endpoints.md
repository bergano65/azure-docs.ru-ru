---
title: Конечная точка для веб-поиска
titleSuffix: Azure Cognitive Services
description: Сводные сведения о конечной точке API для поиска в Интернете.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 11/14/2018
ms.author: v-gedod
ms.openlocfilehash: 2f7e6cd577b1eabbaabdfe87fca8ea0f036a062d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55149396"
---
# <a name="web-search-endpoint"></a>Конечная точка для поиска в Интернете

**API для поиска в Интернете** возвращает веб-страницы, новости, изображения, видео и [сущности](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Сущности содержат сводные данные о человеке, месте или теме.

## <a name="endpoint"></a>Конечная точка

Чтобы получить результаты поиска в Интернете с помощью API Bing, отправьте запрос `GET` на следующую конечную точку. Заголовки и параметры URL-адреса определяют дополнительные спецификации.

**Конечная точка**. возвращает результаты поиска в Интернете, соответствующие поисковому запросу пользователя, который определен с помощью `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Конечная точка: дополнительные сведения о заголовках, параметрах, кодах рынков, объектах ответов, ошибках и т. п. вы найдете в статье [Web Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) (Руководство по API для поиска в сети версии 7).

## <a name="response-json"></a>Ответ в формате JSON

Ответ на запрос на поиск в Интернете содержит результаты в виде объектов JSON. Для анализа результатов требуются процедуры обработки элементов каждого типа. Чтобы ознакомиться с примерами, изучите это [руководство](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) и [исходный код](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search).

## <a name="next-steps"></a>Дополнительная информация

Интерфейсы API **Bing** поддерживают действия поиска, которые возвращают результаты определенного типа. Все конечные точки поиска возвращают результаты в виде объектов ответа JSON.  Все конечные точки поддерживают запросы, которые возвращают результаты с учетом языка и местоположения по значениям долготы, широты и радиуса поиска.

Полные сведения о параметрах, поддерживаемых каждой конечной точкой, приведены в справочной документации по каждому типу.
Примеры простых запросов к API поиска в Интернете приведены в [кратких руководствах по поиску в Интернете](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
