---
title: использование конечной точки прогноза для программного тестирования изображений с помощью классификатора — Пользовательская служба визуального распознавания
titleSuffix: Azure Cognitive Services
description: Сведения о том, как использовать API для программного тестирования изображений с помощью классификатора Пользовательской службы визуального распознавания.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: dcb12da680d70e1f0ce4cd763bee340bb3416c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169954"
---
# <a name="use-your-model-with-the-prediction-api"></a>Используйте свою модель с API прогнозирования

После обучения модели можно программно тестировать изображения, отправив их в конечную точку API прогнозирования.

> [!NOTE]
> В этом документе описано, как использовать C# для отправки изображения в API прогнозирования. Для получения дополнительной информации [Prediction API reference](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)и примеров см.

## <a name="publish-your-trained-iteration"></a>Опубликовать обученную итерацию

На [веб-странице Пользовательской службы визуального распознавания](https://customvision.ai) выберите проект и перейдите на вкладку __Производительность__.

Чтобы отправить изображения в API прогноза, сначала необходимо опубликовать свою итерацию для прогнозирования, что можно сделать, выбрав __Publish__ и указав имя для опубликованной итерации. Это сделает вашу модель доступной для API прогнозирования ресурса Custom Vision Azure.

![Отображается вкладка производительности с красным прямоугольником, окружающим кнопку «Публикация».](./media/use-prediction-api/unpublished-iteration.png)

Как только ваша модель будет успешно опубликована, рядом с итерацией в левой боковой панели появится метка «Опубликовано», и ее имя появится в описании итерации.

![Отображается вкладка производительности с красным прямоугольником, окружающим опубликованную метку и название опубликованной итерации.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Получение URL-адреса и ключа прогнозирования

После публикации модели можно получить необходимую информацию, выбрав __URL-адрес прогноза.__ Это откроет диалог с информацией для использования API прогнозирования, включая __URL-адрес прогнозирования__ и __prediction-Key.__

![Вкладка производительности отображается красным прямоугольником, окружающим кнопку «Прогноз» URL.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Вкладка производительности отображается красным прямоугольником, окружающим значение URL-адреса прогноза для использования файла изображения и значения Prediction-Key.](./media/use-prediction-api/prediction-api-info.png)


В этом руководстве вы будете использовать локальное изображение, поэтому скопируйте URL под **Если у вас есть файл изображения** во временном месте. Скопируйте и соответствующее значение __«Прогноз-ключ».__

## <a name="create-the-application"></a>Создание приложения

1. В Visual Studio создайте новое приложение для консолей с C.'

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
   * Установите `namespace` поле под название множества проекта.
   * Замените заполнитель `<Your prediction key>` ключевым значением, полученным ранее.
   * Замените заполнитель `<Your prediction URL>` на URL-адрес, полученный ранее.

## <a name="run-the-application"></a>Выполнение приложения

При запуске приложения вам предлагается ввести путь к файлу изображения в консоли. Затем изображение передается в API прогнозирования, а результаты прогнозирования возвращаются в виде строки формата JSON. Ниже приводится пример ответа.

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

В этом руководстве вы узнали, как отправлять изображения на ваш пользовательский классификатор изображения / детектор и получать ответ программно с C sDK. Далее, узнайте, как завершить сквозные сценарии с помощью C, или начать использовать другой язык SDK.

* [Быстрый запуск: .NET SDK](csharp-tutorial.md)
* [Быстрый запуск: Python SDK](python-tutorial.md)
* [Быстрый запуск: Java SDK](java-tutorial.md)
* [Быстрый запуск: Узла SDK](node-tutorial.md)
* [Быстрый запуск: Перейти SDK](go-tutorial.md)
