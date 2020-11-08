---
title: Конечные точки API Поиска новостей Bing
titleSuffix: Azure Cognitive Services
description: В этой статье содержится сводка конечных точек API поиска новостей. Новости, лучшие новости и тенденции.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c095a46b3a0526b23645c9cbb5e99eb8eda9067
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366406"
---
# <a name="bing-news-search-api-endpoints"></a>Конечные точки API Bing для поиска новостей

> [!WARNING]
> API-интерфейсы поиска Bing перемещаются из Cognitive Services в Поиск Bing службы. Начиная с **30 октября 2020** , все новые экземпляры Поиск Bing должны быть подготовлены, следуя описанному [здесь](/bing/search-apis/bing-web-search/create-bing-search-service-resource)процессу.
> API-интерфейсы поиска Bing, подготовленные с помощью Cognitive Services, будут поддерживаться в течение следующих трех лет или до конца Соглашение Enterprise, в зависимости от того, что происходит раньше.
> Инструкции по миграции см. в разделе [Поиск Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

**API для поиска новостей** возвращает новостные статьи, веб-страницы, изображения, видео и [сущности](../bing-entities-search/overview.md). Сущности содержат сводные данные о человеке, месте или теме.

## <a name="endpoints"></a>Конечные точки

Чтобы получить результаты поиска новостей с помощью API Bing для поиска новостей, отправьте запрос `GET` на одну из конечных точек, приведенных ниже. Заголовки и параметры URL-адреса определяют дополнительные спецификации.

### <a name="news-items-by-search-query"></a>Новости по поисковому запросу

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Возвращает новости на основе поискового запроса. Если отправить пустой поисковый запрос, то API вернет самые популярные новостные статьи из разных категорий. Отправьте запрос по URL-адресу, кодирующему ваш поисковый запрос и добавляющему его к параметру `q=""`. Сведения о доступности см. в разделе [Поддерживаемые страны, регионы и рынки](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Самые популярные новости по категориям.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Возвращает самые популярные новости по категориям. Например, можно запросить главные статьи на тему бизнеса, спорта или развлечений, используя значения `category=business`, `category=sports` или `category=entertainment`.  Параметр `category` можно использовать только с URL-адресом `/news`. Существуют некоторые формальные требования для указания категорий. Ознакомьтесь с разделом `category` в документации по [параметрам запроса](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters). Отправьте запрос по URL-адресу, кодирующему ваш поисковый запрос и добавляющему его к параметру `q=""`. Сведения о доступности см. в разделе [Поддерживаемые страны, регионы и рынки](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Новости, набирающие популярность. 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Возвращает темы новостей, которые в данный момент набирают популярности в социальных сетях. Когда параметр `/trendingtopics` включен, поиск Bing игнорирует некоторые другие параметры, такие как `freshness` и `?q=""`. Сведения о доступности см. в разделе [Поддерживаемые страны, регионы и рынки](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о заголовках, параметрах, кодах рынков, объектах ответов, ошибках и т. п. вы найдете в справочнике [по API Bing для поиска новостей версии 7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

Полные сведения о параметрах, поддерживаемых каждой конечной точкой, приведены в справочной документации по каждому типу.
Простые примеры запросов к API для поиска новостей см. в [кратких руководствах по поиску новостей Bing](/azure/cognitive-services/bing-news-search).