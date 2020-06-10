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
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, tracking-python
ms.openlocfilehash: 37571113be715c7eb6b120aa592b5a2422a08ad8
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84610004"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Краткое руководство. Поиск новостей с помощью REST API Bing для поиска новостей и Python

Используйте это краткое руководство, чтобы выполнить вызов API "Поиск новостей Bing". Это простое приложение Python отправляет поисковый запрос к API и обрабатывает результат JSON. 

Это приложение создано на языке Python. Но API представляет собой веб-службу на основе REST, совместимую с большинством языков программирования.

Чтобы запустить этот пример кода как записную книжку Jupyter в [MyBinder](https://mybinder.org), выберите эмблему **запуска Binder**: 

[![Запуск Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Исходный код этого примера также доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

Создайте файл Python в избранной интегрированной среде разработки или редакторе и импортируйте модуль запроса. Создайте переменные для своего ключа подписки, конечной точки и условия поиска. Вы можете использовать глобальную конечную точку, указанную в коде ниже, или конечную точку [личного поддомена](../../cognitive-services/cognitive-services-custom-subdomains.md), которая отображается на портале Azure для вашего ресурса.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

## <a name="create-parameters-for-the-request"></a>Создание параметров для запроса

Добавьте ключ подписки в новый словарь, используя `Ocp-Apim-Subscription-Key` в качестве ключа. Используйте его для параметров поиска.

```python
headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
```

## <a name="send-a-request-and-get-a-response"></a>Отправка запроса и получение ответа

1. Используйте библиотеку запросов для вызова API "Визуальный поиск Bing" с помощью ключа подписки и объектов словаря, которые вы создали на предыдущем этапе.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. Ознакомьтесь с описаниями статей, содержащимися в ответе API. Он хранится в `search_results` как объект JSON. 
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="display-the-results"></a>Отображение результатов

Эти описания затем можно визуализировать в виде таблицы с ключевым словом поиска, выделенным полужирным шрифтом.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](tutorial-bing-news-search-single-page-app.md)
