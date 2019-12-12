---
title: Краткое руководство. Поиск изображений с помощью пакета SDK для поиска изображений Bing для Python
titleSuffix: Azure Cognitive Services
description: Воспользуйтесь сведениями из быстрого начала и выполните первый поиск изображения с помощью пакета SDK Bing для поиска изображений. Данный пакет является оболочкой для API и содержит те же функции. Это простое приложение Python отправляет запрос на поиск изображения, анализирует ответ JSON и отображает URL-адрес первого возвращенного изображения.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 85aa1bda395240d0f11b0654ee48b9f1a0401eaa
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930594"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-python"></a>Краткое руководство. Поиск изображений с помощью пакета SDK для поиска изображений Bing для Python

Воспользуйтесь сведениями из быстрого начала и выполните первый поиск изображения с помощью пакета SDK Bing для поиска изображений. Данный пакет является оболочкой для API и содержит те же функции. Это простое приложение Python отправляет запрос на поиск изображения, анализирует ответ JSON и отображает URL-адрес первого возвращенного изображения.

Исходный код, используемый в данном примере, вместе с дополнительной обработкой ошибок и аннотациями можно получить на [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py).

## <a name="prerequisites"></a>Предварительные требования

* [Python 2.7 или 3.4](https://www.python.org/) и более поздних версий.

* [SDK Поиска изображений Azure](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) для Python.
    * Установка с помощью `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте сценарий Python в избранной интегрированной среде разработки или редакторе и добавьте следующие строки импорта:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Создайте переменные для ключа подписки и условия поиска.

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Создание клиента для поиска изображений

1. Создайте экземпляр `CognitiveServicesCredentials` и используйте его для создания клиента:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
1. Отправьте поисковой запрос к API Bing для поиска изображений:
    ```python
    image_results = client.images.search(query=search_term)
    ```
   ## <a name="process-and-view-the-results"></a>Обработка и просмотр результатов

Выполните синтаксический анализ результатов изображений, возвращенных в ответе.


Если в ответе содержатся результаты поиска, сохраните первый результат и распечатайте такие сведения, как URL-адрес эскиза, исходный URL-адрес и общее количество возвращенных изображений.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по одностраничным приложениям для API Bing для поиска изображений](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>См. также

* [Что такое API Bing для поиска изображений?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Попробуйте API Bing для поиска изображений](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Примеры Python для пакета SDK в Azure Cognitive Services](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Документация по службам Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Справочник по API Bing для поиска изображений](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
