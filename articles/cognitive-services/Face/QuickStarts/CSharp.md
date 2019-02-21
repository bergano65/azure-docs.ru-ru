---
title: Краткое руководство. Обнаружение лиц на изображении с помощью REST API и C#
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как определить лица на изображении с помощью REST API распознавания лиц Azure и C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 02/06/2019
ms.author: pafarley
ms.openlocfilehash: 8ccc5664ab06fe23266915196b98a8dac4f71880
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311925"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>Краткое руководство. Обнаружение лиц на изображении с помощью REST API распознавания лиц и C#

В этом кратком руководстве описано, как определить лица на изображении с помощью REST API распознавания лиц Azure и C#.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

## <a name="prerequisites"></a>Предварительные требования

- Ключ подписки на API распознавания лиц. Вы можете получить ключ бесплатной пробной подписки на странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Или следуйте инструкциям в руководстве по [созданию учетной записи Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), чтобы получить подписку на API распознавания лиц и свой ключ.
- Любой выпуск [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Создание проекта Visual Studio

1. В Visual Studio создайте проект **Консольное приложение (.NET Framework)** и назовите его **FaceDetection**. 
1. При наличии других проектов в решении выберите этот в качестве единого запускаемого проекта.

## <a name="add-face-detection-code"></a>Добавление кода определения лиц

Откройте файл *Program.cs* нового проекта. Здесь вы добавите код, необходимый для загрузки изображений и определения лиц.

### <a name="include-namespaces"></a>Включение пространства имен

Добавьте следующие инструкции `using` в начало файла *Program.cs*.

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
```

### <a name="add-essential-fields"></a>Добавление важных полей

Добавьте следующие поля в класс **Program** . Они указывают, как подключиться к службе распознавания лиц и где получить входные данные. Вам нужно будет обновить поле `subscriptionKey`, указав значение вашего ключа подписки, и изменить строку `uriBase`, чтобы она содержала идентификатор правильного региона (список конечных точек для всех регионов см. в документации по [API распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)).


```csharp
// Replace <Subscription Key> with your valid subscription key.
const string subscriptionKey = "<Subscription Key>";

// NOTE: You must use the same region in your REST call as you used to
// obtain your subscription keys. For example, if you obtained your
// subscription keys from westus, replace "westcentralus" in the URL
// below with "westus".
//
// Free trial subscription keys are generated in the "westus" region.
// If you use a free trial subscription key, you shouldn't need to change
// this region.
const string uriBase =
    "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";
```

### <a name="receive-image-input"></a>Получение входного изображения

Добавьте следующий код в метод **Main** класса **Program**. В этом коде запрограммировано отображение в консоли приглашения, запрашивающего пользователя ввести URL-адрес изображения. Затем код вызывает другой метод, **MakeAnalysisRequest**, для обработки изображения в этом расположении.

```csharp
// Get the path and filename to process from the user.
Console.WriteLine("Detect faces:");
Console.Write(
    "Enter the path to an image with faces that you wish to analyze: ");
string imageFilePath = Console.ReadLine();

if (File.Exists(imageFilePath))
{
    try
    {
        MakeAnalysisRequest(imageFilePath);
        Console.WriteLine("\nWait a moment for the results to appear.\n");
    }
    catch (Exception e)
    {
        Console.WriteLine("\n" + e.Message + "\nPress Enter to exit...\n");
    }
}
else
{
    Console.WriteLine("\nInvalid file path.\nPress Enter to exit...\n");
}
Console.ReadLine();
```

### <a name="call-the-face-detection-rest-api"></a>Вызов REST API распознавания лиц

Добавьте следующий метод в класс **Program**. Он создает вызов REST к API распознавания лиц, чтобы определить информацию о лицах на удаленном изображении (строка `requestParameters` указывает, какие атрибуты лица нужно получить). Затем он записывает выходные данные в строку JSON.

Вы определите вспомогательные методы на следующих этапах.

```csharp
// Gets the analysis of the specified image by using the Face REST API.
static async void MakeAnalysisRequest(string imageFilePath)
{
    HttpClient client = new HttpClient();

    // Request headers.
    client.DefaultRequestHeaders.Add(
        "Ocp-Apim-Subscription-Key", subscriptionKey);

    // Request parameters. A third optional parameter is "details".
    string requestParameters = "returnFaceId=true&returnFaceLandmarks=false" +
        "&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses," +
        "emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

    // Assemble the URI for the REST API Call.
    string uri = uriBase + "?" + requestParameters;

    HttpResponseMessage response;

    // Request body. Posts a locally stored JPEG image.
    byte[] byteData = GetImageAsByteArray(imageFilePath);

    using (ByteArrayContent content = new ByteArrayContent(byteData))
    {
        // This example uses content type "application/octet-stream".
        // The other content types you can use are "application/json"
        // and "multipart/form-data".
        content.Headers.ContentType =
            new MediaTypeHeaderValue("application/octet-stream");

        // Execute the REST API call.
        response = await client.PostAsync(uri, content);

        // Get the JSON response.
        string contentString = await response.Content.ReadAsStringAsync();

        // Display the JSON response.
        Console.WriteLine("\nResponse:\n");
        Console.WriteLine(JsonPrettyPrint(contentString));
        Console.WriteLine("\nPress Enter to exit...");
    }
}
```

### <a name="process-the-input-image-data"></a>Обработка данных входных изображений

Добавьте следующий метод в класс **Program**. Он преобразует изображение по указанному URL-адресу в массив байтов.

```csharp
// Returns the contents of the specified file as a byte array.
static byte[] GetImageAsByteArray(string imageFilePath)
{
    using (FileStream fileStream =
        new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
    {
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }
}
```

### <a name="parse-the-json-response"></a>Проанализируйте ответ JSON.

Добавьте следующий метод в класс **Program**. Он преобразует входные данные JSON в более удобный для чтения формат. Ваше приложение будет записывать эти строковые данные в консоль.

```csharp
// Formats the given JSON string by adding line breaks and indents.
static string JsonPrettyPrint(string json)
{
    if (string.IsNullOrEmpty(json))
        return string.Empty;

    json = json.Replace(Environment.NewLine, "").Replace("\t", "");

    StringBuilder sb = new StringBuilder();
    bool quote = false;
    bool ignore = false;
    int offset = 0;
    int indentLength = 3;

    foreach (char ch in json)
    {
        switch (ch)
        {
            case '"':
                if (!ignore) quote = !quote;
                break;
            case '\'':
                if (quote) ignore = !ignore;
                break;
        }

        if (quote)
            sb.Append(ch);
        else
        {
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', ++offset * indentLength));
                    break;
                case '}':
                case ']':
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', --offset * indentLength));
                    sb.Append(ch);
                    break;
                case ',':
                    sb.Append(ch);
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', offset * indentLength));
                    break;
                case ':':
                    sb.Append(ch);
                    sb.Append(' ');
                    break;
                default:
                    if (ch != ' ') sb.Append(ch);
                    break;
            }
        }
    }

    return sb.ToString().Trim();
}
```

## <a name="run-the-app"></a>Запуск приложения

При успешном ответе данные распознавания лиц отобразятся в легко читаемом формате JSON. Например: 

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
            "neutral": 0.986,
            "sadness": 0.009,
            "surprise": 0.005
         },
         "blur": {
            "blurLevel": "low",
            "value": 0.06
         },
         "exposure": {
            "exposureLevel": "goodExposure",
            "value": 0.67
         },
         "noise": {
            "noiseLevel": "low",
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
               },
               {
                  "color": "black",
                  "confidence": 0.87
               },
               {
                  "color": "other",
                  "confidence": 0.51
               },
               {
                  "color": "blond",
                  "confidence": 0.08
               },
               {
                  "color": "red",
                  "confidence": 0.08
               },
               {
                  "color": "gray",
                  "confidence": 0.02
               }
            ]
         }
      }
   }
]
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали простое консольное приложение .NET, которое использует вызовы REST с API распознавания лиц Azure для определения лиц на изображении и возврата их атрибутов. Затем ознакомьтесь со справочной документацией по API распознавания лиц, чтобы узнать больше о поддерживаемых сценариях.

> [!div class="nextstepaction"]
> [API распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
