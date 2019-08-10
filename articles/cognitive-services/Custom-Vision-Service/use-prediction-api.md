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
ms.openlocfilehash: 22955ba4b885b264210dc8788f2a410b785b28b4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883970"
---
# <a name="use-your-model-with-the-prediction-api"></a>Использование модели с API-интерфейсом прогнозирования

После обучения модели можно протестировать изображения программно, отправив их в конечную точку API прогнозирования.

> [!NOTE]
> В этом документе описано, как использовать C# для отправки изображения в API прогнозирования. Дополнительные сведения и примеры см. в справочнике по [API прогнозирования](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Опубликуйте обученную итерацию

На [веб-странице Пользовательской службы визуального распознавания](https://customvision.ai) выберите проект и перейдите на вкладку __Производительность__ .

Чтобы отправить изображения в API прогнозирования, сначала необходимо опубликовать итерацию для прогнозирования, что можно сделать, выбрав __опубликовать__ и указав имя опубликованной итерации. Это сделает вашу модель доступной для API-интерфейса прогнозирования Пользовательское визуальное распознавание ресурса Azure.

![На вкладке "производительность" отображается красный прямоугольник, окружающий кнопку "опубликовать".](./media/use-prediction-api/unpublished-iteration.png)

После успешной публикации модели на боковой панели слева появится метка "опубликованная", и ее имя появится в описании итерации.

![Вкладка производительность отображается с красным прямоугольником, окружающим опубликованную метку, и именем опубликованной итерации.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Получение URL-адреса и ключа прогнозирования

После публикации модели можно получить необходимые сведения, выбрав __прогнозирующий URL-адрес__ . Откроется диалоговое окно со сведениями об использовании API прогнозирования, включая прогнозирующий __URL-адрес__ и __ключ прогноза__ .

![На вкладке "производительность" отображается красный прямоугольник, окружающий кнопку "прогнозирующий URL-адрес".](./media/use-prediction-api/published-iteration-prediction-url.png)

![Вкладка "производительность" отображается с красным прямоугольником, окружающим значение прогнозирующего URL-адреса для использования файла изображения и значения прогнозирующего ключа.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> __Ключ прогноза__ также можно найти на странице [портал Azure](https://portal.azure.com) для ресурса пользовательское визуальное распознавание Azure, связанного с проектом, в колонке __ключи__ .

В этом разделе вы будете использовать локальный образ, поэтому скопируйте URL-адрес в разделе **при наличии файла изображения** во временном расположении. Скопируйте также и соответствующее значение __ключа прогноза__ .

## <a name="create-the-application"></a>Создание приложения

1. В Visual Studio создайте новое C# консольное приложение.

1. Используйте следующий код в файле __Program.cs__ :

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
   * Присвойте `namespace` полю имя проекта.
   * Замените заполнитель `<Your prediction key>` значением ключа, полученным ранее.
   * Замените заполнитель `<Your prediction URL>` URL-адресом, полученным ранее.

## <a name="run-the-application"></a>Выполнение приложения

При запуске приложения вам будет предложено ввести путь к файлу изображения в консоли. Затем изображение отправляется в API-интерфейс прогнозирования, и результаты прогноза возвращаются в виде строки в формате JSON. Ниже приведен пример ответа.

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

## <a name="next-steps"></a>Следующие шаги

В этом руководство вы узнали, как отправлять изображения в классификатор или детектор пользовательского образа, а также как получить ответ программным путем с C# помощью пакета SDK. Далее вы узнаете, как выполнять комплексные сценарии с C#или приступить к работе с другим ЯЗЫКОВым пакетом SDK.

* [Краткое руководство. пакет SDK для .NET](csharp-tutorial.md)
* [Краткое руководство Пакет SDK для Python](python-tutorial.md)
* [Краткое руководство Пакет SDK для Java](java-tutorial.md)
* [Краткое руководство Пакет SDK для Node](node-tutorial.md)
* [Краткое руководство Пакет SDK для Go](go-tutorial.md)
