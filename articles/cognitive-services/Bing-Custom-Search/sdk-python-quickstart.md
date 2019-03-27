---
title: Краткое руководство. Вызов конечной точки пользовательского поиска Bing с помощью пакета SDK для Python | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Используйте пакет SDK пользовательского поиска Bing для Python, чтобы получить настраиваемые результаты поиска.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: aahi
ms.openlocfilehash: c4c5059bc57ea33357145f6b119456dc6c5bdb7b
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571821"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Краткое руководство. Вызов конечной точки пользовательского поиска Bing с помощью пакета SDK для Python 

Используйте это краткое руководство,чтобы начать запрашивать результаты поиска из экземпляра пользовательского поиска Bing с помощью пакета SDK для Python. Хотя Пользовательский поиск Bing имеет API REST, совместимый с большинством языков, пакет SDK для Пользовательского поиска Bing предоставляет простой способ интегрировать службу в приложения. Исходный код этого примера можно получить на [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) с дополнительными возможностями обработки ошибок и заметками.

## <a name="prerequisites"></a>Предварительные требования

- Экземпляр службы "Пользовательский поиск Bing". См. [Краткое руководство. Создание первого экземпляра службы "Пользовательский поиск Bing"](quick-start.md), чтобы получить дополнительные сведения.
- Python версии [2.x или 3.x](https://www.python.org/) 
- [Пакет SDK пользовательского поиска Bing для Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Установка пакета SDK для Python

Установите пакет SDK пользовательского поиска Bing с помощью следующей команды.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Создание приложения

Создайте файл Python в предпочитаемом текстовом редакторе или интегрированной среде разработки и добавьте следующие операции импорта.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Создание поискового клиента и отправка запроса

1. Создайте переменную для ключа подписки.

    ```python
    subscription_key = 'your-subscription-key'
    ```

2. Создайте экземпляр `CustomSearchClient`, используя объект `CognitiveServicesCredentials` с ключом подписки. 

    ```python
    client = CustomSearchClient(CognitiveServicesCredentials(subscription_key))
    ```

3. Отправьте запрос на поиск с помощью `client.custom_instance.search()`. Добавьте условие поиска к параметру `query` ​​и задайте `custom_config` для пользовательского идентификатора конфигурации, чтобы использовать экземпляр поиска. Вы можете получить свой идентификатор на [портале пользовательского поиска Bing](https://www.customsearch.ai/), щелкнув по вкладке **Рабочий этап**.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Просмотр результатов поиска

Если были найдены какие-либо результаты поиска по веб-странице, получите первый из них и распечатайте его имя, URL-адрес и общее количество найденных веб-страниц.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание веб-страницы пользовательского поиска](./tutorials/custom-search-web-page.md)
