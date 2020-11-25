---
title: Краткое руководство. Обнаружение лиц на изображении с помощью REST API и cURL
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как определить лица на изображении с помощью REST API распознавания лиц Azure и cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: 922b261ffeb6cb7a7170a50a3ba5e5ca91a10a11
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "96012005"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Краткое руководство. Обнаружение лиц на изображении с помощью REST API распознавания лиц и cURL

В этом кратком руководстве описано, как определить лица на изображении с помощью REST API распознавания лиц Azure и cURL.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/), прежде чем начинать работу. 

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/).
* Получив подписку Azure, перейдите к <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Создание ресурса Распознавания лиц"  target="_blank">созданию ресурса Распознавания лиц<span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.
    * Для подключения приложения к API Распознавания лиц потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="write-the-command"></a>Написание команды
 
Для вызова API Распознавания лиц и получения данных об атрибутах лиц из изображения будет использоваться приведенная ниже команда. Сначала скопируйте код в текстовый редактор. Вам потребуется внести изменения в определенные части команды перед ее запуском.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" id="detection_model_2":::

### <a name="subscription-key"></a>Ключ подписки
Замените `<Subscription Key>` действительным ключом подписки API распознавания лиц.

### <a name="face-endpoint-url"></a>URL-адрес конечной точки API распознавания лиц

URL-адрес `https://<My Endpoint String>.com/face/v1.0/detect` определяет конечную точку API распознавания лиц Azure, которая будет запрашиваться. Первую часть этого URL-адреса нужно будет изменить, указав конечную точку, соответствующую ключу подписки.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="image-source-url"></a>URL-адрес источника изображения
URL-адрес источника указывает на изображение, используемое в качестве входных данных. Вы можете изменить значение в этом поле, указав URL-адрес любого изображения, которое нужно проанализировать.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Выполните команду следующую команду.

После внесения изменений откройте командную строку и введите новую команду. В окне консоли должна появиться информация о лицах в виде данных JSON. Пример:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

## <a name="extract-face-attributes"></a>Извлечение атрибутов лиц
 
Чтобы извлечь атрибуты лиц, используйте модель обнаружения 1 и добавьте в запрос параметр `returnFaceAttributes`. Команда должна выглядеть, как представленный ниже пример. Как и ранее, добавьте реальные значения ключа подписки и конечной точки Распознавания лиц.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" id="detection_model_1":::

Теперь возвращаемая информация о лицах содержит атрибуты. Пример:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
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
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
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

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого краткого руководства вы написали команду cURL, которая вызывает службу "Распознавание лиц Azure" для обнаружения лиц на изображении и возвращения их атрибутов. Ознакомьтесь со справочной документацией по API распознавания лиц, чтобы узнать больше.

> [!div class="nextstepaction"]
> [API Распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
