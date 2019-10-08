---
title: Краткое руководство. Получение намерения с помощью браузера — LUIS
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства вы узнаете, как использовать общедоступное приложение LUIS для определения намерения пользователя на основе текста разговора в браузере.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6282e768ebc51d0d4ec2b15f057727f207a7d81a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703604"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Краткое руководство. Получение намерения с помощью браузера

Чтобы понять, какие данные возвращает конечная точка прогнозирования LUIS, просмотрите результат прогнозирования в веб-браузере. 

## <a name="prerequisites"></a>Предварительные требования

Чтобы запросить общедоступное приложение, вам потребуется перечисленные ниже средства.

* Ваш ключ Распознавания речи (LUIS). Если у вас еще нет подписки для создания ключа, вы можете зарегистрироваться, чтобы получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Идентификатор общедоступного приложения: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## <a name="use-the-browser-to-see-predictions"></a>Использование браузера для просмотра прогнозирования

1. Откройте веб-браузер. 
1. Используйте полные URL-адреса, приведенные ниже, заменив `{your-key}` своим собственным ключом LUIS. Запросы — это запросы GET, которые включает авторизацию с ключом LUIS в качестве параметра строки запроса.

    #### <a name="v2-prediction-endpoint-requesttabv2"></a>[Запрос конечной точки прогнозирования V2](#tab/V2)
    
    Формат URL-адреса V2 для запроса **GET** к конечной точке:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key={your-key}&q=turn on all lights
    `
    
    #### <a name="v3-prediction-endpoint-requesttabv3"></a>[Запрос конечной точки прогнозирования V3](#tab/V3)
    
    
    Формат URL-адреса V3 для запроса **GET** к конечной точке:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}
    `
    
    * * *

1. Вставьте URL-адрес в окне браузера и нажмите клавишу ВВОД. В браузере отображается результат JSON, который указывает, что LUIS определяет намерение `HomeAutomation.TurnOn` как основное и сущность `HomeAutomation.Operation` со значением `on`.

    #### <a name="v2-prediction-endpoint-responsetabv2"></a>[Отклик конечной точки прогнозирования V2](#tab/V2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    #### <a name="v3-prediction-endpoint-responsetabv3"></a>[Отклик конечной точки прогнозирования V3](#tab/V3)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    * * *

1. Чтобы увидеть все намерения, добавьте соответствующий параметр строки запроса. 

    #### <a name="v2-prediction-endpointtabv2"></a>[Конечная точка прогнозирования V2](#tab/V2)

    Добавьте `verbose=true` в конец строки запроса, чтобы **показать все намерения**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    #### <a name="v3-prediction-endpointtabv3"></a>[Конечная точка прогнозирования V3](#tab/V3)

    Добавьте `show-all-intents=true` в конец строки запроса, чтобы **показать все намерения**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "HomeAutomation.TurnOff": {
                     "score": 0.0207554
                },
                "None": {
                     "score": 0.08687421
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    * * * 

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о [конечной точке прогнозирования V3](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Создание приложения на портале LUIS](get-started-portal-build-app.md)