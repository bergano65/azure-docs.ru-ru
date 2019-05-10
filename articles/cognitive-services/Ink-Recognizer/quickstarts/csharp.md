---
title: Краткое руководство. Распознавать рукописный ввод с помощью REST API распознавания рукописного ввода иC#
description: Используйте API распознавания рукописного ввода для запуска, распознавая цифровых рукописных штрихов.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 9bb9c23cc1f807cae1d0d22f1652e8f4408f1f91
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65518669"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Краткое руководство. Распознавать рукописный ввод с помощью REST API распознавания рукописного ввода иC#

Используйте в этом кратком руководстве, чтобы начать отправку цифровых рукописных штрихов API распознавания рукописного ввода. Это C# приложение отправляет запрос API, содержащий данные рукописного ввода на формате JSON и возвращает ответ.

Хотя это приложение создается на языке C#, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

Обычно будет вызов API из цифрового рукописного ввода приложения. В этом кратком руководстве отправляет данные штрихов рукописного ввода для следующего примера рукописных из JSON-файла.

![изображение рукописного текста](../media/handwriting-sample.jpg)

Исходный код для этого краткого руководства можно найти на портале [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Технические условия

- Любой выпуск [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Установка Newtonsoft.Json в виде пакета NuGet в Visual studio:
        1. Щелкните правой кнопкой мыши **Solution Manager**
        2. Щелкните **Manage NuGet Packages...**
        3. Поиск `Newtonsoft.Json` и установить пакет
- Если вы используете Linux и Mac OS, это приложение можно запускать с помощью [Mono](https://www.mono-project.com/).

- Данные stroke рукописного ввода для примера в этом кратком руководстве можно найти на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


## <a name="create-a-new-application"></a>Создание приложения

1. В Visual Studio создайте новое решение консоли и добавьте следующие пакеты. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. Создайте переменные для ключ подписки и конечной точки. Ниже приведен URI, который можно использовать для распознавания рукописного ввода. Он будет добавляться к конечной точке службы позднее для создания API URL-адрес запроса.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Создание функции для отправки запросов

1. Создайте новую функцию async `Request` , принимающий переменные, созданные выше.

2. Задать протокол безопасности и сведения заголовка с помощью клиента `HttpClient` объекта. Не забудьте добавить ключ подписки для `Ocp-Apim-Subscription-Key` заголовка. Затем создайте `StringContent` для запроса.
 
3. Отправка запроса с `PutAsync()`. Если запрос выполнен успешно, возвращает ответ.  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Отправить запрос распознавания рукописного ввода

1. Создайте новую функцию `recognizeInk()`. Создать запрос и отправить его, вызвав `Request()` функцию с конечной точкой, ключ подписки, URL-адрес API и данные рукописного ввода.

2. Десериализовать объект JSON и вывести его на консоль. 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>После загрузки данных рукописного ввода

Создание функции с именем `LoadJson()` загрузить JSON-файл данных рукописного ввода. Используйте `StreamReader` и `JsonTextReader` для создания `JObject` и вернуть его.
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>Отправка запроса API

1. В методе main приложения загрузки данных JSON с помощью функции, созданной ранее. 

2. Вызовите `recognizeInk()` функции, созданной ранее. Используйте `System.Console.ReadKey()` для не закрывайте окно консоли после запуска приложения.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Запустите приложение и просмотреть ответ

Запустите приложение. Успешный ответ возвращается в формате JSON. Можно также найти ответ JSON на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по REST API](https://go.microsoft.com/fwlink/?linkid=2089907)


Чтобы увидеть, как API Распознавателя рукописного текста работает в приложении для рукописного ввода, взгляните на следующие примеры приложений на GitHub.
* [C# и универсальная платформа Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# и Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Приложение браузера Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Мобильное приложение для Java и Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Мобильное приложение для Swift и iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
