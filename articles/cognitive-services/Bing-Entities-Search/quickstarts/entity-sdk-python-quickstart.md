---
title: Краткое руководство. Поиск сущностей с помощью пакета SDK Поиска сущностей Bing для Python
titleSuffix: Azure Cognitive Services
description: Из этого руководства вы узнаете, как выполнить поиск сущностей с помощью пакета SDK Поиска сущностей Bing для Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 0a4a4e3ce6f318a693b8ee760a0d892eaecb2c82
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479017"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Краткое руководство. Пакет SDK Поиска сущностей Bing для Python

Используйте это краткое руководство, чтобы начать поиск сущностей с помощью пакета SDK Поиска сущностей Bing для Python. Так как для Поиска сущностей Bing имеется REST API, совместимый с большинством языков программирования, пакет SDK обеспечивает простой способ интеграции службы в ваши приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Предварительные требования

* Python версии [2.x или 3.x](https://www.python.org/)

* [Пакет SDK Поиска сущностей Bing для Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Мы советуем использовать виртуальное окружение Python. Вы можете установить и инициализировать виртуальное окружение с помощью модуля venv. Вы можете установить virtualenv с помощью следующей команды:

```Console
python -m venv mytestenv
```

Установите пакет SDK Поиска сущностей Bing с помощью следующей команды:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл Python в любой интегрированной среде разработки или редакторе и добавьте следующие инструкции импорта. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Создайте переменную для ключа подписки и экземпляр клиента путем создания в нем объекта `CognitiveServicesCredentials`.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Отправка поискового запроса и получение ответа

1. Отправьте поисковой запрос в Поиск сущностей Bing с помощью `client.entities.search()` и поискового запроса. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Если возвращены сущности, преобразуйте `entity_data.entities.value` в список и выведите первый результат.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](../tutorial-bing-entities-search-single-page-app.md)

* [Основные сведения об API Bing для поиска сущностей](../overview.md )