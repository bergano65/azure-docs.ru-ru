---
title: Получение сведений о намерении с помощью вызова REST и Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: b2c840b9de25ff6997037c284c60390e7afa03ec
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125597"
---
## <a name="prerequisites"></a>Предварительные требования

* [Python 3.6](https://www.python.org/downloads/) или более поздней версии.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="get-luis-key"></a>Получение ключа LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent--programmatically"></a>Получение намерения программным способом

Используйте Python, чтобы запросить конечную точку прогноза GET [API](https://aka.ms/luis-apim-v3-prediction) и получить результат прогноза.

1. Скопируйте приведенный ниже фрагмент кода в файл с именем `predict.py`.

    ```python
    ########### Python 3.6 #############
    import requests
    
    try:
    
        key = 'YOUR-KEY'
        endpoint = 'YOUR-ENDPOINT' # such as 'westus2.api.cognitive.microsoft.com' 
        appId = 'df67dcdb-c37d-46af-88e1-8b97951ca1c2'
        utterance = 'turn on all lights'
    
        headers = {
        }
    
        params ={
            'query': utterance,
            'timezoneOffset': '0',
            'verbose': 'true',
            'show-all-intents': 'true',
            'spellCheck': 'false',
            'staging': 'false',
            'subscription-key': key
        }
    
        r = requests.get(f'https://{endpoint}/luis/prediction/v3.0/apps/{appId}/slots/production/predict',headers=headers, params=params)
        print(r.json())
    
    except Exception as e:
        print(f'{e}')
    ```

1. Замените следующие значения:

    * `YOUR-KEY` на ключ для начала работы.
    * `YOUR-ENDPOINT` на конечную точку, например `westus2.api.cognitive.microsoft.com`.

1. Для установки зависимостей введите следующие команды в консоли:

    ```console
    pip install requests
    ```

1. Выполните следующую команду в консоли, чтобы запустить сценарий:

    ```console
    python predict.py
    ``` 

1. Проверьте ответ с прогнозом в формате JSON:

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Ответ JSON, отформатированный для удобочитаемости: 

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="luis-keys"></a>Ключи LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с этим кратким руководством удалите файл из файловой системы. 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Добавление высказываний и обучение](../get-started-get-model-rest-apis.md)