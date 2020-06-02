---
title: Краткое руководство. Вызов конечной точки службы "Пользовательский поиск Bing" с помощью Python | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Узнайте, как запрашивать результаты поиска из экземпляра Пользовательского поиска Bing с помощью Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: e1248a4e90653f0e862841ec6f58a12364943590
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196559"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Краткое руководство. Вызов конечной точки службы "Пользовательский поиск Bing" с помощью Python

Узнайте, как запрашивать результаты поиска из экземпляра Пользовательского поиска Bing. Хотя это приложение написано на Python, API Пользовательского поиска Bing представляет собой веб-службу RESTful, совместимую с большинством языков программирования. Исходный код этого примера доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Предварительные требования

- Экземпляр службы "Пользовательский поиск Bing". Дополнительные сведения см. в [кратком руководстве Создание первого экземпляра Пользовательского поиска Bing](quick-start.md).
- [Python](https://www.python.org/) версии 2.x или 3.x.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

- Создайте файл Python в любой интегрированной среде разработки или редакторе и добавьте следующие инструкции импорта. Создайте переменные для ключа подписки, идентификатора пользовательской конфигурации и условия поиска.

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Отправка и получение поискового запроса 

1. Создайте URL-адрес запроса, добавив условие поиска к параметру запроса `q=` и идентификатор пользовательской конфигурации экземпляра поиска к параметру `customconfig=`. Разделите параметры символом `&`. Вы можете использовать глобальную конечную точку в следующем коде или конечную точку [личного поддомена](../../cognitive-services/cognitive-services-custom-subdomains.md), отображаемую на портале Azure для вашего ресурса.

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Отправьте запрос к экземпляру Пользовательского поиска Bing и выведите на экран полученные результаты поиска.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание веб-страницы пользовательского поиска](./tutorials/custom-search-web-page.md)
