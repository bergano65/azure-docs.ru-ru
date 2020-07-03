---
title: Получение сведений о намерении с помощью вызова REST и Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 2307ef6ea8940a3b3ddfb8c7539f4f809dc4c52c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733286"
---
## <a name="prerequisites"></a>Предварительные требования

* [Python 3.6](https://www.python.org/downloads/) или более поздней версии.
* [Visual Studio Code](https://code.visualstudio.com/)
* Идентификатор приложения LUIS. Используйте общедоступный идентификатор приложения для Интернета вещей `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. Пользовательский запрос, используемый в коде из этого краткого руководства, относится только к этому приложению.

## <a name="create-luis-runtime-key-for-predictions"></a>Создание ключа среды выполнения LUIS для прогнозирования

1. Войдите на [портал Azure](https://portal.azure.com).
1. Щелкните [Создать **Распознавание речи**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne).
1. Введите все необходимые параметры для ключа **среды выполнения**:

    |Параметр|Значение|
    |--|--|
    |Имя|Требуемое имя (от 2 до 64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Расположение|Выберите доступное поблизости расположение|
    |Ценовая категория|`F0` — минимальная ценовая категория|
    |Группа ресурсов|Выберите доступную группу ресурсов|

1. Щелкните **Создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурсов.
1. Получите настроенные `endpoint` и `key`.

## <a name="get-intent-from-the-prediction-endpoint"></a>Получение намерений из конечной точки прогноза

Используйте Python, чтобы запросить [конечную точку прогноза](https://aka.ms/luis-apim-v3-prediction) и получить результат прогноза.

1. Скопируйте в файл `predict.py` следующий фрагмент кода:

    ```python
    ########### Python 3.6 #############
    import requests

    try:

        key = 'YOUR-KEY' # your Runtime key
        endpoint = 'YOUR-ENDPOINT' # such as 'your-resource-name.api.cognitive.microsoft.com'
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

1. Замените значения `YOUR-KEY` и `YOUR-ENDPOINT` собственным ключом и конечной точкой **среды выполнения** прогнозирования.

    |Сведения|Назначение|
    |--|--|
    |`YOUR-KEY`|Ваш ключ (32 символа) **среды выполнения** прогнозирования.|
    |`YOUR-ENDPOINT`| Конечная точка URL-адреса прогнозирования. Например, `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Установите зависимость `requests`. Это нужно для создания HTTP-запросов:

    ```console
    pip install requests
    ```

1. Выполните скрипт с помощью этой команды консоли:

    ```console
    python predict.py
    ```

1. Проверьте ответ прогноза, который возвращается в формате JSON:

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Ниже приведен ответ JSON, отформатированный для удобочитаемости:

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

## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с этим кратким руководством удалите файл из файловой системы.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Добавление высказываний и обучение](../get-started-get-model-rest-apis.md)