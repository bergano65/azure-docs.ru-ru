---
title: Краткое руководство по использованию клиентской библиотеки Поиска сущностей Bing для Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 0c0a124773eab8166806312dc47ded24e1cd841f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79136602"
---
Используйте это краткое руководство, чтобы начать поиск сущностей с помощью клиентской библиотеки Поиска сущностей Bing для Python. Поскольку REST API Поиска сущностей Bing совместим с большинством языков программирования, клиентская библиотека обеспечивает простой способ интеграции службы в ваши приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Предварительные требования

* Python версии [2.x или 3.x](https://www.python.org/)

* [Пакет SDK Поиска сущностей Bing для Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Мы советуем использовать виртуальное окружение Python. Вы можете установить и инициализировать виртуальное окружение с помощью модуля venv. Вы можете установить virtualenv с помощью следующей команды:

```Console
python -m venv mytestenv
```

Установите клиентскую библиотеку Поиска сущностей Bing с помощью следующей команды:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл Python в любой интегрированной среде разработки или редакторе и добавьте следующие инструкции импорта. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchClient
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Создайте переменную для ключа подписки и конечной точки. Создайте экземпляр клиента, создав новый объект `CognitiveServicesCredentials` с помощью ключа.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    client = EntitySearchclient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](../../tutorial-bing-entities-search-single-page-app.md)

* [Основные сведения об API Bing для поиска сущностей](../../overview.md )