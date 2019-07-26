---
title: Краткое руководство. Вызов конечной точки службы "Пользовательский поиск Bing" с помощью Python | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Сведения из этого краткого руководства помогут вам приступить к созданию запросов результатов поиска из экземпляра службы "Пользовательский поиск Bing" с помощью Python
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 7d3aeb2fd9072c508cad5adec9ce9129087d3a88
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405140"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Краткое руководство. Вызов конечной точки службы "Пользовательский поиск Bing" с помощью Python

Сведения из этого краткого руководства помогут вам приступить к созданию запросов результатов поиска из экземпляра "Пользовательский поиск Bing". Хотя это приложение создается на языке Python, API пользовательского поиска Bing представляет собой веб-службу RESTful, совместимую с большинством языков. Исходный код этого примера доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Предварительные требования

- Экземпляр службы "Пользовательский поиск Bing". См. [Краткое руководство. Создание первого экземпляра службы "Пользовательский поиск Bing"](quick-start.md), чтобы получить дополнительные сведения.
- [Python](https://www.python.org/) 2.x или 3.x.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл Python в любой интегрированной среде разработки или редакторе и добавьте следующие инструкции импорта. Создайте переменные для ключа подписки, идентификатор пользовательской конфигурации и условие поиска. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Отправка и получение поискового запроса 

1. Создайте URL-адрес запроса, добавив условие поиска к параметру запроса `q=` и идентификатор пользовательской конфигурации экземпляра поиска к `customconfig=`. Разделяйте параметры символом `&`. 

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Отправьте запрос к экземпляру службы "Пользовательский поиск Bing" и выведите на экран возвращенные результаты поиска.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание веб-страницы пользовательского поиска](./tutorials/custom-search-web-page.md)
