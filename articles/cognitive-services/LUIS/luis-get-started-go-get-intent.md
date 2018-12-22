---
title: Получение намерения с использованием Go
titleSuffix: Language Understanding - Azure Cognitive Services
description: Из этого краткого руководства по Go вы узнаете, как использовать общедоступное приложение LUIS для определения намерений пользователя в разговоре.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 9768e7945a94c5b28a1f28ea0264108cd47865d6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104719"
---
# <a name="quickstart-get-intent-using-go"></a>Краткое руководство. Получение намерения с использованием Go

Из этой статьи вы узнаете, как передать фразы в конечную точку LUIS и получить намерение и сущности.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Предварительные требования

* язык программирования [GO](https://golang.org/);  
* [Visual Studio Code](https://code.visualstudio.com/)
* идентификатор общедоступного приложения: df67dcdb-c37d-46af-88e1-8b97951ca1c2.

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Получение ключа LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Получение намерения с помощью браузера

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Получение намерения программным способом

С помощью GO можно получить доступ к тем же результатам, которые вы видели в окне браузера на предыдущем шаге. 

1. Создайте файл с именем `endpoint.go`. Добавьте следующий код:
    
   [!code-go[Go code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/go/endpoint.go?range=36-98)]

2. В командной строке каталога, в котором вы создали файл Go, введите `go build endpoint.go`, чтобы скомпилировать этот файл. Командная строка не возвращает никаких данных в случае успешной компиляции.

3. Запустите приложение Go из командной строки, введя следующий текст: 

    ```CMD
    go run endpoint.go -appID df67dcdb-c37d-46af-88e1-8b97951ca1c2 -endpointKey <add-your-key> -region westus
    ```
    
    Замените `<add-your-key>` фактическим значением ключа.  
    
    В командной строке возвращается следующий ответ: 
    
    ```CMD
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    region has value westus
    utterance has value turn on the bedroom light
    response
    {
        "query": "turn on the bedroom light",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.809439957
        },
        "entities": [
            {
            "entity": "bedroom",
            "type": "HomeAutomation.Room",
            "startIndex": 12,
            "endIndex": 18,
            "score": 0.8065475
            }
        ]
    }
    ```
    
## <a name="luis-keys"></a>Ключи LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов
Закройте файл Go и удалите его из файловой системы. 

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Добавление высказываний](luis-get-started-go-add-utterance.md)