---
title: Получение модели с помощью вызова REST на C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: bbb2ae0b10af795d71f0a78c045bec0c216ee378
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368404"
---
## <a name="prerequisites"></a>Предварительные требования

* Распознавание речи Azure — ключ ресурса из 32 символов и URL-адреса конечной точки для разработки. Создайте их с помощью [портала Azure](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) или [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Импорт приложения [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) из репозитория GitHub cognitive-services-language-understanding.
* Идентификатор приложения LUIS для импортированного приложения TravelAgent. Идентификатор приложения отображается на панели мониторинга приложения.
* Идентификатор версии приложения, которое получает речевые фрагменты. Идентификатор по умолчанию — 0.1.
* Язык программирования [Node.js](https://nodejs.org/).
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Файл JSON с примерами высказываний.

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="change-model-programmatically"></a>Изменение модели программными средствами

1. Создайте файл с именем `model.js`. Добавьте следующий код:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');

    // 32 character key value
    const LUIS_authoringKey = "YOUR-KEY";

    // endpoint example: your-resource-name.api.cognitive.microsoft.com
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

1. Замените значения, начинающиеся с `YOUR-`, собственными значениями.

    |Сведения|Назначение|
    |--|--|
    |`YOUR-KEY`|Ключ для разработки (32 символа).|
    |`YOUR-ENDPOINT`| Конечная точка URL-адреса для разработки. Например, `replace-with-your-resource-name.api.cognitive.microsoft.com`. Имя ресурса задается при создании ресурса.|
    |`YOUR-APP-ID`| Идентификатор приложения LUIS. |

    Назначенные ключи и ресурсы отображаются на портале LUIS в разделе "Управление" на странице **ресурсов Azure**. Идентификатор приложения доступен в том же разделе "Управление" на странице **Параметры приложения**.

1. В командной строке каталога, в котором вы создали файл, введите следующую команду, чтобы запустить этот файл:

    ```console
    node model.js
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с этим кратким руководством удалите файл из файловой системы.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Рекомендации для приложения](../luis-concept-best-practices.md)