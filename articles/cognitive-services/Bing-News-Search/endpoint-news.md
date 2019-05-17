---
title: Конечные точки API Bing для поиска новостей
titlesuffix: Azure Cognitive Services
description: Сводные сведения о конечной точке API для поиска новостей.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: b680f476345bde61797fd1cc137cad6e5653d0a5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798217"
---
# <a name="bing-news-search-api-endpoints"></a>Конечные точки API Bing для поиска новостей

**API для поиска новостей** возвращает новостные статьи, веб-страницы, изображения, видео и [сущности](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Сущности содержат сводные данные о человеке, месте или теме.

## <a name="endpoints"></a>Конечные точки

Чтобы получить результаты поиска новостей с помощью API Bing для поиска новостей, отправьте запрос `GET` на одну из конечных точек, приведенных ниже. Заголовки и параметры URL-адреса определяют дополнительные спецификации.

### <a name="news-items-by-search-query"></a>Новости по поисковому запросу

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Возвращает новости на основе поискового запроса. Если отправить пустой поисковый запрос, то API вернет самые популярные новостные статьи из разных категорий. Отправьте запрос по URL-адресу, кодирующему ваш поисковый запрос и добавляющему его к параметру `q=""`. Для обеспечения доступности, см. в разделе [поддерживаемые страны и регионы и рынки](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Самые популярные новости по категориям.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Возвращает самые популярные новости по категориям. Например, можно запросить главные статьи на тему бизнеса, спорта или развлечений, используя значения `category=business`, `category=sports` или `category=entertainment`.  Параметр `category` можно использовать только с URL-адресом `/news`. Существуют некоторые формальные требования для указания категорий. Ознакомьтесь с разделом `category` в документации по [параметрам запроса](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters). Отправьте запрос по URL-адресу, кодирующему ваш поисковый запрос и добавляющему его к параметру `q=""`. Для обеспечения доступности, см. в разделе [поддерживаемые страны и регионы и рынки](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Новости, набирающие популярность. 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Возвращает темы новостей, которые в данный момент набирают популярности в социальных сетях. Когда параметр `/trendingtopics` включен, поиск Bing игнорирует некоторые другие параметры, такие как `freshness` и `?q=""`. Для обеспечения доступности, см. в разделе [поддерживаемые страны и регионы и рынки](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о заголовках, параметрах, кодах рынков, объектах ответов, ошибках и т. п. вы найдете в справочнике [по API Bing для поиска новостей версии 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).

Полные сведения о параметрах, поддерживаемых каждой конечной точкой, приведены в справочной документации по каждому типу.
Простые примеры запросов к API для поиска новостей см. в [кратких руководствах по поиску новостей Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
