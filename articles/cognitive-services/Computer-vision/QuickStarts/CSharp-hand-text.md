---
title: Краткое руководство. Извлечение рукописного текста — REST, C#
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства вы узнаете, как, используя API компьютерного зрения, извлекать рукописный текст из изображения с помощью C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 160d42725abdb91af4f83696c2bceae38bbab67d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869336"
---
# <a name="quickstart-extract-handwritten-text-using-the-rest-api-and-c35-in-computer-vision"></a>Краткое руководство. Извлечению рукописного текста с помощью REST API компьютерного зрения и C#

Из этого краткого руководства вы узнаете, как извлечь рукописный текст из изображения с помощью REST API компьютерного зрения. С помощью методов [Recognize Text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) и [Get Recognize Text Operation Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) можно определить рукописный текст на изображении, а потом извлечь распознанные знаки в поток знаков, пригодный для машинной обработки.

> [!IMPORTANT]
> В отличие от метода [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) метод [Recognize Text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) выполняется асинхронно. Этот метод не возвращает никаких данных в текст успешного ответа. Вместо этого метод Recognize Text возвращает URI в значение поля заголовка ответа `Operation-Content`. Затем можно вызвать этот URI, который представляет метод [Get Recognize Text Operation Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201), чтобы проверить статус и вернуть результаты вызова метода распознавания текста.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

- У вас должна быть [Visual Studio 2015 или более поздней версии](https://visualstudio.microsoft.com/downloads/).
- У вас должен быть ключ подписки для Компьютерного зрения. Получение ключа подписки описано в статье [How to obtain subscription keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Получение ключей подписки).

## <a name="create-and-run-the-sample-application"></a>Создание и запуск примера приложения

Чтобы создать пример в Visual Studio, сделайте следующее:

1. Создайте решение Visual Studio в Visual Studio, используя шаблон консольного приложения Visual C#.
1. Установите пакет NuGet Newtonsoft.Json.
    1. В меню щелкните **Средства**, выберите **Диспетчер пакетов NuGet**, а затем **Управление пакетами NuGet для решения**.
    1. Перейдите на вкладку **Обзор** и в поле **Поиск** введите Newtonsoft.Json.
    1. Выберите **Newtonsoft.Json**, затем установите флажок рядом с именем проекта и щелкните **Установить**.
1. Замените код в файле `Program.cs` следующим кодом, а затем внесите в него следующие изменения (там, где это необходимо):
    1. Замените значение `subscriptionKey` своим ключом подписки.
    1. Замените значение `uriBase` URL-адресом конечной точки для метода [Recognize Text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) из региона Azure, где вы получили ключи подписки, если это необходимо.
1. Запустите программу.
1. Введите путь к локальному изображению в командной строке.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // You must use the same Azure region in your REST API method as you used to
        // get your subscription keys. For example, if you got your subscription keys
        // from the West US region, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the "westus" region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        const string uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/recognizeText";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Handwriting Recognition:");
            Console.Write(
                "Enter the path to an image with handwritten text you wish to read: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                ReadHandwrittenText(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the handwritten text from the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with handwritten text.</param>
        static async Task ReadHandwrittenText(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameter.
                string requestParameters = "mode=Handwritten";

                // Assemble the URI for the REST API method.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Two REST API methods are required to extract handwritten text.
                // One method to submit the image for processing, the other method
                // to retrieve the text found in the image.

                // operationLocation stores the URI of the second REST API method,
                // returned by the first REST API method.
                string operationLocation;

                // Reads the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Adds the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // The first REST API method, Recognize Text, starts
                    // the async process to analyze the written text in the image.
                    response = await client.PostAsync(uri, content);
                }

                // The response header for the Recognize Text method contains the URI
                // of the second method, Get Recognize Text Operation Result, which
                // returns the results of the process in the response body.
                // The Recognize Text operation does not return anything in the response body.
                if (response.IsSuccessStatusCode)
                    operationLocation =
                        response.Headers.GetValues("Operation-Location").FirstOrDefault();
                else
                {
                    // Display the JSON error data.
                    string errorString = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("\n\nResponse:\n{0}\n",
                        JToken.Parse(errorString).ToString());
                    return;
                }

                // If the first REST API method completes successfully, the second 
                // REST API method retrieves the text written in the image.
                //
                // Note: The response may not be immediately available. Handwriting
                // recognition is an asynchronous operation that can take a variable
                // amount of time depending on the length of the handwritten text.
                // You may need to wait or retry this operation.
                //
                // This example checks once per second for ten seconds.
                string contentString;
                int i = 0;
                do
                {
                    System.Threading.Thread.Sleep(1000);
                    response = await client.GetAsync(operationLocation);
                    contentString = await response.Content.ReadAsStringAsync();
                    ++i;
                }
                while (i < 10 && contentString.IndexOf("\"status\":\"Succeeded\"") == -1);

                if (i == 10 && contentString.IndexOf("\"status\":\"Succeeded\"") == -1)
                {
                    Console.WriteLine("\nTimeout error.\n");
                    return;
                }

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n\n{0}\n",
                    JToken.Parse(contentString).ToString());
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            // Open a read-only file stream for the specified file.
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                // Read the file's contents into a byte array.
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="examine-the-response"></a>Изучите ответ.

Успешный ответ будет возвращен в формате JSON. После этого запустится синтаксический анализ примера приложения и в окне консоли отобразится успешный ответ, аналогичный следующему.

```json
{
    "status": "Succeeded",
    "recognitionResult": {
        "lines": [
            {
                "boundingBox": [
                    99,
                    195,
                    1309,
                    45,
                    1340,
                    292,
                    130,
                    442
                ],
                "text": "when you write them down",
                "words": [
                    {
                        "boundingBox": [
                            152,
                            191,
                            383,
                            154,
                            341,
                            421,
                            110,
                            458
                        ],
                        "text": "when"
                    },
                    {
                        "boundingBox": [
                            436,
                            145,
                            607,
                            118,
                            565,
                            385,
                            394,
                            412
                        ],
                        "text": "you"
                    },
                    {
                       "boundingBox": [
                            644,
                            112,
                            873,
                            76,
                            831,
                            343,
                            602,
                            379
                        ],
                        "text": "write"
                    },
                    {
                        "boundingBox": [
                            895,
                            72,
                            1092,
                            41,
                            1050,
                            308,
                            853,
                            339
                        ],
                        "text": "them"
                    },
                    {
                        "boundingBox": [
                            1140,
                            33,
                            1400,
                            0,
                            1359,
                            258,
                            1098,
                            300
                        ],
                        "text": "down"
                    }
                ]
            },
            {
                "boundingBox": [
                    142,
                    222,
                    1252,
                    62,
                    1269,
                    180,
                    159,
                    340
                ],
                "text": "You remember things better",
                "words": [
                    {
                        "boundingBox": [
                            140,
                            223,
                            267,
                            205,
                            288,
                            324,
                            162,
                            342
                        ],
                        "text": "You"
                    },
                    {
                        "boundingBox": [
                            314,
                            198,
                            740,
                            137,
                            761,
                            256,
                            335,
                            317
                        ],
                        "text": "remember"
                    },
                    {
                        "boundingBox": [
                            761,
                            134,
                            1026,
                            95,
                            1047,
                            215,
                            782,
                            253
                        ],
                        "text": "things"
                    },
                    {
                        "boundingBox": [
                            1046,
                            92,
                            1285,
                            58,
                            1307,
                            177,
                            1068,
                            212
                        ],
                        "text": "better"
                    }
                ]
            },
            {
                "boundingBox": [
                    155,
                    405,
                    537,
                    338,
                    557,
                    449,
                    175,
                    516
                ],
                "text": "by hand",
                "words": [
                    {
                        "boundingBox": [
                            146,
                            408,
                            266,
                            387,
                            301,
                            495,
                            181,
                            516
                        ],
                        "text": "by"
                    },
                    {
                        "boundingBox": [
                            290,
                            383,
                            569,
                            334,
                            604,
                            443,
                            325,
                            491
                        ],
                        "text": "hand"
                    }
                ]
            }
        ]
    }
}
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите решение Visual Studio, если оно больше не требуется. Чтобы сделать это, откройте проводник, перейдите в папку, в которой вы создали решение Visual Studio, и удалите эту папку.

## <a name="next-steps"></a>Дополнительная информация

Изучите базовое приложение Windows, в котором используется API компьютерного зрения для оптического распознавания символов и создания интеллектуально обрезанных эскизов, а также для обнаружения, классификации, добавления тегов и описания визуальных признаков изображения, включая лица. Для быстрых экспериментов с API-интерфейсами компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Руководство по API компьютерного зрения для C&#35;](../Tutorials/CSharpTutorial.md)
