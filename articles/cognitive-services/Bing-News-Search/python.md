---
title: Краткое руководство. Поиск новостей с помощью REST API Поиск новостей Bing и Python
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве показано, как отправлять запросы в REST API Bing для поиска в новостей с помощью Python и получать ответы в формате JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 1c424c75a4df193ec412355607c68abeda0560a5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448492"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Краткое руководство. Поиск новостей с помощью REST API Bing для поиска новостей и Python

Из этого краткого руководства вы узнаете, как вызвать API Bing для поиска новостей и получить ответ в формате JSON. Это простое приложение JavaScript отправляет поисковый запрос к API и обрабатывает результаты. Хотя это приложение создается на языке Python, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

Вы можете запустить этот пример кода как записную книжку Jupyter в [MyBinder](https://mybinder.org), щелкнув эмблему запуска Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Исходный код этого примера также доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл Python в избранной интегрированной среде разработки или редакторе и импортируйте модуль запроса. Создайте переменные для вашего ключа подписки, конечной точки и условия поиска. Вы можете использовать указанную ниже глобальную конечную точку или конечную точку [пользовательского поддомена](../../cognitive-services/cognitive-services-custom-subdomains.md), отображаемого на портале Azure для вашего ресурса.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Создание параметров для запроса

1. Добавьте ключ подписки в новый словарь, используя `"Ocp-Apim-Subscription-Key"` в качестве ключа. Используйте его для параметров поиска.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Отправка запроса и получение ответа

1. Используйте библиотеку запросов для вызова API визуального поиска Bing с использованием ключа подписки и объектов словаря, созданных на последнем шаге.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results` содержит ответ от API в виде объекта JSON. Доступ к описаниям статей, содержащихся в ответе.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>Отображение результатов

Эти описания затем можно визуализировать в виде таблицы с ключевым словом поиска, выделенным **полужирным шрифтом**.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](tutorial-bing-news-search-single-page-app.md)
