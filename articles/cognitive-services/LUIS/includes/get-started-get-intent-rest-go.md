---
title: Получение сведений о намерении с помощью вызова REST и Go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: b36e967e960ec6a2b39409ea7be5f3f98f69b9c8
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838483"
---
## <a name="prerequisites"></a>Предварительные требования

* язык программирования [GO](https://golang.org/);  
* [Visual Studio Code](https://code.visualstudio.com/)
* идентификатор общедоступного приложения: df67dcdb-c37d-46af-88e1-8b97951ca1c2.

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Получение ключа LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

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

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

По окончании работы с этим кратким руководством закройте проект Visual Studio и удалите каталог проекта из файловой системы. 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Добавление высказываний и обучение на Go](../luis-get-started-go-add-utterance.md)