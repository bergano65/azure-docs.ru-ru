---
title: Получение сведений о намерении с помощью вызова REST и Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: 5b3cf31fd5388c1d558726ab7c01b9946e826c23
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654332"
---
## <a name="prerequisites"></a>Предварительные требования

* [JDK SE](https://aka.ms/azure-jdks) (комплект разработчика Java, выпуск "Стандартный");
* [Visual Studio Code](https://code.visualstudio.com/) или привычный вам редактор кода;

## <a name="create-pizza-app"></a>Создание приложения Pizza

[!INCLUDE [Create pizza app](get-started-get-intent-create-pizza-app.md)]

## <a name="get-intent-programmatically"></a>Получение намерения программным способом

Используйте Java, чтобы запросить [конечную точку прогноза](https://aka.ms/luis-apim-v3-prediction) и получить результат прогноза.

1. Создайте новую папку для размещения проекта Java, например `java-predict-with-rest`.

1. Создайте подкаталог `lib` и скопируйте в подкаталог `lib` следующие библиотеки Java:

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar);
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar);
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar).

1. Скопируйте следующий код для создания класса в файл с именем `Predict.java`:

    ```java
    import java.io.*;
    import java.net.URI;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;

    // To compile, execute this command at the console:
    //      Windows: javac -cp ";lib/*" Predict.java
    //      macOs: javac -cp ":lib/*" Predict.java
    //      Linux: javac -cp ":lib/*" Predict.java

    // To run, execute this command at the console:
    //      Windows: java -cp ";lib/*" Predict
    //      macOs: java -cp ":lib/*" Predict
    //      Linux: java -cp ":lib/*" Predict

    public class Predict {

        public static void main(String[] args)
        {
            HttpClient httpclient = HttpClients.createDefault();

            try
            {
                //////////
                // Values to modify.

                // YOUR-APP-ID: The App ID GUID found on the www.luis.ai Application Settings page.
                String AppId = "93066630-3523-4df6-b05f-2b6cd9d46ea1";

                // YOUR-PREDICTION-KEY: Your LUIS authoring key, 32 character value.
                String Key = "9c0c8b2196ae4f95818b006b4de05cc7";

                // YOUR-PREDICTION-ENDPOINT: Replace this with your authoring key endpoint.
                // For example, "https://westus.api.cognitive.microsoft.com/"
                String Endpoint = "https://westus.api.cognitive.microsoft.com/";

                // The utterance you want to use.
                String Utterance = "I want two large pepperoni pizzas on thin crust please";
                //////////

                // Begin building the endpoint URL.
                URIBuilder endpointURLbuilder = new URIBuilder(Endpoint + "luis/prediction/v3.0/apps/" + AppId + "/slots/production/predict?");

                // Create the query string params.
                endpointURLbuilder.setParameter("query", Utterance);
                endpointURLbuilder.setParameter("subscription-key", Key);
                endpointURLbuilder.setParameter("show-all-intents", "true");
                endpointURLbuilder.setParameter("verbose", "true");

                // Create the prediction endpoint URL.
                URI endpointURL = endpointURLbuilder.build();

                // Create the HTTP object from the URL.
                HttpGet request = new HttpGet(endpointURL);

                // Access the LUIS endpoint to analyze the text utterance.
                HttpResponse response = httpclient.execute(request);

                // Get the response.
                HttpEntity entity = response.getEntity();

                // Print the response on the console.
                if (entity != null)
                {
                    System.out.println(EntityUtils.toString(entity));
                }
            }

            // Display errors if they occur.
            catch (Exception e)
            {
                System.out.println(e.getMessage());
            }
        }
    }
    ```

1. Замените значения `YOUR-KEY` и `YOUR-ENDPOINT` собственным ключом и конечной точкой **среды выполнения** прогнозирования.

    |Сведения|Назначение|
    |--|--|
    |`YOUR-APP-ID`|Идентификатор приложения. Находится на портале LUIS на странице "Application Settings" (Параметры приложения) для приложения.
    |`YOUR-PREDICTION-KEY`|Ваш ключ прогнозирования длиной в 32 символа. Находится на портале LUIS на странице "Azure Resources" (Ресурсы Azure) для приложения.
    |`YOUR-PREDICTION-ENDPOINT`| Конечная точка URL-адреса прогнозирования. Находится на портале LUIS на странице "Azure Resources" (Ресурсы Azure) для приложения.<br>Например, `https://westus.api.cognitive.microsoft.com/`.|

1. Скомпилируйте программу Java из командной строки:

    ::: zone pivot="client-operating-system-linux"

    ```console
    javac -cp ":lib/*" Predict.java
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-macos"

    ```console
    javac -cp ":lib/*" Predict.java
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-windows"

    ```console
    javac -cp ";lib/*" Predict.java
    ```

    ::: zone-end

1. Запустите программу Java из командной строки:

    ::: zone pivot="client-operating-system-linux"

    ```console
    java -cp ":lib/*" Predict
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-macos"

    ```console
    java -cp ":lib/*" Predict
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-windows"

    ```console
    java -cp ";lib/*" Predict
    ```

    ::: zone-end

1. Проверьте ответ прогноза, который возвращается в формате JSON:

    ```json
    {"query":"I want two large pepperoni pizzas on thin crust please","prediction":{"topIntent":"ModifyOrder","intents":{"ModifyOrder":{"score":1.0},"None":{"score":8.55E-09},"Greetings":{"score":1.82222226E-09},"CancelOrder":{"score":1.47272727E-09},"Confirmation":{"score":9.8125E-10}},"entities":{"Order":[{"FullPizzaWithModifiers":[{"PizzaType":["pepperoni pizzas"],"Size":[["Large"]],"Quantity":[2],"Crust":[["Thin"]],"$instance":{"PizzaType":[{"type":"PizzaType","text":"pepperoni pizzas","startIndex":17,"length":16,"score":0.9978157,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Size":[{"type":"SizeList","text":"large","startIndex":11,"length":5,"score":0.9984481,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Quantity":[{"type":"builtin.number","text":"two","startIndex":7,"length":3,"score":0.999770939,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Crust":[{"type":"CrustList","text":"thin crust","startIndex":37,"length":10,"score":0.933985531,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"$instance":{"FullPizzaWithModifiers":[{"type":"FullPizzaWithModifiers","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.90681237,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"ToppingList":[["Pepperoni"]],"$instance":{"Order":[{"type":"Order","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.9047088,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"ToppingList":[{"type":"ToppingList","text":"pepperoni","startIndex":17,"length":9,"modelTypeId":5,"modelType":"List Entity Extractor","recognitionSources":["model"]}]}}}}
    ```

    Ответ JSON, отформатированный для удобочитаемости:

    ```JSON
    {
      "query": "I want two large pepperoni pizzas on thin crust please",
      "prediction": {
        "topIntent": "ModifyOrder",
        "intents": {
          "ModifyOrder": {
            "score": 1
          },
          "None": {
            "score": 8.55e-9
          },
          "Greetings": {
            "score": 1.82222226e-9
          },
          "CancelOrder": {
            "score": 1.47272727e-9
          },
          "Confirmation": {
            "score": 9.8125e-10
          }
        },
        "entities": {
          "Order": [
            {
              "FullPizzaWithModifiers": [
                {
                  "PizzaType": [
                    "pepperoni pizzas"
                  ],
                  "Size": [
                    [
                      "Large"
                    ]
                  ],
                  "Quantity": [
                    2
                  ],
                  "Crust": [
                    [
                      "Thin"
                    ]
                  ],
                  "$instance": {
                    "PizzaType": [
                      {
                        "type": "PizzaType",
                        "text": "pepperoni pizzas",
                        "startIndex": 17,
                        "length": 16,
                        "score": 0.9978157,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Size": [
                      {
                        "type": "SizeList",
                        "text": "large",
                        "startIndex": 11,
                        "length": 5,
                        "score": 0.9984481,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Quantity": [
                      {
                        "type": "builtin.number",
                        "text": "two",
                        "startIndex": 7,
                        "length": 3,
                        "score": 0.999770939,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Crust": [
                      {
                        "type": "CrustList",
                        "text": "thin crust",
                        "startIndex": 37,
                        "length": 10,
                        "score": 0.933985531,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ]
                  }
                }
              ],
              "$instance": {
                "FullPizzaWithModifiers": [
                  {
                    "type": "FullPizzaWithModifiers",
                    "text": "two large pepperoni pizzas on thin crust",
                    "startIndex": 7,
                    "length": 40,
                    "score": 0.90681237,
                    "modelTypeId": 1,
                    "modelType": "Entity Extractor",
                    "recognitionSources": [
                      "model"
                    ]
                  }
                ]
              }
            }
          ],
          "ToppingList": [
            [
              "Pepperoni"
            ]
          ],
          "$instance": {
            "Order": [
              {
                "type": "Order",
                "text": "two large pepperoni pizzas on thin crust",
                "startIndex": 7,
                "length": 40,
                "score": 0.9047088,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ],
            "ToppingList": [
              {
                "type": "ToppingList",
                "text": "pepperoni",
                "startIndex": 17,
                "length": 9,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
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

Завершив работу с этим кратким руководством, удалите папку проекта из файловой системы.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Добавление высказываний и обучение на Java](../get-started-get-model-rest-apis.md)