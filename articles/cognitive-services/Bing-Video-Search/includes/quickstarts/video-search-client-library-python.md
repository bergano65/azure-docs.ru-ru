---
title: Краткое руководство по использованию клиентской библиотеки Поиска видео Bing для Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: 7a9fab8ba8bb9d21c9284cbf14bc67226d2ef9d3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "80289862"
---
Используйте это краткое руководство, чтобы начать поиск новостей с помощью клиентской библиотеки Поиска видео Bing для Python. Поскольку REST API Поиска видео Bing совместим с большинством языков программирования, клиентская библиотека обеспечивает простой способ интеграции службы в ваши приложения. Исходный код этого примера можно получить на [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) с дополнительными возможностями и заметками.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Предварительные требования

- [Python](https://www.python.org/) 2.x или 3.x.
- Клиентская библиотека Поиска видео Bing для python

Мы советуем использовать [виртуальное окружение](https://docs.python.org/3/tutorial/venv.html) Python. Вы можете установить и инициализировать виртуальное окружение с помощью [модуля venv](https://pypi.python.org/pypi/virtualenv). Установите virtualenv для Python 2.7 с помощью команды:

```console
python -m venv mytestenv
```

Установите клиентскую библиотеку Поиска видео Bing с помощью следующей команды:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл Python в любой интегрированной среде разработки или редакторе и добавьте следующие инструкции импорта. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchClient
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Создайте переменную для ключа подписки. 

    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    ```

## <a name="create-the-search-client"></a>Создание клиента для поиска

Создайте экземпляр `CognitiveServicesCredentials`, а затем создайте экземпляр клиента:

```python
client = VideoSearchAPI(endpoint, CognitiveServicesCredentials(subscription_key))
```

## <a name="send-a-search-request-and-get-a-response"></a>Отправка поискового запроса и получение ответа

1. Используйте `client.videos.search()` в поисковом запросе, чтобы отправить запрос в API Поиска видео Bing и получить ответ.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Если ответ содержит результаты поиска, извлеките первый и выведите его идентификатор, имя и URL-адрес.

    ```python
    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. Одностраничное приложение для поиска видео](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>См. также раздел 

- [Что такое API Bing для поиска видео?](../../overview.md)
- [Примеры пакета SDK для .NET в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
