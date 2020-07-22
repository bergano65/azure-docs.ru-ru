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
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: a0ec14448df91aa49e7140e608c65e216773c57d
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2020
ms.locfileid: "84987034"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Краткое руководство. Обнаружение лиц на изображении с помощью REST API распознавания лиц и cURL

В этом кратком руководстве описано, как определить лица на изображении с помощью REST API распознавания лиц Azure и cURL.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/).
* Получив подписку Azure, перейдите к <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Создание ресурса Распознавания лиц"  target="_blank">созданию ресурса Распознавания лиц<span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.
    * Для подключения приложения к API Распознавания лиц потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="write-the-command"></a>Написание команды
 
Для вызова API Распознавания лиц и получения данных об атрибутах лиц из изображения будет использоваться приведенная ниже команда. Сначала скопируйте код в текстовый редактор. Вам потребуется внести изменения в определенные части команды перед ее запуском.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://<My Endpoint String>.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Ключ подписки
Замените `<Subscription Key>` действительным ключом подписки API распознавания лиц.

### <a name="face-endpoint-url"></a>URL-адрес конечной точки API распознавания лиц

URL-адрес `https://<My Endpoint String>.com/face/v1.0/detect` определяет конечную точку API распознавания лиц Azure, которая будет запрашиваться. Первую часть этого URL-адреса нужно будет изменить, указав конечную точку, соответствующую ключу подписки.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="url-query-string"></a>Строка запроса URL-адреса

Строка запроса URL-адреса конечной точки API распознавания лиц указывает атрибуты лица, которые нужно получить. Вы можете изменить эту строку в зависимости от предполагаемого использования.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

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
