---
title: Фильтрация результатов из API Bing Local Business Search с помощью географических границ
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как фильтровать результаты поиска из API Bing Local Business Search.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: aec50155089b12122f0c438aecaf8efd737e59f7
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881673"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Фильтрация результатов из API Bing Local Business Search с помощью географических границ

API Bing Local Business Search позволяет задать границы в определенной географической области, в которой вам нужно выполнить поиск с помощью параметров запроса `localCircularView` или `localMapView`. Укажите только один параметр в запросах. 

Если условие поиска содержит прямое географическое местоположение, API Bing Local Business Search будет автоматически использовать его для задания границ для представления результатов поиска. Например, если условие поиска — `sailing in San Diego`, то `San Diego` будет использоваться как расположение, а другие определенные расположения в параметрах запроса или пользовательских заголовках будут игнорироваться. 

Если географическое расположение не обнаружено в условии поиска и не указано с помощью параметров запроса, API Bing Local Business Search попытается определить расположение в заголовках `X-Search-ClientIP` или `X-Search-Location` запроса. Если заголовок не указан, API определит расположение по IP-адресу клиента запроса или координатам GPS для мобильных устройств.

## <a name="localcircularview"></a>localCircularView

Параметр `localCircularView` создает круглую географическую область вокруг набора координат широты и долготы, определяемых радиусом. При использовании этого параметра ответы от API Bing Local Business Search будут включать только расположения в пределах этого круга, в отличие от параметра `localMapView`, который может содержать расположения, немного выходящие за пределы области поиска.

Чтобы задать круглую географическую область поиска, выберите широту и долготу в качестве центра круга и радиус в метрах. Этот параметр можно затем добавить в строку запроса, например: `q=Restaurants&localCircularView=47.6421,-122.13715,5000`.

Завершение запроса:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

Параметр `localMapView` указывает прямоугольную географическую область поиска с помощью двух наборов координат для указания его юго-восточного и северо-западного углов. При использовании этого параметра ответы от API Bing Local Business Search могут включать расположения в пределах указанной области и возле нее, в отличие от параметра `localCircularView`, который может содержать только расположения в пределах области поиска.

Чтобы задать прямоугольную область поиска, выберите два набора координат широты и долготы в качестве юго-восточного и северо-западного углов границы. Сначала определите юго-восточные координаты, как показано в следующем примере: `localMapView=47.619987,-122.181671,47.6421,-122.13715`.

Завершение запроса:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Следующие шаги
- [Краткое руководство. Использование Local Business Search с помощью Java](quickstarts/local-search-java-quickstart.md)
- [Краткое руководство. Использование Local Business Search с помощью C#](quickstarts/local-quickstart.md)
- [Quickstart: Send a query to the Bing Local Business Search API using Node.js](quickstarts/local-search-node-quickstart.md) (Краткое руководство. Отправка запроса в API Bing Local Business Search с помощью Node.js)
- [Quickstart: Send a query to the Bing Local Business Search API in Python](quickstarts/local-search-python-quickstart.md) (Краткое руководство. Отправка запроса в API Bing Local Business Search с помощью Python)
