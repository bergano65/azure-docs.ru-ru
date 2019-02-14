---
title: Краткое руководство. Создание проекта классификации изображений с помощью пакета SDK Пользовательской службы визуального распознавания для Java
titlesuffix: Azure Cognitive Services
description: Создайте проект, добавьте теги, загрузите изображения, обучите проект и выполните прогнозирование с использованием пакета SDK для Java.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 10/31/2018
ms.author: areddish
ms.openlocfilehash: f6695e1c141d329b3f3d4defe9f01d3a05355908
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55880314"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Краткое руководство. Создание проекта классификации изображений с помощью пакета SDK Пользовательской службы визуального распознавания для Java

Эта статья содержит сведения и примеры кода, которые помогут вам приступить к работе с использованием пакета SDK Пользовательской службы визуального распознавания для Java, чтобы создать модель классификации изображений. Создав проект, вы можете добавить теги, загрузить изображения, обучить проект, получить URL-адрес конечной точки прогнозирования проекта по умолчанию и с помощью конечной точки программными средствами протестировать изображение. Этот пример можно использовать как шаблон для создания приложения Java. Если вы хотите создать модель классификации и использовать ее _без кода_, ознакомьтесь со статьей [Как создать классификатор с помощью Пользовательской службы визуального распознавания](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Предварительные требования
- Любая среда разработки Java.
- [JDK 7 или 8](https://aka.ms/azure-jdks) установлены.
- Установленный Maven.


## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Получение пакета SDK для Пользовательской службы визуального распознавания и примеров кода
Чтобы написать приложение Java, которое использует Пользовательскую службу визуального распознавания, вам потребуются пакеты Пользовательской службы визуального распознавания Maven. Эти элементы включены в скачиваемый пример проекта, однако будут доступны по отдельности здесь.

Можно установить пакет SDK для Пользовательской службы визуального распознавания из центрального репозитория Maven.
* [Пакет SDK для обучения](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [Пакет SDK для прогнозирования](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Клонируйте или скачайте проект с [примерами пакета SDK для Java в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Перейдите в папку **Vision/CustomVision/**.

Этот проект Java создает проект классификации изображений Пользовательской службы визуального распознавания с именем __Пример проекта Java__, доступ к которому можно получить через [веб-сайт Пользовательской службы визуального распознавания](https://customvision.ai/). Затем приложение передает изображения для обучения и тестирует классификатор. В этом проекте классификатор предназначен, чтобы определить, является ли дерево __болиголовом__ или __японской вишней__.

[!INCLUDE [get-keys](includes/get-keys.md)]

Эта программа настроена для хранения данных ключа в качестве переменных сред. Задайте эти переменные, перейдя в папку **Vision/CustomVision** в PowerShell. Затем введите указанные ниже команды:

```PowerShell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Изучение кода

Загрузите проект `Vision/CustomVision` в Java IDE и откройте файл _CustomVisionSamples.java_. Найдите метод **runSample** и закомментируйте вызов метода **ObjectDetection_Sample** &mdash;, будет выполнен сценарий обнаружения объекта, который не рассматривается в этом руководстве. Метод **ImageClassification_Sample** реализует основные функции этого примера. Перейдите в его определение и проверьте код. 

### <a name="create-a-custom-vision-service-project"></a>Создание проекта Пользовательской службы визуального распознавания

Этот первый фрагмент кода создает проект классификации изображений. Созданный проект будет отображаться на [веб-сайте Пользовательской службы визуального распознавания](https://customvision.ai/), который вы посещали ранее. 

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=57-63)]

### <a name="create-tags-in-the-project"></a>Создание тегов в проекте

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=65-74)]

### <a name="upload-and-tag-images"></a>Отправка и снабжение тегами изображений

Примеры изображений включены в папку проекта **src/main/resources**. Они считываются оттуда и передаются в службу с соответствующими тегами.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=76-87)]

Предыдущий фрагмент кода применяет две вспомогательные функции, которые извлекают изображения как потоки данных и загружают их в службу.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=277-314)]

### <a name="train-the-classifier"></a>Обучение классификатора

Этот код создает первую итерацию в проекте и отмечает ее как итерацию по умолчанию. Итерация по умолчанию отражает версию модели, которая будет отвечать на запросы прогнозирования. Итерацию следует обновлять при каждом переобучении модели.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=89-99)]

### <a name="use-the-prediction-endpoint"></a>Использование конечной точки прогнозирования

Конечная точка прогнозирования, представленная объектом `predictor`, представляет собой ссылку, которую можно использовать для отправки изображения в текущую модель и получения прогноза классификации. В этом примере `predictor` определяется в другом месте с помощью переменной среды ключа прогнозирования.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=108-120)]

## <a name="run-the-application"></a>Выполнение приложения

Чтобы скомпилировать и запустить решение с помощью Maven, выполните следующую команду в каталоге проекта в PowerShell:

```PowerShell
mvn compile exec:java
```

Выходные данные консоли приложения должны выглядеть приблизительно так:

```
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Затем можно проверить правильность прогноза относительно тестового изображения (последние несколько строк выходных данных).

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Дополнительная информация

Теперь вы узнали, как выполнять в коде каждый шаг процесса классификации изображений. В этом примере выполняется одна итерация обучения, но часто нужно несколько раз обучать и тестировать модель, чтобы сделать ее более точной.

> [!div class="nextstepaction"]
> [Тестирование и переобучение модели с помощью Пользовательской службы визуального распознавания](test-your-model.md)
