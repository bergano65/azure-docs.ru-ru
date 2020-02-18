---
title: Получение сведений о намерении с помощью вызова REST и Node.js
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 9252fbbf0895bf821c119272ac37d3af1c91fc89
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987807"
---
## <a name="prerequisites"></a>Предварительные требования

* Язык программирования [Node.js](https://nodejs.org/).
* [Visual Studio Code](https://code.visualstudio.com/)
* Идентификатор общедоступного приложения: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.

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

## <a name="get-intent-programmatically"></a>Получение намерения программным способом

Используйте Node.js, чтобы запросить [конечную точку прогноза](https://aka.ms/luis-apim-v3-prediction) и получить результат прогноза.

1. Скопируйте следующий фрагмент кода в файл с именем `predict.js`:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    var querystring = require('querystring');

    // Analyze text
    //
    getPrediction = async () => {

        // YOUR-KEY - Language Understanding runtime key
        var endpointKey = "YOUR-KEY";

        // YOUR-ENDPOINT Language Understanding endpoint URL, an example is your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT";

        // Set the LUIS_APP_ID environment variable
        // to df67dcdb-c37d-46af-88e1-8b97951ca1c2, which is the ID
        // of a public sample application.
        var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

        var utterance = "turn on all lights";

        // Create query string
        var queryParams = {
            "show-all-intents": true,
            "verbose":  true,
            "query": utterance,
            "subscription-key": endpointKey
        }

        // append query string to endpoint URL
        var URI = `https://${endpoint}/luis/prediction/v3.0/apps/${appId}/slots/production/predict?${querystring.stringify(queryParams)}`

        // HTTP Request
        const response = await requestpromise(URI);

        // HTTP Response
        console.log(response);

    }

    // Pass an utterance to the sample LUIS app
    getPrediction().then(()=>console.log("done")).catch((err)=>console.log(err));
    ```

1. Замените значения `YOUR-KEY` и `YOUR-ENDPOINT` собственным ключом и конечной точкой **среды выполнения** прогнозирования.

    |Сведения|Назначение|
    |--|--|
    |`YOUR-KEY`|Ваш ключ (32 символа) **среды выполнения** прогнозирования.|
    |`YOUR-ENDPOINT`| Конечная точка URL-адреса прогнозирования. Например, `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Установите зависимости `request`, `request-promise` и `querystring` с помощью следующей команды:

    ```console
    npm install request request-promise querystring
    ```

1. Запустите приложение с помощью следующей команды:

    ```console
    node predict.js
    ```

 1. Проверьте ответ прогноза, который возвращается в формате JSON:

    ```console
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
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

## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с этим кратким руководством удалите файл из файловой системы.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Добавление высказываний и обучение](../get-started-get-model-rest-apis.md)