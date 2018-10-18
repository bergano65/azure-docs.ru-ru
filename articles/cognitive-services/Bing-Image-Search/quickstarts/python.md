---
title: Краткое руководство. Поиск изображения с помощью Python (API Bing для поиска изображений)
description: Используйте это краткое руководство, чтобы вызвать API Bing для поиска изображений и получить ответ в формате JSON. Это простое приложение Python отправляет поисковой запрос к API и отображает необработанные результаты.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 8/20/2018
ms.author: aahi
ms.openlocfilehash: 44cc556e68234fb9957c01fa9f04861293e96e6a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46298172"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>Краткое руководство. Отправка поисковых запросов с помощью REST API и Python

Используйте это краткое руководство, чтобы вызвать API Bing для поиска изображений и получить ответ в формате JSON. Это простое приложение Python отправляет поисковой запрос к API и отображает необработанные результаты.

Хотя это приложение создается на языке Python, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

Этот пример можно запустить как записную книжку Jupyter в [MyBinder](https://mybinder.org), щелкнув эмблему запуска Binder:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


Кроме того, исходный код этого примера доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-quickstart"></a>Выполнение быстрого запуска

Чтобы приступить к работе, установите в качестве значения `subscription_key` действительный ключ подписки для службы Bing API.

```python
subscription_key = None
assert subscription_key
```

Затем проверьте правильность конечной точки `search_url`. На момент написания этой статьи для API-интерфейсов поиска Bing используется только одна конечная точка. Если вы столкнетесь с ошибками авторизации, внимательно сверьте это значение с конечной точкой поиска Bing на панели мониторинга Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Задайте `search_term` для поиска изображений щенков.


```python
search_term = "puppies"
```

В следующем блоке для вызова API-интерфейсов поиска Bing и возврата результатов в формате объекта JSON используется библиотека `requests` на Python. Обратите внимание, что ключ API передается через словарь `headers`, а условие поиска — через словарь `params`. Чтобы просмотреть весь список параметров, доступных для фильтрации результатов поиска, обратитесь к документации по [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Объект `search_results` содержит фактические рисунки вместе с обширными метаданными, такими как связанные элементы. Например, следующая строка кода позволяет извлечь URL-адреса эскизов для первых 16 результатов.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Затем с помощью библиотеки `PIL` скачайте эскизы изображений, а с помощью библиотеки `matplotlib` — преобразуйте их для просмотра в сетке $4 \times 4$.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

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

## <a name="sample-json-response"></a>Пример ответа в формате JSON

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
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по одностраничным приложениям для API Bing для поиска изображений](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>См. также

* [Что такое API Bing для поиска изображений?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Попробуйте API Bing для поиска изображений](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Документация по службам Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Справочник по API Bing для поиска изображений](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
