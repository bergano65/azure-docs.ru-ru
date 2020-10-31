---
title: Конечная точка для веб-поиска
titleSuffix: Azure Cognitive Services
description: Чтобы получить результаты поиска в Интернете, отправьте `GET` запрос в следующую конечную точку. Заголовки и параметры URL-адреса определяют дополнительные спецификации.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 25ecd1a753cb9a401408f7ed6605d53e5310df2b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075676"
---
# <a name="web-search-endpoint"></a>Конечная точка для поиска в Интернете

> [!WARNING]
> API-интерфейсы поиска Bing перемещаются из Cognitive Services в Поиск Bing службы. Начиная с **30 октября 2020** , все новые экземпляры Поиск Bing должны быть подготовлены, следуя описанному [здесь](https://aka.ms/cogsvcs/bingmove)процессу.
> API-интерфейсы поиска Bing, подготовленные с помощью Cognitive Services, будут поддерживаться в течение следующих трех лет или до конца Соглашение Enterprise, в зависимости от того, что происходит раньше.
> Инструкции по миграции см. в разделе [Поиск Bing Services](https://aka.ms/cogsvcs/bingmigration).

**API для поиска в Интернете** возвращает веб-страницы, новости, изображения, видео и [сущности](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Сущности содержат сводные данные о человеке, месте или теме.

## <a name="endpoint"></a>Конечная точка

Чтобы получить результаты поиска в Интернете с помощью API Bing, отправьте запрос `GET` на следующую конечную точку. Заголовки и параметры URL-адреса определяют дополнительные спецификации.

**Конечная точка** возвращает результаты поиска в Интернете, соответствующие поисковому запросу пользователя, который определен с помощью `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Конечная точка: дополнительные сведения о заголовках, параметрах, кодах рынков, объектах ответов, ошибках и т. п. вы найдете в справочнике [по API Bing для поиска в Интернете версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

## <a name="response-json"></a>Ответ в формате JSON

Ответ на запрос на поиск в Интернете содержит результаты в виде объектов JSON. Для анализа результатов требуются процедуры обработки элементов каждого типа. Чтобы ознакомиться с примерами, изучите это [руководство](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) и [исходный код](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search).

## <a name="next-steps"></a>Дальнейшие действия

Интерфейсы API **Bing** поддерживают действия поиска, которые возвращают результаты определенного типа. Все конечные точки поиска возвращают результаты в виде объектов ответа JSON.  Все конечные точки поддерживают запросы, которые возвращают результаты с учетом языка и местоположения по значениям долготы, широты и радиуса поиска.

Полные сведения о параметрах, поддерживаемых каждой конечной точкой, приведены в справочной документации по каждому типу.
Примеры простых запросов к API поиска в Интернете приведены в [кратких руководствах по поиску в Интернете](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
