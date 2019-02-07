---
title: Краткое руководство. Пакет SDK для Визуального поиска Bing для Python
titleSuffix: Azure Cognitive Services
description: Установка компонентов консольного приложения пакета SDK для визуального поиска для Python.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 06/11/2018
ms.author: v-gedod
ms.openlocfilehash: dfd611746b8d962d5844fcac0e9ea42638acad11
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766611"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-python"></a>Краткое руководство. Получение сведений изображений с помощью пакета SDK для Визуального поиска Bing для Python

Ознакомьтесь с этим кратким руководством, чтобы начать получать сведения изображений из службы "Визуальный поиск Bing" с помощью пакета SDK для Python. Хотя визуальный поиск Bing имеет API REST, совместимый с большинством языков, пакет SDK предоставляет простой способ интегрировать службу в приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) 

## <a name="prerequisites"></a>Предварительные требования

* [Python](https://www.python.org/) 2.x или 3.x.
* Рекомендуется использовать [виртуальную среду](https://docs.python.org/3/tutorial/venv.html). Установите и инициализируйте виртуальное окружение с помощью [модуля venv](https://pypi.python.org/pypi/virtualenv). Установите virtualenv для Python 2.7.
* Пакет SDK для Визуального поиска Bing для Python. Установите его, выполнив следующие команды.
    1. `cd mytestenv`
    2. `python -m pip install azure-cognitiveservices-search-visualsearch`



[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл Python в любой интегрированной среде разработки или редакторе и добавьте следующие инструкции импорта. 

    ```python
    import http.client, urllib.parse
    import json
    import os.path
    from azure.cognitiveservices.search.visualsearch import VisualSearchClient
    from azure.cognitiveservices.search.visualsearch.models import (
        VisualSearchRequest,
        CropArea,
        ImageInfo,
        Filters,
        KnowledgeRequest,
    )
    ```
2. Создайте переменные для ключа подписки, идентификатора пользовательской конфигурации и образа, который нужно загрузить. 
    
    ```python
    subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
    PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
    image_path = os.path.join(PATH, "image.jpg")
    
    ```

3. Создание экземпляра клиента

    ```python
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"))
    ```

## <a name="send-the-search-request"></a>Отправка поискового запроса

1. После открытия файла изображения сериализируйте `VisualSearchRequest()` и передайте его в качестве параметра `knowledge_request` для `visual_search()`.

    ```python
    with open(image_path, "rb") as image_fd:
        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(VisualSearchRequest().serialize())
    
        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
    ```

2. Если результаты были возвращены, распечатайте их, теги и действия в первый тегах.

    ```python
    if not result:
            print("No visual search result data.")
    
            # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")
    
        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))
    
            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")
    ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по одностраничным веб-приложениям для наглядного поиска](tutorial-bing-visual-search-single-page-app.md)
