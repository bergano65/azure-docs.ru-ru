---
title: Автозаполнение условий поиска. API Bing для поиска в Интернете
titleSuffix: Azure Cognitive Services
description: Создайте пару между API Bing для поиска в Интернете и API автозаполнения Bing для предоставления пользователям улучшенного интерфейса поиска.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 8/13/2018
ms.author: aahi
ms.openlocfilehash: 5640d8ca23f0efc3e54b6ef7c986314a9b209fcf
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192134"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Автозаполнение Bing условий поиска в приложении

Если вы предоставили окно поиска, в котором пользователь вводит свой поисковый запрос, используйте [API автозаполнения Bing](../bing-autosuggest/get-suggested-search-terms.md), чтобы оптимизировать работу. API возвращает предложенные строки запроса на основе частичного поиска, как пользовательские типы.

После того, как пользователь введет условие поиска, оно должно закодироваться с использованием URL-адреса перед установкой параметра запроса [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query). Например, если пользователь вводит *парусные шлюпки*, установите для параметра `q` значение `sailing+dinghies` или `sailing%20dinghies`.

Если в условии поиска есть орфографические ошибки, ответ поиска включает объект [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#querycontext). Объект показывает исходное написание и исправленное написание, которое Bing использует для поиска.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

При отображении результатов поиска можно использовать эти сведения, чтобы пользователь понял, что вы изменили строку запроса.

![Пример UX контекста запроса](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Дополнительная информация  

* Просмотрите пример [ответов API Bing для поиска в Интернете](search-responses.md).  

## <a name="see-also"></a>См. также  

* [Справка по API Bing для поиска в Интернете версии 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
