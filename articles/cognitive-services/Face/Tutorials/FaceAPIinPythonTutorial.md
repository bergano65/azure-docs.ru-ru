---
title: Руководство. Обнаружение и выделение лиц на изображении с помощью API распознавания лиц и Python
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать API распознавания лиц с пакетом SDK для Python, чтобы обнаруживать лица на изображении.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 6cc3ac25d2196c0275b445503b79b9ac06a791d3
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127743"
---
# <a name="tutorial-detect-and-frame-faces-with-the-face-api-and-python"></a>Руководство. Обнаружение и выделение лиц с помощью API распознавания лиц и Python 

Из этого руководства вы узнаете, как вызвать API распознавания лиц с использованием пакета SDK для Python, чтобы обнаружить на изображении человеческие лица.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством необходимо сделать следующее:

- установить Python 2.7+ или Python 3.5+;
- установить pip;
- установить пакет SDK для Python с поддержкой API распознавания лиц, используя следующую команду:

```bash
pip install cognitive_face
```

- получить [ключ подписки ](https://azure.microsoft.com/try/cognitive-services/) для Microsoft Cognitive Services. При работе с этим руководством можно использовать основной или дополнительный ключ. (Обратите внимание, что для работы с любым API распознавания лиц требуется действительный ключ подписки.)

## <a name="detect-a-face-in-an-image"></a>Распознавание лиц на изображении

```python
import cognitive_face as CF

KEY = '<Subscription Key>'  # Replace with a valid subscription key (keeping the quotes in place).
CF.Key.set(KEY)

BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'  # Replace with your regional Base URL
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

Ниже приведен пример результата. Он содержит список (`list`) обнаруженных лиц. Каждый элемент этого списка является экземпляром `dict`, в котором `faceId` представляет собой уникальный идентификатор обнаруженного лица, а `faceRectangle` описывает расположение этого лица на изображении. Идентификатор лица действует в течение 24 часов.

```python
[{u'faceId': u'68a0f8cf-9dba-4a25-afb3-f9cdf57cca51', u'faceRectangle': {u'width': 89, u'top': 66, u'height': 89, u'left': 446}}]
```

## <a name="draw-rectangles-around-the-faces"></a>Размещение прямоугольников вокруг лиц

На основе координат, полученных в коде JSON от предыдущей команды, вы можете нанести на изображение прямоугольники, выделяющие каждое обнаруженное лицо. Выполните команду `pip install Pillow`, чтобы применить модуль обработки изображений `PIL`.  В начале файла добавьте следующее:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Затем вставьте следующий код в этот скрипт после команды `print(faces)`:

```python
#Convert width height to a point in a rectangle
def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))

#Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

#For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

#Display the image in the users default image browser.
img.show()
```

## <a name="further-exploration"></a>Дальнейшее изучение

Чтобы помочь вам разобраться с API распознавания лиц, в этом руководстве содержится пример с графическим пользовательским интерфейсом. Чтобы использовать этот пример, сначала установите [wxPython](https://wxpython.org/pages/downloads/), а затем выполните приведенные ниже команды.

```bash
git clone https://github.com/Microsoft/Cognitive-Face-Python.git
cd Cognitive-Face-Python
python sample
```

## <a name="summary"></a>Сводка

В этом руководстве вы изучили базовый процесс использования API распознавания лиц через вызовы пакета SDK для Python. Дополнительные сведения об этом API вы можете найти в практическом руководстве и в [разделе справки по API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related-topics"></a>Связанные темы

- [Начало работы с API распознавания лиц в CSharp](FaceAPIinCSharpTutorial.md)
- [Начало работы с API распознавания лиц в Java для Android](FaceAPIinJavaForAndroidTutorial.md)
