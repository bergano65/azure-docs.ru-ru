---
title: Краткое руководство. Обнаружение лиц на изображении с помощью REST API и Node.js
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как определить лица на изображении с помощью REST API распознавания лиц Azure и Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: f77282502a0032a5b1811e32d9aa47d9b4ede569
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912826"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-nodejs"></a>Краткое руководство. Обнаружение лиц на изображении с помощью REST API и Node.js

В этом кратком руководстве описано, как обнаруживать лица на изображении с помощью REST API Распознавания лиц Azure и Node.js.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/), прежде чем начинать работу. 

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/).
* Получив подписку Azure, перейдите к <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Создание ресурса Распознавания лиц"  target="_blank">созданию ресурса Распознавания лиц<span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.
    * Для подключения приложения к API Распознавания лиц потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.
- Редактор кода, например [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="set-up-the-node-environment"></a>Настройка среды Node

В папке, где вы хотите создать проект, создайте файл *facedetection.js*. Затем установите в этот проект модуль `axios`. Он позволит вашим скриптам отправлять HTTP-запросы.

```shell
npm install axios --save
```

## <a name="write-the-nodejs-script"></a>Запись скрипта Node.js

Вставьте следующий код в *facedetection.js*. Эти поля указывают, как подключиться к службе распознавания лиц и где получить входные данные. [Создайте переменные среды](../../cognitive-services-apis-create-account.md?tabs=multiservice%252cwindows#configure-an-environment-variable-for-authentication) и добавьте к ним свой ключ подписки и конечную точку Распознавания лиц. Вам может потребоваться изменить поле `imageUrl`, чтобы указать свое входное изображение.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/Face/rest/detect.js" id="environment":::

Затем добавьте следующий код, чтобы вызвать API распознавания лиц и получить данные об атрибутах лиц из входного изображения. Поле `returnFaceAttributes` определяет атрибуты лица, которые нужно получить. Вы можете изменить эту строку в зависимости от предполагаемого использования.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/Face/rest/detect.js" id="main":::

## <a name="save-and-run-the-script"></a>Сохранение и запуск скрипта

После внесения изменений откройте командную строку и запустите файл с помощью команды `node`.

```
node facedetection.js
```

При успешном ответе данные распознавания лиц отобразятся в легко читаемом формате JSON. Пример:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
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
 
Чтобы извлечь атрибуты лиц, используйте модель обнаружения 1 и добавьте в запрос параметр `returnFaceAttributes`. Измените параметры следующим образом:

```javascript
    params : {
        detectionModel: 'detection_01',
        returnFaceAttributes: 'age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise',
        returnFaceId: true
    },
```

Теперь в ответе будут указаны атрибуты лиц. Пример:

```json
[
  {
    "faceId": "ae8952c1-7b5e-4a5a-a330-a6aa351262c9",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "b1bb3cbe-5a73-4f8d-96c8-836a5aca9415",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого краткого руководства вы написали скрипт Node.js, который вызывает службу "Распознавание лиц Azure" для обнаружения лиц на изображении и возвращения их атрибутов. Ознакомьтесь со справочной документацией по API распознавания лиц, чтобы узнать больше.

> [!div class="nextstepaction"]
> [API Распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)