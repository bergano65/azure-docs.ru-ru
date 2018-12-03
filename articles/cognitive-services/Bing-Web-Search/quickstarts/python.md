---
title: Краткое руководство. Поиск с помощью Python и API Bing для поиска в Интернете
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства вы узнаете, как вызвать API Bing для поиска в Интернете и получить ответ в формате JSON, используя Python.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 8/16/2018
ms.author: aahi
ms.openlocfilehash: 0f6f3991e01e4eb6919d958002ef6230a2570dbe
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309463"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Краткое руководство. Вызов API Bing для поиска в Интернете с использованием Python  
Получите [ключ доступа Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) с помощью **поиска**.  См. также [Цены на Cognitive Services. API-интерфейсы поиска Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Используйте это краткое руководство, чтобы за 10 минут вызвать API Bing для поиска в Интернете и получить ответ в формате JSON.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

См. также [Цены на Cognitive Services. API-интерфейсы поиска Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Этот пример запускается как записная книжка Jupyter в [MyBinder](https://mybinder.org). Для этого щелкните эмблему запуска Binder:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

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

В этом блоке для вызова API Bing для поиска в Интернете и возврата результатов в виде объекта JSON используется библиотека `requests`. Ключ API передается в словарь `headers`, а условие поиска и параметры запроса — в словарь `params`. Полный список вариантов и параметров см. в документации по [API Bing для поиска в Интернете версии 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).

```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Форматирование и отображение ответа

Объект `search_results` включает результаты поиска и метаданные, в частности связанные запросы и страницы. Этот код использует библиотеку `IPython.display`, чтобы форматировать и отображать ответ в браузере.

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

Если вы хотите выполнить этот код локально, готовый [пример можно найти в GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingWebSearchv7.js).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по одностраничным приложениям для API Bing для Поиска в Интернете](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
