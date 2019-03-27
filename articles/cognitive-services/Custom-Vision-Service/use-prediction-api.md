---
title: 'Пример: использование конечной точки прогноза для программного тестирования изображений с помощью классификатора — Пользовательская служба визуального распознавания'
titlesuffix: Azure Cognitive Services
description: Сведения о том, как использовать API для программного тестирования изображений с помощью классификатора Пользовательской службы визуального распознавания.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 03/26/2019
ms.author: anroth
ms.openlocfilehash: 715fa526c83608c9922315e3a0d89b67b31e0d16
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472733"
---
#  <a name="use-your-model-with-the-prediction-api"></a>Использование модели с помощью API-интерфейса прогнозирования

После обучения модели можно протестировать изображения программными средствами, отправив их в API прогнозирования.

> [!NOTE]
> В этом документе описано, как использовать C# для отправки изображения в API прогнозирования. См. дополнительные сведения и примеры использования [API прогнозирования](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Публикация обученной итерации

На [веб-странице Пользовательской службы визуального распознавания](https://customvision.ai) выберите проект и перейдите на вкладку __Производительность__.

Для отправки изображений в API прогнозирования, необходимо сначала опубликовать итерации для прогнозирования, это можно сделать, выбрав __публикации__ и указав имя опубликованного итерации. Это позволит модели могут быть недоступны в API прогнозирования ресурса Custom Vision Azure. 

![Вкладка "производительность" отображается с красным прямоугольником вокруг кнопки «публикация».](./media/use-prediction-api/unpublished-iteration.png)

После успешной публикации модели вы увидите, что отображаются рядом с итерации в боковой панели слева, а также имя опубликованного итерации в описании итерации метку «Опубликовано».

![Вкладка "производительность" отображается с красным прямоугольником вокруг метки опубликовано и имя опубликованного итерации.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Получение URL-адреса и ключа прогнозирования

После публикации модели можно получить сведения об использовании API прогнозирования, выбрав __прогноза URL-адрес__. Откроется диалоговое окно, как показано ниже, с информацией для использования API прогноза, включая __URL-адрес прогноза__ и __прогноза ключ__.

![Вкладка "производительность" отображается с красным прямоугольником вокруг кнопки прогноза URL-адрес.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Вкладка "производительность" отображается с красным прямоугольником вокруг URL-адрес прогноза, для использования в файл изображения и значения прогноза ключа.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Ваш __прогноза ключ__ также можно найти в [портал Azure](https://portal.azure.com) странице для компьютерного зрения Azure настраиваемого ресурса, связанного в проект, в разделе __ключи__. 

В диалоговом окне скопируйте следующие сведения для использования в приложении:

* __URL-адрес прогноза__ по использованию __файл изображения__.
* __Ключ прогноза__ значение.

## <a name="create-the-application"></a>Создание приложения

1. В Visual Studio создайте новое консольное приложение C#.

1. Используйте следующий код в файле __Program.cs__:

    > [!IMPORTANT]
    > Измените следующие сведения:
    >
    > * задайте в качестве __namespace__ имя проекта;
    > * Задайте __прогноза ключ__ значение, полученное ранее в строку, которая начинается с `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Задайте __URL-адрес прогноза__ значение, полученное ранее в строку, которая начинается с `string url =`.

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
                client.DefaultRequestHeaders.Add("Prediction-Key", "3b9dde6d1ae1453a86bfeb1d945300f2");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v3.0/Prediction/8622c779-471c-4b6e-842c-67a11deffd7b/classify/iterations/Cats%20vs.%20Dogs%20-%20Published%20Iteration%203/image";

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

## <a name="use-the-application"></a>Использование приложения

При запуске приложения, необходимо ввести путь для файла изображения в консоли. Изображение отправляется в API прогнозирования и результаты прогнозирования возвращаются в виде документа JSON. Приведенный ниже код JSON приведен пример ответа.

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

[Экспорт модели для использования на мобильных устройствах](export-your-model.md)

[Начало работы с пакетами SDK для .NET](csharp-tutorial.md)

[Начало работы с пакетами SDK для Python](python-tutorial.md)

[Начало работы с пакетами SDK для Java](java-tutorial.md)

[Начало работы с пакетами SDK для узла](node-tutorial.md)

[Начало работы с пакетами SDK для Go](go-tutorial.md)
