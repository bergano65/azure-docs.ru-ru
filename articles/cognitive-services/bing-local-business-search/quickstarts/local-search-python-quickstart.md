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
ms.custom: tracking-python
ms.openlocfilehash: c821df0e7cb00c73899a2694dd0b2eb6823b1d9e
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85611202"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Краткое руководство. Отправка запроса в API Bing Local Business Search с помощью Python

Из этого краткого руководства вы узнаете, как отправлять запросы в API Поиска местных компаний Bing в Azure Cognitive Services. Это простое приложение написано на Python. Но API является веб-службой RESTful, совместимой с любым языком программирования, который может выполнять HTTP-запросы и анализировать данные в формате JSON.

В этом примере приложения из API извлекаются сведения о местных компаниях по поисковому запросу.

## <a name="prerequisites"></a>Предварительные требования

* подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/).
* [Python](https://www.python.org/) версии 2.x или 3.x.
* Получив подписку Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="создайте ресурс Поиска Bing"  target="_blank">Create a Bing Search resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.

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
