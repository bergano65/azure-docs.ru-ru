---
title: Краткое руководство. Отправка запроса в API Bing Local Business Search с помощью Python | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как использовать API Bing Local Business Search с помощью Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: e6626c1243f9f05a56effe3538870a5be9ac6b34
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796570"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Краткое руководство. Отправка запроса в API Bing Local Business Search с помощью Python

С помощью этого краткого руководства вы сможете начать отправку запросов в Bing Local Business Search API в Cognitive Services. Хотя это простое приложение написано на Python, API является веб-службой RESTful, совместимой с любым языком программирования, который может выполнять HTTP-запросы и анализировать данные в формате JSON.

В этом примере приложения из API извлекаются сведения о местных компаниях по поисковому запросу `hotel in Bellevue`.

## <a name="prerequisites"></a>Технические условия

* [Python](https://www.python.org/) 2.x или 3.x.
 
Необходима [учетная запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с API Bing. Для этого краткого руководства достаточно [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Используйте ключ доступа, предоставляемый в бесплатной пробной версии.  См. также [Цены на Cognitive Services. API-интерфейсы поиска Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Запуск готового приложения

Следующий код получает локализованные результаты. Он реализуется с помощью следующих действий:
1. Объявите переменные для указания конечной точки с помощью узла и пути.
2. Задайте параметр запроса. 
3. Определите функцию Поиска, которая создает запрос и добавляет заголовок Ocp-Apim-Subscription-Key.
4. Задайте заголовок Ocp-Apim-Subscription-Key. 
5. Установите подключение и отправьте запрос.
6. Выведите на экран результаты JSON.

Полный код этого примера выглядит так:

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com/bing'
path = '/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>Дальнейшие действия
- [Краткое руководство. Использование Local Business Search с помощью Java](local-search-java-quickstart.md)
- [Краткое руководство. Использование Local Business Search с помощью C#](local-quickstart.md)
- [Краткое руководство. Использование Local Business Search с помощью Node](local-search-node-quickstart.md)
