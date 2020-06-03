---
title: Краткое руководство. Отправка запроса в API Поиска местных компаний Bing с помощью Python
titleSuffix: Azure Cognitive Services
description: Из этого руководства вы узнаете, как использовать API "Поиск местных компаний Bing" с помощью Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.openlocfilehash: 3a90d5455c0664ceabf80647fc94a37ad0c716b5
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873027"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Краткое руководство. Отправка запроса в API Bing Local Business Search с помощью Python

Из этого краткого руководства вы узнаете, как отправлять запросы в API Поиска местных компаний Bing в Azure Cognitive Services. Это простое приложение написано на Python. Но API является веб-службой RESTful, совместимой с любым языком программирования, который может выполнять HTTP-запросы и анализировать данные в формате JSON.

В этом примере приложения из API извлекаются сведения о местных компаниях по поисковому запросу.

## <a name="prerequisites"></a>Предварительные требования

* [Python](https://www.python.org/) версии 2.x или 3.x.
* [Учетная запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с API-интерфейсами Поиска Bing. Для работы с этим кратким руководством достаточно [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Сохраните ключ API, указанный при активации бесплатной пробной версии. См. сведения на странице [Цены на Cognitive Services. API-интерфейсы поиска Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Запуск готового приложения

В следующем примере возвращаются локализованные результаты, которые реализуются в следующих действиях:
1. Объявите переменные для указания конечной точки с помощью узла и пути.
2. Задайте параметр запроса. 
3. Определите функцию поиска, которая отвечает за создание запроса и добавление заголовка `Ocp-Apim-Subscription-Key`.
4. Установите заголовок `Ocp-Apim-Subscription-Key`. 
5. Установите подключение и отправьте запрос.
6. Выведите на экран результаты JSON.

Полный код этого примера выглядит так:

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

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
- [Краткое руководство. Отправка запроса в API Bing для поиска местных компаний с помощью Java](local-search-java-quickstart.md)
- [Краткое руководство по использованию Поиска местных компаний Bing с помощью C#](local-quickstart.md)
- [Краткое руководство по использованию Поиска местных компаний Bing с помощью Node.js](local-search-node-quickstart.md)
