---
title: Краткое руководство. Поиск с помощью Python и API Bing для поиска в Интернете
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как отправлять запросы в REST API Bing для поиска в Интернете с помощью Python и получать ответы в формате JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2f26392bdac34dd831e04c772e5357f5e41fc746
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390210"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Краткое руководство. Вызов API Bing для поиска в Интернете с использованием Python  

Из этого краткого руководства вы узнаете, как вызвать API Bing для поиска в Интернете и получить ответ в формате JSON. Это приложение Python отправляет поисковый запрос к API и показывает ответ. Хотя это приложение создается на языке Python, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

Этот пример запускается как записная книжка Jupyter в [MyBinder](https://mybinder.org). Выберите эмблему запуска Binder.

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Предварительные требования

* [Python версии 2.x или 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Определение переменных

Замените значение `subscription_key` действительным ключом подписки из своей учетной записи Azure.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Объявите конечную точку API Bing для поиска в Интернете. Если вы столкнетесь с ошибками авторизации, внимательно сверьте это значение с конечной точкой поиска Bing на панели мониторинга Azure.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Вы можете настроить поисковый запрос, заменив значение параметра `search_term`.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>Выполнение запроса

В этом блоке для вызова API Bing для поиска в Интернете и возврата результатов в виде объекта JSON используется библиотека `requests`. Ключ API передается в словарь `headers`, а условие поиска и параметры запроса — в словарь `params`. Полный список вариантов и параметров см. в документации по [API Bing для поиска в Интернете версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Форматирование и отображение ответа

Объект `search_results` включает результаты поиска и метаданные, например связанные запросы и страницы. Этот код использует библиотеку `IPython.display`, чтобы форматировать и отображать ответ в браузере.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Пример кода в GitHub

Если вы хотите выполнить этот код локально, готовый [пример можно найти в GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по одностраничным приложениям для API Bing для Поиска в Интернете](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
