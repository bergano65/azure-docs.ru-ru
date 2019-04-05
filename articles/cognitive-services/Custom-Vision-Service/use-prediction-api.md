---
title: использование конечной точки прогноза для программного тестирования изображений с помощью классификатора — Пользовательская служба визуального распознавания
titlesuffix: Azure Cognitive Services
description: Сведения о том, как использовать API для программного тестирования изображений с помощью классификатора Пользовательской службы визуального распознавания.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 1ee6edbf49bbcd2014afcf29ed3b737168a3b5bc
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046076"
---
# <a name="use-your-model-with-the-prediction-api"></a>Использование модели с помощью API-интерфейса прогнозирования

После после обучения модели, можно протестировать образы программными средствами, отправив их к конечной точке API прогноза.

> [!NOTE]
> В этом документе описано, как использовать C# для отправки изображения в API прогнозирования. Дополнительные сведения и примеры см. в разделе [Справочник по API прогнозирования](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Публикация обученной итерации

На [веб-странице Пользовательской службы визуального распознавания](https://customvision.ai) выберите проект и перейдите на вкладку __Производительность__.

Для отправки изображений в API прогнозирования, необходимо сначала опубликовать итерации для прогнозирования, это можно сделать, выбрав __публикации__ и указав имя опубликованного итерации. Это будет сделать доступным для ресурса Custom Vision Azure API прогноза модели.

![Вкладка "производительность" отображается с красным прямоугольником вокруг кнопки «публикация».](./media/use-prediction-api/unpublished-iteration.png)

После успешной публикации модели вы увидите метку «Опубликовано» отображаются рядом с итерации на боковой панели слева, а его имя появится в описании итерации.

![Вкладка "производительность" отображается с красным прямоугольником вокруг метки опубликовано и имя опубликованного итерации.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Получение URL-адреса и ключа прогнозирования

После публикации модели можно получить необходимую информацию, выбрав __прогноза URL-адрес__. Откроется диалоговое окно с информацией для использования API прогноза, включая __URL-адрес прогноза__ и __прогноза ключ__.

![Вкладка "производительность" отображается с красным прямоугольником вокруг кнопки прогноза URL-адрес.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Вкладка "производительность" отображается с красным прямоугольником вокруг URL-адрес прогноза, для использования в файл изображения и значения прогноза ключа.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Ваш __прогноза ключ__ также можно найти в [портала Azure](https://portal.azure.com) странице для компьютерного зрения Azure настраиваемого ресурса, связанного с проектом, в разделе __ключи__ колонке.

В этом руководстве, необходимо использовать локальный образ, поэтому скопируйте URL-адрес в разделе **при наличии файла изображения** во временную папку. Скопируйте соответствующий __прогноза ключ__ также значение.

## <a name="create-the-application"></a>Создание приложения

1. В Visual Studio создайте новое C# консольное приложение.

1. Используйте следующий код в файле __Program.cs__:

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Измените следующие сведения:
   * Задайте `namespace` на имя вашего проекта.
   * Замените заполнитель `<Your prediction key>` со значением ключа, полученным ранее.
   * Замените заполнитель `<Your prediction URL>` с URL-адресом, полученным ранее.

## <a name="run-the-application"></a>Выполнение приложения

При запуске приложения, вам будет предложено ввести путь к файлу изображения в консоли. Изображение затем отправляются в API прогнозирования и результаты прогнозирования возвращаются в виде строки в формате JSON. Ниже приведен пример ответа на запрос.

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как отправлять изображения для пользовательского образа классификатора/средства обнаружения и получение ответа программным образом с помощью C# пакета SDK. Узнайте, как для выполнения сценариев end-to-end с C#, или приступить к использованию на другом языке SDK.

* [Краткое руководство: Пакет SDK для .NET](csharp-tutorial.md)
* [Краткое руководство. Пакет SDK для Python](python-tutorial.md)
* [Краткое руководство. Пакет SDK для Java](java-tutorial.md)
* [Краткое руководство. Пакет SDK для Node](node-tutorial.md)
* [Краткое руководство. Пакет SDK для GO](go-tutorial.md)
