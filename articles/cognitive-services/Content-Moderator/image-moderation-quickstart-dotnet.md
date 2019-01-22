---
title: Краткое руководство. Анализ изображений на наличие нежелательного содержимого с помощью C# — Content Moderator
titlesuffix: Azure Cognitive Services
description: Как анализировать содержимое изображения на наличие нежелательного содержимого с помощью пакета SDK Content Moderator для .NET
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: e11e037e7be8ca308f7924654c4f28b1634dc5fd
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260777"
---
# <a name="quickstart-analyze-image-content-for-objectionable-material-in-c"></a>Краткое руководство. Анализ изображений для выявления нежелательного содержимого с помощью C#

В этой статье содержатся сведения и примеры кода, которые помогут вам приступить к работе с [пакетом SDK Content Moderator для .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Вы узнаете, как проверять на наличие непристойного содержимого или содержимого для взрослых, извлекаемого текста или человеческих лиц с целью модерации потенциально нежелательного содержимого.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

## <a name="prerequisites"></a>Предварительные требования

- Ключ подписки Content Moderator. Следуйте инструкциям в статье [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Создание учетной записи Cognitive Services), чтобы получить подписку Content Moderator и свой ключ.
- Любой выпуск [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/)


> [!NOTE]
> В этом руководстве используется бесплатная подписка Content Moderator. Чтобы получить сведения о каждом уровне подписки, см. страницу [Цены и ограничения](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/).

## <a name="create-the-visual-studio-project"></a>Создание проекта Visual Studio

1. В Visual Studio создайте проект **Консольное приложение (.NET Framework)** и назовите его **ImageModeration**. 
1. При наличии других проектов в решении выберите этот в качестве единого запускаемого проекта.
1. Получите необходимые пакеты NuGet. Щелкните проект правой кнопкой мыши в обозревателе решений и выберите **Управление пакетами NuGet**, затем найдите и установите следующие пакеты:
    - Microsoft.Azure.CognitiveServices.ContentModerator;
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json.

## <a name="add-image-moderation-code"></a>Добавление кода модерации изображения

Затем скопируйте и вставьте код из этого руководства в проект, чтобы реализовать основной сценарий модерации содержимого.

### <a name="include-namespaces"></a>Включение пространства имен

Добавьте следующие инструкции `using` в начало файла *Program.cs*.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Создание клиента Content Moderator

Добавьте следующий фрагмент кода в файл *Program.cs*, чтобы создать поставщик клиента Content Moderator для своей подписки. Добавьте код вместе с классом **Program** в то же пространство имен. Необходимо обновить поля **AzureRegion** и **CMSubscriptionKey** значениями идентификатора региона и ключа подписки.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=84-107)]


### <a name="set-up-input-and-output-targets"></a>Настройка целевых файлов ввода-вывода

Добавьте следующие статические поля в класс **Program** в файле _Program.cs_. Это указывает файлы для входного содержимого изображения и выходного содержимого JSON.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=49-53)]

Вам нужно будет создать входной файл *_ImageFiles.txt* и обновить его путь соответствующим образом (относительные пути задаются относительно каталога выполнения). Откройте _ImageFiles.txt_ и добавьте URL-адрес изображений для модерации. В этом кратком руководстве в качестве примера входных данных используются следующие URL-адреса.

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="create-a-class-to-handle-results"></a>Создание класса для обработки результатов

Добавьте следующий код в *Program.cs* вместе с классом **Program** в том же пространстве имен. Используйте экземпляр этого класса для записи результатов модерации каждого проверенного изображения.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=109-124)]


### <a name="define-the-image-evaluation-method"></a>Определение метода оценки изображений

Добавьте следующий метод в класс **Program**. Этот метод оценивает отдельное изображение тремя различными способами и возвращает результаты оценки. Если вы хотите узнать больше о возможностях отдельных операций, перейдите по ссылке в раздел [Дополнительная информация](#next-steps).

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=55-81)]

### <a name="load-the-input-images"></a>Загрузка входных изображений

Добавьте следующий код в метод **Main** в классе **Program**. Это настраивает программу для получения данных оценки для каждого URL-адреса изображения во входном файле. Затем он записывает эти данные в один выходной файл.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=17-46)]

## <a name="run-the-program"></a>Запуск программы

Программа будет записывать данные строки JSON в файл _ModerationOutput.json_. Примеры изображений, используемые в этом кратком руководстве, предоставляют следующие выходные данные. Обратите внимание, что для каждого изображения предусмотрены отдельные разделы `ImageModeration`, `FaceDetection` и `TextDetection`, которые соответствуют объектам, возвращаемым тремя вызовами API метода **EvaluateImage**.

```json
[{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg",
  "ImageModeration": {
    "cacheID": "7733c303-3b95-4710-a41e-7a322ae81a15_636488005858745661",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_687c356d-0f00-4aeb-ae5f-c7555af80247",
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "116"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "12"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_814fa162-c5d6-4ca6-997b-30ed0686ca83",
    "cacheId": "3fb69496-c64b-4de9-affd-6dd6d23f3e78_636488005876558920",
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_a2c40dbe-609d-4eb8-b01c-9988388804ea",
    "cacheId": "e4c0b500-ea8e-4a31-8fb3-35f98c4fbd65_636488005889528303",
    "result": false,
    "count": 0,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "11"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "faces": []
  }
},
{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
  "ImageModeration": {
    "cacheID": "b4866aa2-5e69-44ed-806a-f9a5d618c8ae_636488005930693926",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_fdce5510-f689-4791-b081-c2ad54dcfe78",
    "adultClassificationScore": 0.0035635426174849272,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.021369094029068947,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "109"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "46"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_08a4bc19-6010-41bb-a440-a77278e167d8",
    "cacheId": "28b37471-41b3-4f79-bd23-965498bcff51_636488005950851288",
    "language": "eng",
    "text": "",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_40f2ce07-14ba-47cd-ba09-58b557a89854",
    "cacheId": "ec9c1be3-99b7-4bd9-8bc4-dc958c74459f_636488005964914299",
    "result": true,
    "count": 6,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "60"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "faces": [
      {
        "bottom": 598,
        "left": 44,
        "right": 268,
        "top": 374
      },
      {
        "bottom": 620,
        "left": 308,
        "right": 532,
        "top": 396
      },
      {
        "bottom": 575,
        "left": 594,
        "right": 773,
        "top": 396
      },
      {
        "bottom": 563,
        "left": 812,
        "right": 955,
        "top": 420
      },
      {
        "bottom": 611,
        "left": 972,
        "right": 1151,
        "top": 432
      },
      {
        "bottom": 510,
        "left": 1232,
        "right": 1456,
        "top": 286
      }
    ]
  }
}]
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы разработали простое приложение .NET, которое использует службу Content Moderator, чтобы найти нужные сведения о данном примере изображения. Затем узнайте больше о том, что означают разные флаги и классификации, чтобы решить, какие данные вам нужны и как ваше приложение должно их обрабатывать.

> [!div class="nextstepaction"]
> [Модерация изображений](image-moderation-api.md)
