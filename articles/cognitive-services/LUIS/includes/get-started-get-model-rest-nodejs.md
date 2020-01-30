---
title: Получение модели с помощью вызова REST на C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: a262db04e51015edb760a8b04952dfa24b2ad63a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748848"
---
## <a name="prerequisites"></a>Предварительные требования

* Ключ для начала работы.
* Импорт приложения [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) из репозитория GitHub cognitive-services-language-understanding.
* Идентификатор приложения LUIS для импортированного приложения TravelAgent. Идентификатор приложения отображается на панели мониторинга приложения.
* Идентификатор версии приложения, которое получает речевые фрагменты. Идентификатор по умолчанию — 0.1.
* Язык программирования [Node.js](https://nodejs.org/). 
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Файл JSON с примерами высказываний.

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="get-luis-key"></a>Получение ключа LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>Изменение модели программными средствами

На Go добавьте [API](https://aka.ms/luis-apim-v3-authoring) сущности машинного обучения в приложение. 

1. Создайте файл с именем `model.js`. Добавьте следующий код:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    
    const LUIS_authoringKey = "YOUR-KEY";
    const LUIS_endpoint = "YOUR-ENDPOINT";
    const LUIS_appId = "YOUR-APP-ID";
    const LUIS_versionId = "0.1";
    const addUtterancesURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;
    
    const utterances = [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ];
    
    const main = async() =>{
    
    
        await addUtterance();
        await train("POST");
        await trainStatus("GET");
    
    }
    const addUtterance = async () => {
    
        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };
    
        const response = await requestpromise(options)
        console.log(response.body);
    }
    const train = async (verb) => {
    
        const options = {
            uri: addTrainURI,
            method: verb, 
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: null // The body can be empty for a training request
        };
    
        const response = await requestpromise(options)
        console.log(response.body);
    }
    
    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err returned));
    ```
1. Измените следующие значения:

    * `YOUR-KEY` на ключ для начала работы.
    * `YOUR-ENDPOINT` на конечную точку, например `westus2.api.cognitive.microsoft.com`.
    * `YOUR-APP-ID` на идентификатор приложения.

1. В командной строке каталога, в котором вы создали файл, введите следующую команду, чтобы запустить этот файл:

    ```console
    node model.js
    ```  

## <a name="luis-keys"></a>Ключи LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с этим кратким руководством удалите файл из файловой системы. 

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Рекомендации для приложения](../luis-concept-best-practices.md)