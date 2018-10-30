---
title: Краткое руководство. Обнаружение лиц на изображении с помощью .NET SDK и C#
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как обнаруживать лица на изображениях, используя клиентскую библиотеку службы распознавания лиц для Windows для C# в Cognitive Services.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: pafarley
ms.openlocfilehash: a4b0b8b277ed6bc6e2bc3c7549d1e67d5f18c615
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954969"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-net-sdk-with-c"></a>Краткое руководство. Обнаружение лиц на изображении с помощью .NET SDK и C#

В этом кратком руководстве вы узнаете, как определять лица людей на изображении с помощью клиентской библиотеки распознавания лиц для Windows.

## <a name="prerequisites"></a>Предварительные требования

* Чтобы выполнить пример, нужен ключ подписки. Вы можете получить ключи бесплатной пробной подписки на странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
* Любой выпуск [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Пакет NuGet клиентской библиотеки [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview). Скачивать пакет не нужно. Инструкции по установке приведены ниже.

## <a name="detectwithurlasync-method"></a>Метод DetectWithUrlAsync

> [!TIP]
> Получите последнюю версию кода, предоставленного в решении Visual Studio, с сайта [GitHub](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face).

Методы `DetectWithUrlAsync` и `DetectWithStreamAsync` создают программу-оболочку [API распознавания лиц](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) для удаленных и локальных изображений соответственно. С помощью этих методов можно распознать лица на изображении и вернуть их атрибуты, в том числе:

* Идентификатор лица: уникальный идентификатор, используемый в различных сценариях API распознавания лиц.
* Границы лица: отступ слева, сверху, а также ширина и высота лица, определяющие его место на изображении.
* Ориентиры: массив из 27-точечных ориентиров, указывающий на важные позиции компонентов лица.
* Атрибуты лица, в т. ч. возраст, пол, интенсивность улыбки, положение головы и наличие усов и бороды.

Чтобы выполнить наш пример, сделайте следующее:

1. Создайте консольное приложение Visual C# в Visual Studio.
1. Установите пакет NuGet клиентской библиотеки распознавания лиц.
    1. В верхнем меню щелкните **Средства**, выберите **Диспетчер пакетов NuGet**, а затем **Manage NuGet Packages for Solution** (Управление пакетами NuGet для решения).
    1. Щелкните вкладку **Обзор** и выберите **Включить предварительные выпуски**.
    1. В поле **Поиск** введите "Microsoft.Azure.CognitiveServices.Vision.Face".
    1. Выберите **Microsoft.Azure.CognitiveServices.Vision.Face**, установите флажок рядом с именем своего проекта и щелкните **Установить**.
1. Замените *Program.cs* следующим кодом.
1. Замените `<Subscription Key>` действительным ключом подписки.
1. При необходимости замените `faceEndpoint` регионом Azure, связанным с ключами подписки.
1. При необходимости вместо `LocalImage>` укажите путь к файлу и имя локального файла изображения (этот параметр игнорируется, если значение не задано).
1. При необходимости задайте `remoteImageUrl` другого изображения.
1. Запустите программу.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace DetectFace
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // You must use the same region as you used to get your subscription
        // keys. For example, if you got your subscription keys from westus,
        // replace "westcentralus" with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus
        // region. If you use a free trial subscription key, you shouldn't
        // need to change the region.
        // Specify the Azure region
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg";

        private static readonly FaceAttributeType[] faceAttributes =
            { FaceAttributeType.Age, FaceAttributeType.Gender };

        static void Main(string[] args)
        {
            FaceClient faceClient = new FaceClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });
            faceClient.Endpoint = faceEndpoint;

            Console.WriteLine("Faces being detected ...");
            var t1 = DetectRemoteAsync(faceClient, remoteImageUrl);
            var t2 = DetectLocalAsync(faceClient, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press any key to exit");
            Console.ReadLine();
        }

        // Detect faces in a remote image
        private static async Task DetectRemoteAsync(
            FaceClient faceClient, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine("\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            try
            {
                IList<DetectedFace> faceList =
                    await faceClient.Face.DetectWithUrlAsync(
                        imageUrl, true, false, faceAttributes);

                DisplayAttributes(GetFaceAttributes(faceList, imageUrl), imageUrl);
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imageUrl + ": " + e.Message);
            }
        }

        // Detect faces in a local image
        private static async Task DetectLocalAsync(FaceClient faceClient, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            try
            {
                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    IList<DetectedFace> faceList =
                            await faceClient.Face.DetectWithStreamAsync(
                                imageStream, true, false, faceAttributes);
                    DisplayAttributes(
                        GetFaceAttributes(faceList, imagePath), imagePath);
                }
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imagePath + ": " + e.Message);
            }
        }

        private static string GetFaceAttributes(
            IList<DetectedFace> faceList, string imagePath)
        {
            string attributes = string.Empty;

            foreach (DetectedFace face in faceList)
            {
                double? age = face.FaceAttributes.Age;
                string gender = face.FaceAttributes.Gender.ToString();
                attributes += gender + " " + age + "   ";
            }

            return attributes;
        }

        // Display the face attributes
        private static void DisplayAttributes(string attributes, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(attributes + "\n");
        }
    }
}
```

### <a name="detectwithurlasync-response"></a>Ответ DetectWithUrlAsync

Успешный ответ отображает пол и возраст для каждого лица на изображении.

Пример необработанных данных в формате JSON приведен в разделе [Краткое руководство. Обнаружение лиц на изображении с помощью C#](CSharp.md).

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как создать Windows-приложение WPF, которое использует службу "Распознавание лиц" для обнаружения лиц на изображении. Приложение выделяет рамкой каждое лицо и отображает его описание в строке состояния.

> [!div class="nextstepaction"]
> [Руководство. Создание приложения WPF для обнаружения и выделения лиц на изображении](../Tutorials/FaceAPIinCSharpTutorial.md)
