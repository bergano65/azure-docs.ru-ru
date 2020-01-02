---
title: Краткое руководство. Содержимое предметной области — REST, Python
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы будете использовать модели предметной области для распознавания знаменитостей и достопримечательностей на изображении с помощью API компьютерного зрения c Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8e5ff917e7283457e7ff1d4c5fd0cbd91a1b0ace
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973753"
---
# <a name="quickstart-use-a-domain-model-using-the-rest-api-and-python-in-computer-vision"></a>Краткое руководство. Использование модели предметной области с помощью REST API компьютерного зрения и Python

Из этого краткого руководства вы узнаете, как использовать модель предметной области для поиска достопримечательностей или, при необходимости, знаменитостей на изображении в удаленном расположении с использованием REST API Компьютерного зрения. Вы можете применить модель предметной области для распознавания содержимого на изображении с помощью метода [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200).

Процедуры из этого руководства можно выполнять пошагово из записной книжки Jupyter с помощью [MyBinder](https://mybinder.org). Чтобы запустить модуль привязки, нажмите следующую кнопку:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/try/cognitive-services/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

- Установите [Python](https://www.python.org/downloads/), если хотите выполнить этот пример кода в локальной среде.
- У вас должен быть ключ подписки для Компьютерного зрения. На странице [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) (Пробная версия Cognitive Services) можно получить ключ бесплатной пробной версии. Или следуйте инструкциям из статьи [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Создание учетной записи Cognitive Services), чтобы получить подписку Content Moderator и свой ключ. Затем [создайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для строки ключа и конечной точки службы с именами `COMPUTER_VISION_SUBSCRIPTION_KEY` и `COMPUTER_VISION_ENDPOINT` соответственно.

## <a name="create-and-run-the-landmarks-sample"></a>Создание и запуск образца для распознавания достопримечательностей

Чтобы создать и запустить пример для распознавания достопримечательностей, сделайте следующее.

1. Скопируйте приведенный ниже код в текстовый редактор.
1. при необходимости замените значение `image_url` URL-адресом другого изображения, на котором вы хотите произвести поиск достопримечательностей.
1. Сохраните код как файл с расширением `.py`. Например, `get-landmarks.py`.
1. Откройте окно командной строки.
1. В командной строке выполните пример кода с помощью команды `python`. Например, `python get-landmarks.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

landmark_analyze_url = endpoint + "vision/v2.1/models/landmarks/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/f/f6/" + \
    "Bunker_Hill_Monument_2005.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'landmarks'}
data = {'url': image_url}
response = requests.post(
    landmark_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant landmark for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["landmarks"] is not []
print(analysis)
landmark_name = analysis["result"]["landmarks"][0]["name"].capitalize()

# Display the image and overlay it with the landmark name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(landmark_name, size="x-large", y=-0.1)
```

## <a name="examine-the-response-for-the-landmarks-sample"></a>Изучение ответа для примера распознавания достопримечательностей

Успешный ответ будет возвращен в формате JSON. После этого запустится синтаксический анализ примера веб-страницы и в окне командной строки отобразится успешный ответ, аналогичный следующему.

```json
{
  "result": {
    "landmarks": [
      {
        "name": "Bunker Hill Monument",
        "confidence": 0.9768505096435547
      }
    ]
  },
  "requestId": "659a10cd-44bb-44db-9147-a295b853b2b8",
  "metadata": {
    "height": 1600,
    "width": 1200,
    "format": "Jpeg"
  }
}
```

## <a name="create-and-run-the-celebrities-sample"></a>Создание и запуск примера распознавания знаменитостей

Чтобы создать и запустить пример для распознавания знаменитостей, сделайте следующее.

1. Скопируйте приведенный ниже код в текстовый редактор.
1. При необходимости внесите в код следующие изменения.
    1. Замените значение `subscription_key` своим ключом подписки.
    1. Замените значение `vision_base_url` URL-адресом конечной точки ресурса Компьютерного зрения в регионе Azure, где вы получили ключи подписки, если это необходимо.
    1. при необходимости замените значение `image_url` URL-адресом другого изображения, на котором вы хотите произвести поиск знаменитостей.
1. Сохраните код как файл с расширением `.py`. Например, `get-celebrities.py`.
1. Откройте окно командной строки.
1. В командной строке выполните пример кода с помощью команды `python`. Например, `python get-celebrities.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/"

celebrity_analyze_url = vision_base_url + "models/celebrities/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/d9/" + \
    "Bill_gates_portrait.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'celebrities'}
data = {'url': image_url}
response = requests.post(
    celebrity_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant celebrity for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["celebrities"] is not []
print(analysis)
celebrity_name = analysis["result"]["celebrities"][0]["name"].capitalize()

# Display the image and overlay it with the celebrity name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(celebrity_name, size="x-large", y=-0.1)
```

## <a name="examine-the-response-for-the-celebrities-sample"></a>Изучение ответа для примера распознавания знаменитостей

Успешный ответ будет возвращен в формате JSON. После этого запустится синтаксический анализ примера веб-страницы и в окне командной строки отобразится успешный ответ, аналогичный следующему.


```json
{
  "result": {
    "celebrities": [
      {
        "faceRectangle": {
          "top": 123,
          "left": 156,
          "width": 187,
          "height": 187
        },
        "name": "Bill Gates",
        "confidence": 0.9993845224380493
      }
    ]
  },
  "requestId": "f14ec1d0-62d4-4296-9ceb-6b5776dc2020",
  "metadata": {
    "height": 521,
    "width": 550,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите файлы обоих примеров, если они больше не нужны.

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с приложением Python, которое использует API компьютерного зрения для оптического распознавания символов (OCR) и создания интеллектуально обрезанных эскизов, а также для обнаружения, классификации, добавления тегов и описания визуальных признаков изображения, включая лица. Для быстрых экспериментов с API компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Руководство по API компьютерного зрения для Python](../Tutorials/PythonTutorial.md)
