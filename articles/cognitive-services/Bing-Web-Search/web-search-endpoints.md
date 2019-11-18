---
title: Конечная точка для веб-поиска
titleSuffix: Azure Cognitive Services
description: Чтобы получить результаты поиска в Интернете, отправьте запрос `GET` в следующую конечную точку. Заголовки и параметры URL-адреса определяют дополнительные спецификации.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 2edc6a4cf4d4fb174c1b2067d8b3fb3b6f31859f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111335"
---
# <a name="web-search-endpoint"></a>Конечная точка для поиска в Интернете

**API для поиска в Интернете** возвращает веб-страницы, новости, изображения, видео и [сущности](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Сущности содержат сводные данные о человеке, месте или теме.

## <a name="endpoint"></a>Конечная точка

Чтобы получить результаты поиска в Интернете с помощью API Bing, отправьте запрос `GET` на следующую конечную точку. Заголовки и параметры URL-адреса определяют дополнительные спецификации.

**Конечная точка** возвращает результаты поиска в Интернете, соответствующие поисковому запросу пользователя, который определен с помощью `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Конечная точка: дополнительные сведения о заголовках, параметрах, кодах рынков, объектах ответов, ошибках и т. п. вы найдете в справочнике [по API Bing для поиска в Интернете версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

## <a name="response-json"></a>Ответ JSON

Ответ на запрос на поиск в Интернете содержит результаты в виде объектов JSON. Для анализа результатов требуются процедуры обработки элементов каждого типа. Чтобы ознакомиться с примерами, изучите это [руководство](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) и [исходный код](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search).

## <a name="next-steps"></a>Дополнительная информация

API-интерфейсы **Bing** поддерживают действия поиска, которые возвращают результаты определенного типа. Все конечные точки поиска возвращают результаты в виде объектов ответа JSON.  Все конечные точки поддерживают запросы, возвращающие конкретный язык и расположение по долготе, широте и поиску RADIUS.

Полные сведения о параметрах, поддерживаемых каждой конечной точкой, см. в справочной документации по каждому типу.
Примеры простых запросов к API поиска в Интернете приведены в [кратких руководствах по поиску в Интернете](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
