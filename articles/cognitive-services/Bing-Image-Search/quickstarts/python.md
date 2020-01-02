---
title: Краткое руководство. Поиск изображений с помощью REST API и Python — Поиск изображений Bing
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как отправлять запросы в REST API Bing для поиска изображений с помощью Python и получать ответы в формате JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: f3d00688feb0f9d42e80cdbb51753483e53c388d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930725"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Краткое руководство. Поиск изображений с помощью REST API Bing для поиска изображений и Python

В этом кратком руководстве описано, как отправлять поисковые запросы к API Поиска изображений Bing. Это приложение на Python отправляет поисковый запрос к API и отображает URL-адрес первого возвращенного в результатах изображения. Хотя это приложение создается на языке Python, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

Этот пример можно запустить как записную книжку Jupyter в [MyBinder](https://mybinder.org), щелкнув эмблему запуска Binder:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


Исходный код, используемый в данном примере, вместе с дополнительной обработкой ошибок и аннотациями можно получить на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py).


## <a name="prerequisites"></a>Предварительные требования

* [Python версии 2.x или 3.x](https://www.python.org/).
* [Библиотека изображений Python (PIL)](https://pillow.readthedocs.io/en/stable/index.html).
* [matplotlib](https://matplotlib.org/). 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте новый файл Python в избранной интегрированной среде разработки или редакторе, а затем импортируйте в него следующие модули. Создайте переменные для вашего ключа подписки, конечной точки и условия поиска. В качестве `search_url` может быть глобальная конечная точка, приведенная ниже, или конечная точка [пользовательского поддомена](../../../cognitive-services/cognitive-services-custom-subdomains.md), отображаемая на портале Azure для вашего ресурса.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Добавьте ключ подписки в заголовок `Ocp-Apim-Subscription-Key`, создав словарь и добавив в него ключ в качестве значения. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Создание и отправка поискового запроса

1. Создайте словарь для параметров поисковых запросов. Добавьте условие поиска в параметр `q`. Чтобы искать изображения в общедоступном домене, укажите для параметра `license` значение public, а чтобы искать только фотографии, задайте для параметра `imageType` значение photo.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. Используйте библиотеку `requests` для вызова API Поиска изображений Bing. Добавьте заголовок и параметры в запрос и настройте возвращение ответа в виде объекта JSON. Получите URL-адреса для нескольких эскизов из поля ответа `thumbnailUrl`.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
    ```

## <a name="view-the-response"></a>Просмотр ответа

1. Создайте новую фигуру с четырьмя столбцами и строками, используя библиотеку matplotlib. 

2. Выполните итерацию по строкам и столбцам фигуры и используйте метод `Image.open()` библиотеки PIL, чтобы добавить эскиз изображения во все пространства. 

3. Используйте метод `plt.show()`, чтобы нарисовать фигуру и отобразить изображения.

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    plt.show()
    ```


## <a name="example-json-response"></a>Пример ответа в формате JSON

Ответы из API Bing для поиска изображений возвращаются в формате JSON. Представленный пример сокращен для отображения только одного результата.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по одностраничным приложениям для API Bing для поиска изображений](../tutorial-bing-image-search-single-page-app.md)

* [Знакомство с API Bing для поиска изображений](../overview.md)  
* [Сведения о ценах](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) на API Поиска Bing 
* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Документация по службам Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Справочник по API Bing для поиска изображений](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
