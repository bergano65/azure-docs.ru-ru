---
title: Получение прогноза с помощью вызова REST в C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 2d3a000040ff1b4f6e0ae548b578e8be014dc06a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414510"
---
## <a name="prerequisites"></a>Предварительные требования

* [.NET Core версий 2.2+](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)
* Идентификатор общедоступного приложения: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.

## <a name="get-luis-key"></a>Получение ключа LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent-programmatically"></a>Получение намерения программным способом

Используйте C# (.NET Core), чтобы запросить [конечную точку прогноза](https://aka.ms/luis-apim-v3-prediction) и получить результат прогноза.

1. Создайте консольное приложение для языка C# с проектом и именем папки `predict-with-rest`. 

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. Перейдите в только что созданный каталог `predict-with-rest` и установите необходимые зависимости с помощью следующих команд:  

    ```console
    cd predict-with-rest
    dotnet add package System.Net.Http
    ```

1. Откройте `Program.cs` в любой интегрированной среде разработки или редакторе. Затем замените `Program.cs` следующим кодом:
    
   ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    
    namespace predict_with_rest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // YOUR-KEY: for example, the starter key
                var key = "YOUR-KEY";
                
                // YOUR-ENDPOINT: example is westus2.api.cognitive.microsoft.com
                var endpoint = "YOUR-ENDPOINT";

                // //public sample app
                var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"; 
    
                var utterance = "turn on all lights";
    
                MakeRequest(key, endpoint, appId, utterance);
    
                Console.WriteLine("Hit ENTER to exit...");
                Console.ReadLine();
            }
            static async void MakeRequest(string key, string endpoint, string appId, string utterance)
            {
                var client = new HttpClient();
                var queryString = HttpUtility.ParseQueryString(string.Empty);
    
                // The request header contains your subscription key
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);
    
                // The "q" parameter contains the utterance to send to LUIS
                queryString["query"] = utterance;
    
                // These optional request parameters are set to their default values
                queryString["verbose"] = "true";
                queryString["show-all-intents"] = "true";
                queryString["staging"] = "false";
                queryString["timezoneOffset"] = "0";
    
                var endpointUri = String.Format("https://{0}/luis/prediction/v3.0/apps/{1}/slots/production/predict?query={2}", endpoint, appId, queryString);
    
                var response = await client.GetAsync(endpointUri);
    
                var strResponseContent = await response.Content.ReadAsStringAsync();
                
                // Display the JSON result from LUIS
                Console.WriteLine(strResponseContent.ToString());
            }
        }
    }

   ```

1. Замените следующие значения:

    * `YOUR-KEY` на ключ для начала работы.
    * `YOUR-ENDPOINT` на конечную точку. Например, `westus2.api.cognitive.microsoft.com`.

1. Создайте консольное приложение с помощью этой команды: 

    ```console
    dotnet build
    ```

1. Запустите консольное приложение. В выходных данных консоли отображается тот же код JSON, который вы видели ранее в окне браузера.

    ```console
    dotnet run
    ```

1. Проверьте ответ прогноза, который возвращается в формате JSON:

    ```console
    Hit ENTER to exit...
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