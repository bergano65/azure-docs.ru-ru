---
title: Краткое руководство по использованию клиентских библиотек Пользовательского поиска Bing (Python)
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ec0ffdcf86e67a7126a3100c1e20b6e5c3474e35
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253052"
---
Приступите к работе с клиентской библиотекой службы Пользовательский поиск Bing для Python. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. Служба "API пользовательского поиска Bing" позволяет создавать специально адаптированные интерфейсы поиска без рекламы по темам, которые действительно важны для вас. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py).

Клиентская библиотека Пользовательского поиска Bing для Python позволяет выполнить следующее.
* Найти результаты поиска в Интернете из вашего экземпляра API Пользовательского поиска Bing.

[Справочная документация](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customsearch?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch) | [Пакет (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | [Примеры](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Предварительные требования

- Экземпляр службы "Пользовательский поиск Bing". См. [Краткое руководство. Создание первого экземпляра службы "Пользовательский поиск Bing"](../../quick-start.md), чтобы получить дополнительные сведения.
- Python версии [2.x или 3.x](https://www.python.org/) 
- [Пакет SDK пользовательского поиска Bing для Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>Установка клиентской библиотеки Python

Установите клиентскую библиотеку Пользовательского поиска Bing с помощью следующей команды.

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

1. Создайте переменную для ключа подписки и конечной точки.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Создайте экземпляр `CustomSearchClient`, используя объект `CognitiveServicesCredentials` с ключом подписки. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание веб-страницы пользовательского поиска](../../tutorials/custom-search-web-page.md)
