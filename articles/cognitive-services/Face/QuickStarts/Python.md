---
title: Краткое руководство. Обнаружение лиц на изображении с помощью API распознавания лиц и Python
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как определять лица на изображении с помощью API распознавания лиц и Python.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/24/2018
ms.author: pafarley
ms.openlocfilehash: 581c2a7d4508833647d4dbb9861000fddd75cde8
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339587"
---
# <a name="quickstart-detect-faces-in-an-image-using-python"></a>Краткое руководство. Обнаружение лиц на изображении с помощью Python

В этом кратком руководстве вы узнаете, как определять лица людей на удаленном изображении с помощью службы "Распознавание лиц". Обнаруженные лица выделяются прямоугольной рамкой и дополняются сведениями о поле и возрасте каждого человека. Чтобы использовать локальное изображение, ознакомьтесь с синтаксисом в разделе [Быстрый старт: анализ локального изображения — REST, Python](../../Computer-vision/QuickStarts/python-disk.md).

Код из этого краткого руководства можно запустить как Jupyter Notebook в [MyBinder](https://mybinder.org). Чтобы запустить модуль привязки, нажмите следующую кнопку:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=FaceAPI.ipynb)

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить пример, нужен ключ подписки. Вы можете получить ключи бесплатной пробной подписки на странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

## <a name="detect-faces-in-an-image"></a>определение лиц на изображении.

Используйте метод [определения лица](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), чтобы распознать лица на изображении и вернуть их атрибуты, в том числе:

* Идентификатор лица: уникальный идентификатор, используемый в различных сценариях API распознавания лиц.
* Границы лица: отступ слева, сверху, а также ширина и высота лица, определяющие его место на изображении.
* Ориентиры: массив из 27-точечных ориентиров, указывающий на важные позиции компонентов лица.
* Атрибуты лица, в т. ч. возраст, пол, интенсивность улыбки, положение головы и наличие усов и бороды.

Чтобы выполнить наш пример, сделайте следующее:

1. Скопируйте приведенный ниже код в новый файл скрипта Python.
1. Замените `<Subscription Key>` действительным ключом подписки.
1. При необходимости замените `face_api_url` расположением, в котором вы получили ключи подписки.
1. При необходимости укажите в значении параметра `image_url` другое изображение.
1. Выполните скрипт.

### <a name="face---detect-request"></a>Запрос на определение лица

В следующем коде для вызова API распознавания лиц используется библиотека `requests` для Python. Результаты возвращаются в виде объектов JSON. Ключ API передается через словарь `headers`. Типы распознаваемых функций передаются через словарь `params`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from matplotlib import patches
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the westcentralus region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
face_api_url = 'https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect'

# Set image_url to the URL of an image that you want to analyze.
image_url = 'https://how-old.net/Images/faces2/main007.jpg'

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {
    'returnFaceId': 'true',
    'returnFaceLandmarks': 'false',
    'returnFaceAttributes': 'age,gender,headPose,smile,facialHair,glasses,' +
    'emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
}
data = {'url': image_url}
response = requests.post(face_api_url, params=params, headers=headers, json=data)
faces = response.json()

# Display the original image and overlay it with the face information.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.figure(figsize=(8, 8))
ax = plt.imshow(image, alpha=0.6)
for face in faces:
    fr = face["faceRectangle"]
    fa = face["faceAttributes"]
    origin = (fr["left"], fr["top"])
    p = patches.Rectangle(
        origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
    ax.axes.add_patch(p)
    plt.text(origin[0], origin[1], "%s, %d"%(fa["gender"].capitalize(), fa["age"]),
             fontsize=20, weight="bold", va="bottom")
_ = plt.axis("off")
```

### <a name="face---detect-response"></a>Результат распознавания лица

В случае успешного выполнения возвращается ответ в формате JSON, например:

```json
[
  {
    "faceId": "35102aa8-4263-4139-bfd6-185bb0f52d88",
    "faceRectangle": {
      "top": 208,
      "left": 228,
      "width": 91,
      "height": 91
    },
    "faceAttributes": {
      "smile": 1,
      "headPose": {
        "pitch": 0,
        "roll": 4.3,
        "yaw": -0.3
      },
      "gender": "female",
      "age": 27,
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
        "happiness": 1,
        "neutral": 0,
        "sadness": 0,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "low",
        "value": 0
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.65
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
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "blond",
            "confidence": 0.5
          },
          {
            "color": "black",
            "confidence": 0.34
          },
          {
            "color": "red",
            "confidence": 0.32
          },
          {
            "color": "gray",
            "confidence": 0.14
          },
          {
            "color": "other",
            "confidence": 0.03
          }
        ]
      }
    }
  },
  {
    "faceId": "42502166-31bb-4ac8-81c0-a7adcb3b3e70",
    "faceRectangle": {
      "top": 109,
      "left": 125,
      "width": 79,
      "height": 79
    },
    "faceAttributes": {
      "smile": 1,
      "headPose": {
        "pitch": 0,
        "roll": 1.7,
        "yaw": 2.1
      },
      "gender": "male",
      "age": 32,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.4
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 1,
        "neutral": 0,
        "sadness": 0,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.11
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.74
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.02,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "blond",
            "confidence": 0.94
          },
          {
            "color": "red",
            "confidence": 0.76
          },
          {
            "color": "gray",
            "confidence": 0.2
          },
          {
            "color": "other",
            "confidence": 0.03
          },
          {
            "color": "black",
            "confidence": 0.01
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с API-интерфейсами распознавания лиц, которые используются для обнаружения лиц, обозначения их границ и возврата таких атрибутов, как возраст и пол.

> [!div class="nextstepaction"]
> [API-интерфейсы распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
