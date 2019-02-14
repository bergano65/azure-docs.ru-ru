---
title: Конечная точка API автозаполнения Bing
titlesuffix: Azure Cognitive Services
description: Сводные сведения о конечной точке API автозаполнения Bing.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 9e80e81a79f45f132baf07af7c2e7be8f98ceec4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857555"
---
# <a name="bing-autosuggest-endpoint"></a>Конечная точка API автозаполнения Bing

**API автозаполнения Bing** включает одну конечную точку, которая возвращает список предлагаемых запросов для частичного поискового запроса.

## <a name="endpoint"></a>Конечная точка

Чтобы получить предложенные запросы с помощью API Bing, отправьте запрос `GET` на указанную ниже конечную точку. Для определения дополнительных спецификаций используйте заголовки и параметры URL-адреса.

**Конечная точка** возвращает в формате JSON предложения по поиску, релевантные для введенных пользователем данных, которые определяются с помощью `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Подробные сведения о заголовках, параметрах, кодах рынков, объектах ответов, ошибках и т. п. вы найдете в справочнике [по API автозаполнения Bing версии 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference).

## <a name="response-json"></a>Ответ в формате JSON

Ответ на автоматически заполненный запрос включает результаты в виде объектов JSON. Примеры синтаксического анализа результатов см. в этом [руководстве](tutorials/autosuggest.md) и [исходном коде](tutorials/autosuggest-source.md).

## <a name="next-steps"></a>Дополнительная информация

Интерфейсы API **Bing** поддерживают действия поиска, которые возвращают результаты определенного типа. Все конечные точки поиска возвращают результаты в виде объектов ответа JSON.
Все конечные точки поддерживают запросы, которые возвращают результаты с учетом языка и (или) местоположения по значениям долготы, широты и радиуса поиска.

Полные сведения о параметрах, поддерживаемых каждой конечной точкой, см. в справочной документации по каждому типу.
Примеры основных запросов, созданных с помощью API автозаполнения Bing, см. в [этих кратких руководствах](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).
