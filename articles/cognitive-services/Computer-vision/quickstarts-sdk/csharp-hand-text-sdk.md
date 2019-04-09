---
title: Краткое руководство. Извлечение рукописного текста — SDK, C#
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства вы узнаете, как извлечь текст из изображения с помощью клиентской библиотеки API компьютерного зрения для Windows на C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2ae5cd0fd177f64bed5ed0705207c6a3e81a1b24
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878202"
---
# <a name="quickstart-extract-handwritten-text-using-the-computer-vision-c-sdk"></a>Краткое руководство. Извлечению рукописного текста с помощью пакета SDK Компьютерного зрения для C#

Из этого краткого руководства вы узнаете, как извлечь рукописный или печатный текст из изображения с помощью пакета SDK для API компьютерного зрения. Код из этого руководства можно скачать в виде готового примера приложения в репозитории GitHub [Cognitive Services Csharp Vision](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision).

## <a name="prerequisites"></a>Предварительные требования

* Чтобы использовать API компьютерного зрения, требуется ключ подписки. Его получение описано в статье [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Получение ключей подписки).
* Любой выпуск [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/).
* Пакет NuGet клиентской библиотеки [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Скачивать пакет не нужно. Инструкции по установке приведены ниже.

## <a name="create-and-run-the-sample-app"></a>Создание и запуск примера приложения

Чтобы выполнить наш пример, сделайте следующее:

1. Создайте консольное приложение Visual C# в Visual Studio.
1. Установите пакет NuGet клиентской библиотеки компьютерного зрения.
    1. В меню щелкните **Средства**, выберите **Диспетчер пакетов NuGet**, а затем **Управление пакетами NuGet для решения**.
    1. Нажмите кнопку **Обзор** и в поле **Поиск** введите Microsoft.Azure.CognitiveServices.Vision.ComputerVision.
    1. Выберите **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**, установите флажок рядом с именем проекта и щелкните **Установить**.
1. Замените `Program.cs` следующим кодом. Методы `BatchReadFileAsync` и `BatchReadFileInStreamAsync` создают программу-оболочку [API пакетного чтения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) для удаленных и локальных изображений соответственно. Метод `GetReadOperationResultAsync` служит оболочкой для [API получения результатов операции чтения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d).

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

    using System;
    using System.IO;
    using System.Threading.Tasks;

    namespace ExtractText
    {
        class Program
        {
            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<Subscription key>";

            // For printed text, change to TextRecognitionMode.Printed
            private const TextRecognitionMode textRecognitionMode =
                TextRecognitionMode.Handwritten;

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg";

            private const int numberOfCharsInOperationId = 36;

            static void Main(string[] args)
            {
                ComputerVisionClient computerVision = new ComputerVisionClient(
                    new ApiKeyServiceClientCredentials(subscriptionKey),
                    new System.Net.Http.DelegatingHandler[] { });

                // You must use the same region as you used to get your subscription
                // keys. For example, if you got your subscription keys from westus,
                // replace "westcentralus" with "westus".
                //
                // Free trial subscription keys are generated in the westcentralus
                // region. If you use a free trial subscription key, you shouldn't
                // need to change the region.

                // Specify the Azure region
                computerVision.Endpoint = "https://westus.api.cognitive.microsoft.com";

                Console.WriteLine("Images being analyzed ...");
                var t1 = ExtractRemoteTextAsync(computerVision, remoteImageUrl);
                var t2 = ExtractLocalTextAsync(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Read text from a remote image
            private static async Task ExtractRemoteTextAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                // Start the async process to read the text
                BatchReadFileHeaders textHeaders =
                    await computerVision.BatchReadFileAsync(
                        imageUrl, textRecognitionMode);

                await GetTextAsync(computerVision, textHeaders.OperationLocation);
            }

            // Recognize text from a local image
            private static async Task ExtractLocalTextAsync(
                ComputerVisionClient computerVision, string imagePath)
            {
                if (!File.Exists(imagePath))
                {
                    Console.WriteLine(
                        "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                    return;
                }

                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    // Start the async process to recognize the text
                    BatchReadFileInStreamHeaders textHeaders =
                        await computerVision.BatchReadFileInStreamAsync(
                            imageStream, textRecognitionMode);

                    await GetTextAsync(computerVision, textHeaders.OperationLocation);
                }
            }

            // Retrieve the recognized text
            private static async Task GetTextAsync(
                ComputerVisionClient computerVision, string operationLocation)
            {
                // Retrieve the URI where the recognized text will be
                // stored from the Operation-Location header
                string operationId = operationLocation.Substring(
                    operationLocation.Length - numberOfCharsInOperationId);

                Console.WriteLine("\nCalling GetHandwritingRecognitionOperationResultAsync()");
                ReadOperationResult result =
                    await computerVision.GetReadOperationResultAsync(operationId);

                // Wait for the operation to complete
                int i = 0;
                int maxRetries = 10;
                while ((result.Status == TextOperationStatusCodes.Running ||
                        result.Status == TextOperationStatusCodes.NotStarted) && i++ < maxRetries)
                {
                    Console.WriteLine(
                        "Server status: {0}, waiting {1} seconds...", result.Status, i);
                    await Task.Delay(1000);

                    result = await computerVision.GetReadOperationResultAsync(operationId);
                }

                // Display the results
                Console.WriteLine();
                var recResults = result.RecognitionResults;
                foreach (TextRecognitionResult recResult in recResults)
                {
                    foreach (Line line in recResult.Lines)
                    {
                        Console.WriteLine(line.Text);
                    }
                }
                Console.WriteLine();
            }
        }
    }
    ```

1. Замените `<Subscription Key>` действительным ключом подписки.
1. При необходимости замените `computerVision.Endpoint` регионом Azure, связанным с ключами подписки.
1. При необходимости задайте для `textRecognitionMode` значение `TextRecognitionMode.Printed`.
1. Замените `<LocalImage>` на путь и имя файла локального изображения.
1. При необходимости задайте `remoteImageUrl` другого изображения.
1. Запустите программу.

## <a name="examine-the-response"></a>Изучите ответ.

При успешной обработке возвращаются строки распознанного текста для каждого изображения.

```console
Calling GetHandwritingRecognitionOperationResultAsync()

Calling GetHandwritingRecognitionOperationResultAsync()
Server status: Running, waiting 1 seconds...
Server status: Running, waiting 1 seconds...

dog
The quick brown fox jumps over the lazy
Pack my box with five dozen liquor jugs
```

См. [Краткое руководство. Извлечение рукописного текста с помощью REST API компьютерного зрения и C#](../QuickStarts/CSharp-hand-text.md#examine-the-response), в котором показан пример необработанных выходных данных JSON из вызова API.

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с API-интерфейсами компьютерного зрения, которые позволяют анализировать изображения, обнаруживать знаменитостей и достопримечательности, создавать эскизы, извлекать печатный и рукописный текст.

> [!div class="nextstepaction"]
> [Сведения об API-интерфейсах Компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
