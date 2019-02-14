---
title: Краткое руководство. Создание проекта классификации изображений с помощью пакета SDK Пользовательской службы визуального распознавания для Python
titlesuffix: Azure Cognitive Services
description: Создание проекта, добавление тегов, загрузка изображений, обучение проекта и выполнение прогнозирования с использованием пакета SDK для Python.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 11/2/2018
ms.author: areddish
ms.openlocfilehash: 551d713d6cc5c6ae8024d4784dbc3a8cca79b401
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55854835"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>Краткое руководство. Создание проекта классификации изображений с помощью пакета SDK Пользовательской службы визуального распознавания для Python

Эта статья содержит сведения и примеры кода, применяя которые вы можете создать модель распознавания изображений с помощью пакета SDK Пользовательской службы визуального распознавания совместно с Python. Создав проект, вы можете добавить теги, загрузить изображения, обучить проект, получить URL-адрес конечной точки прогнозирования проекта по умолчанию и с помощью конечной точки программными средствами протестировать изображение. Этот пример можно использовать как шаблон для создания приложения Python. Если вы хотите создать модель классификации и использовать ее _без кода_, ознакомьтесь со статьей [Как создать классификатор с помощью Пользовательской службы визуального распознавания](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Предварительные требования

- [Python 2.7 или 3.5+](https://www.python.org/downloads/).
- Средство [PIP](https://pip.pypa.io/en/stable/installing/).

## <a name="install-the-custom-vision-sdk"></a>Установка пакета SDK Пользовательского визуального распознавания

Чтобы установить пакет SDK Пользовательского визуального распознавания для Python, выполните следующую команду в PowerShell.

```PowerShell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]


## <a name="add-the-code"></a>Добавление кода

Создайте файл с именем *sample.py* в необходимом каталоге проекта.

### <a name="create-the-custom-vision-service-project"></a>Создание проекта Пользовательской службы визуального распознавания

Добавьте в скрипт следующий код, чтобы создать проект Пользовательской службы визуального распознавания. Вставьте ключи подписки в соответствующие определения.

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

ENDPOINT = "https://southcentralus.api.cognitive.microsoft.com"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>Создание тегов в проекте

Чтобы создать теги классификации в проекте, добавьте следующий код в конец *sample.py*.

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>Отправка и снабжение тегами изображений

Чтобы добавить примеры изображений в проект, вставьте следующий код после создания тегов. Этот код загружает каждое изображение с соответствующим тегом. Вам понадобится ввести URL-адрес базового изображения в зависимости от того, куда вы скачали проект с образцами пакета SDK Cognitive Services для Python.

> [!NOTE]
> Вам также понадобится изменить путь образов в зависимости от того, куда вы ранее скачали проект с образцами пакета SDK Cognitive Services для Python.

```Python
base_image_url = "<path to project>"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])
```

### <a name="train-the-classifier"></a>Обучение классификатора

Этот код создает первую итерацию в проекте и отмечает ее как итерацию по умолчанию. Итерация по умолчанию отражает версию модели, которая будет отвечать на запросы прогнозирования. Итерацию следует обновлять при каждом переобучении модели.

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Make it the default project endpoint
trainer.update_iteration(project.id, iteration.id, is_default=True)
print ("Done!")
```

### <a name="get-and-use-the-default-prediction-endpoint"></a>Получение и использование конечной точки прогнозирования по умолчанию

Чтобы отправить изображение в конечную точку прогнозирования и извлечь прогнозирование, добавьте в конец файла следующий код:

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction

predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>Выполнение приложения

Выполните *sample.py*.

```PowerShell
python sample.py
```

Выходные данные приложения должны выглядеть приблизительно так:

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Вы можете убедиться, что тестовое изображение (в **<base_image_url>/Images/Test/**) помечено соответствующим образом. Вы также можете вернуться к [веб-сайту Пользовательской службы визуального распознавания](https://customvision.ai) и просмотреть текущее состояние созданного проекта.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Дополнительная информация

Теперь вы узнали, как выполнять в коде каждый шаг процесса классификации изображений. В этом примере выполняется одна итерация обучения, но часто нужно несколько раз обучать и тестировать модель, чтобы сделать ее более точной.

> [!div class="nextstepaction"]
> [Тестирование и переобучение модели с помощью Пользовательской службы визуального распознавания](test-your-model.md)
