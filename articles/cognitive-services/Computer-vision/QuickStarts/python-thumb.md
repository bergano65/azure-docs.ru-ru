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
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9a76d4f083c791bb1e3c29b4068c2f82f3cd0bc5
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973702"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Краткое руководство. Краткое руководство по созданию эскизов с помощью REST API "Компьютерное зрение" и Python

В этом кратком руководстве описано, как создать эскиз изображения с помощью REST API Компьютерного зрения. С помощью метода [получения эскиза](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) можно указать нужную высоту и ширину, а Компьютерное зрение использует интеллектуальную обрезку для идентификации интересующей области и создания координат обрезки для этой области.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/try/cognitive-services/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

- У вас должен быть ключ подписки для Компьютерного зрения. На странице [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) (Пробная версия Cognitive Services) можно получить ключ бесплатной пробной версии. Или следуйте инструкциям из статьи [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Создание учетной записи Cognitive Services), чтобы получить подписку Content Moderator и свой ключ. Затем [создайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для строки ключа и конечной точки службы с именами `COMPUTER_VISION_SUBSCRIPTION_KEY` и `COMPUTER_VISION_ENDPOINT` соответственно.
- Редактор кода, например [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>Создание и выполнение примера кода

Чтобы создать и выполнить пример кода, скопируйте следующий код в редактор кода. 

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

thumbnail_url = endpoint + "vision/v2.1/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers,
                         params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Затем выполните описанные ниже действия.
1. При необходимости замените значение `image_url` URL-адресом другого изображения, для которого вы хотите создать эскиз.
1. Сохраните код как файл с расширением `.py`. Например, `get-thumbnail.py`.
1. Откройте окно командной строки.
1. В командной строке выполните пример кода с помощью команды `python`. Например, `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Изучите ответ.

Успешный ответ возвращается в виде двоичных данных, которые представляют данные изображения для эскиза. Пример кода должен показать это изображение. Если запрос завершается сбоем, ответ с кодом ошибки отображается в окне командной строки.

## <a name="run-in-jupyter-optional"></a>Выполнение в Jupyter (необязательно)

По желанию процедуры из этого руководства можно выполнять пошагово из записной книжки Jupyter с помощью [MyBinder](https://mybinder.org). Чтобы запустить модуль привязки, нажмите следующую кнопку:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Дополнительная информация

Далее ознакомьтесь с дополнительными сведениями о функции создания эскизов.

> [!div class="nextstepaction"]
> [Создание эскизов](../concept-generating-thumbnails.md)
