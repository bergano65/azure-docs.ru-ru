---
title: Автозаполнение условий поиска. API Bing для поиска в Интернете
titleSuffix: Azure Cognitive Services
description: Создайте пару между API Bing для поиска в Интернете и API автозаполнения Bing для предоставления пользователям улучшенного интерфейса поиска.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: conceptual
ms.date: 8/13/2018
ms.author: aahi
ms.openlocfilehash: 1df478f02e90c95d11c9305381e89e147fee3077
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164036"
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
