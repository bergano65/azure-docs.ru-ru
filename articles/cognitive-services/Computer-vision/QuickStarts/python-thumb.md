---
title: Краткое руководство. Создание эскиза — REST, Python
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как создать эскиз изображения с помощью API компьютерного зрения на Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: c415e8e9c07ca991b32576ebf9daa109a500dbff
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505841"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Краткое руководство. Краткое руководство по созданию эскизов с помощью REST API "Компьютерное зрение" и Python

В этом кратком руководстве описано, как создать эскиз изображения с помощью REST API Компьютерного зрения. С помощью метода [получения эскиза](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c) можно указать нужную высоту и ширину, а Компьютерное зрение использует интеллектуальную обрезку для идентификации интересующей области и создания координат обрезки для этой области.

## <a name="prerequisites"></a>Предварительные требования

* подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/).
* Получив подписку Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="создайте ресурс Компьютерного зрения"  target="_blank">create a Computer Vision resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.
    * Для подключения приложения к API Компьютерного зрения потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.
* [Создайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа и URL-адреса конечной точки с именами `COMPUTER_VISION_SUBSCRIPTION_KEY` и `COMPUTER_VISION_ENDPOINT` соответственно.
- Редактор кода, например [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>Создание и выполнение примера кода

Чтобы создать и выполнить пример кода, скопируйте следующий код в редактор кода. 

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following lines.
# %matplotlib inline
# import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v3.0/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

# Construct URL
headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
# Call API
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

# Open the image from bytes
thumbnail = Image.open(BytesIO(response.content))

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))

# Save thumbnail to file
thumbnail.save('thumbnail.png')

# Display image
thumbnail.show()

# Optional. Display the thumbnail from Jupyter.
# plt.imshow(thumbnail)
# plt.axis("off")
```

Затем выполните описанные ниже действия.

1. (Необязательно) Замените значение `image_url` URL-адресом своего изображения.
1. Сохраните код как файл с расширением `.py`. Например, `get-thumbnail.py`.
1. Откройте окно командной строки.
1. В командной строке выполните пример кода с помощью команды `python`. Например, `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Изучите ответ.

Успешный ответ возвращается в виде двоичных данных, которые представляют данные изображения для эскиза. Пример кода должен показать это изображение. Если запрос завершается сбоем, ответ с кодом ошибки отображается в окне командной строки.

## <a name="run-in-jupyter-optional"></a>Выполнение в Jupyter (необязательно)

По желанию процедуры из этого руководства можно выполнять пошагово из записной книжки Jupyter с помощью [MyBinder](https://mybinder.org). Чтобы запустить модуль привязки, нажмите следующую кнопку:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с приложением Python, которое использует API Компьютерного зрения для оптического распознавания символов (OCR) и создания интеллектуально обрезанных эскизов, а также для обнаружения, классификации, добавления тегов и описания визуальных признаков изображения.

> [!div class="nextstepaction"]
> [Руководство по API компьютерного зрения для Python](../Tutorials/PythonTutorial.md)

* Для быстрых экспериментов с API компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c).
