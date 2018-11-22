---
title: Краткое руководство. API Bing для поиска новостей (Python)
titlesuffix: Azure Cognitive Services
description: Сведения и примеры кода для быстрого начала работы с API Bing для поиска новостей.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: aahi
ms.openlocfilehash: 2696401b15ceca86c7e7b7d33e7e7f7887f4b7dc
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164343"
---
# <a name="quickstart-for-bing-news-search-api-with-python"></a>Краткое руководство по API Bing для поиска новостей с использованием Python
В этом пошаговом руководстве демонстрируется простой пример вызова API Bing для поиска новостей и последующей обработки полученного в результате объекта JSON. Дополнительные сведения см. в [документации по поиску новостей в Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).  

Этот пример можно запустить как объект записной книжки Jupyter в [MyBinder](https://mybinder.org), щелкнув эмблему запуска Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

## <a name="prerequisites"></a>Предварительные требования

Необходима [учетная запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с **API-интерфейсами поиска Bing**. Для этого краткого руководства достаточно [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Потребуется ключ доступа, предоставляемый при активации бесплатной пробной версии. Можно также использовать ключ платной подписки, указанный на панели мониторинга Azure.

## <a name="running-the-walkthrough"></a>Работа с пошаговым руководством
Сначала задайте для `subscription_key` значение своего ключа API для службы API Bing.


```python
subscription_key = None
assert subscription_key
```

Затем проверьте правильность конечной точки `search_url`. На момент написания этой статьи для API-интерфейсов поиска Bing используется только одна конечная точка. Если вы столкнетесь с ошибками авторизации, внимательно сверьте это значение с конечной точкой поиска Bing на панели мониторинга Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

Задайте `search_term` для поиска статей с новостями о корпорации Майкрософт.


```python
search_term = "Microsoft"
```

В следующем блоке для вызова API поиска Bing и возврата результатов в формате объекта JSON используется библиотека `requests` на Python. Обратите внимание, что ключ API передается через словарь `headers`, а условие поиска — через словарь `params`. Чтобы просмотреть весь список параметров, доступных для фильтрации результатов поиска, см. документацию по [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Объект `search_results` содержит связанные статьи новостей вместе с подробными метаданными. Например, следующая строка кода извлекает описания статей.


```python
descriptions = [article["description"] for article in search_results["value"]]
```

Эти описания затем можно визуализировать в виде таблицы с ключевым словом поиска, выделенным **полужирным шрифтом**.


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Разбиение новостей на страницы](paging-news.md)
> [Использование маркеров оформления для выделения текста](hit-highlighting.md)

## <a name="see-also"></a>См. также 

 [Поиск новостей в Интернете](search-the-web.md)  
 [Тестирование](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
