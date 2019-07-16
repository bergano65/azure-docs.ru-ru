---
title: Краткое руководство. Распознавание рукописного ввода с помощью REST API Распознавателя рукописного текста и C#
titleSuffix: Azure Cognitive Services
description: Используйте API Распознавателя рукописного текста, чтобы начать распознавать рукописный текст.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 6a1b4ab43a7d87ac1162a7f0a3556d6bc3bfbfab
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721226"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Краткое руководство. Распознавание рукописного ввода с помощью REST API Распознавателя рукописного текста и C#

Используйте сведения из этого краткого руководство, чтобы начать отправлять рукописные мазки в API Распознавателя рукописного текста. Это приложение C# отправляет запрос API, содержащий данные рукописного мазка в формате JSON, и возвращает ответ.

Хотя это приложение создается на языке C#, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

Обычно вы вызываете API из приложения для рукописного ввода. Этот краткое руководство отправляет данные рукописного мазка для следующего рукописного образца из JSON-файла.

![изображение рукописного текста](../media/handwriting-sample.jpg)

Исходный код для этого краткого руководства можно найти на портале [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Предварительные требования

- Любой выпуск [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Чтобы установить Newtonsoft.Json в качестве пакета NuGet в Visual Studio, сделайте следующее.
        1. Щелкните правой кнопкой мыши на **Solution Manager**.
        2. Щелкните **Manage NuGet Packages...**
        3. Выполните поиск пакета `Newtonsoft.Json` и установите его.
- Если вы используете Linux или MacOS, это приложение можно запустить с помощью [Mono](https://www.mono-project.com/).

- Пример данных росчерка пера для этого краткого руководства можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


## <a name="create-a-new-application"></a>Создание приложения

1. В Visual Studio создайте новое консольное решение и добавьте следующие пакеты. 

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

2. Создайте переменные для ключа подписки и конечной точки. Далее приведен универсальный код ресурса (URI), который можно использовать для распознавания рукописного ввода. Для создания URL-адреса запроса API он будет добавлен в конечную точку службы позднее.

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

1. Создайте новую асинхронную функцию с именем `Request`, принимающую переменные, созданные выше.

2. Установите протокол безопасности клиента и информацию заголовка с помощью объекта `HttpClient`. Не забудьте добавить ключ подписки в заголовок `Ocp-Apim-Subscription-Key`. Затем создайте объект `StringContent` для запроса.
 
3. Отправьте запрос с помощью `PutAsync()`. Отправьте ответ при успешном выполнении запроса.  
    
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

## <a name="send-an-ink-recognition-request"></a>Отправка запроса на распознавание рукописного текста

1. Создайте новую функцию с именем `recognizeInk()`. Создайте запрос, в которой будет включена конечная точка, ключ подписки, URL-адрес API и данные мазка кисты рукописного ввода, а затем отправьте его. Чтобы это сделать используйте функцию `Request()`.

2. Десериализируйте объект JSON и выведите его в консоль. 
    
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

## <a name="load-your-digital-ink-data"></a>Загрузка данных рукописного ввода

Чтобы загрузить файл JSON с данных рукописного ввода создайте функцию `LoadJson()`. Используйте `StreamReader` и `JsonTextReader` чтобы создать `JObject` и вернуть его.
    
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

1. В методе main в приложении необходимо использовать созданную ранее функцию для загрузки данных JSON. 

2. Вызовите созданную ранее функцию `recognizeInk()`. Чтобы после выполнения приложения оставить консоль открытой, используйте `System.Console.ReadKey()`.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Запуск приложения и просмотр ответа

Запустите приложение. Успешный ответ возвращается в формате JSON. Ответ JSON также можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API](https://go.microsoft.com/fwlink/?linkid=2089907)


Чтобы увидеть, как API Распознавателя рукописного текста работает в приложении для рукописного ввода, взгляните на следующие примеры приложений на GitHub.
* [C# и универсальная платформа Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# и Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Приложение браузера Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Мобильное приложение для Java и Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Мобильное приложение для Swift и iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
