---
title: Краткое руководство. Создание проекта обнаружения объектов с помощью пакета SDK Пользовательской службы визуального распознавания для Java
titleSuffix: Azure Cognitive Services
description: Создавайте проект, добавляйте теги, загружайте изображения, обучайте свой проект и находите объекты с помощью пакетов SDK для Java.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: cad7c6464cff05bee7e2b086d22f9e696dfa1c4b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970318"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Краткое руководство. Создание проекта обнаружения объектов с помощью пакета SDK Пользовательской службы визуального распознавания для Java

В этой статье показано, как с помощью пакета SDK Пользовательского визуального распознавания для Java создать модель обнаружения объекта. Создав проект, вы можете добавить регионы с тегами, загрузить изображения, обучить проект, получить URL-адрес конечной точки прогнозирования по умолчанию и с помощью конечной точки программными средствами протестировать изображение. Этот пример можно использовать как шаблон для создания приложения Java.

## <a name="prerequisites"></a>Предварительные требования

- Любая среда разработки Java.
- [JDK 7 или 8](https://aka.ms/azure-jdks) установлены.
- Установленный Maven.
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Получение пакета SDK для Пользовательской службы визуального распознавания и примеров кода

Чтобы написать приложение Java, которое использует Пользовательскую службу визуального распознавания, вам потребуются пакеты Пользовательской службы визуального распознавания Maven. Эти пакеты включены в скачиваемый пример проекта, но будут доступны по отдельности здесь.

Можно установить пакет SDK для Пользовательской службы визуального распознавания из центрального репозитория Maven.
- [Пакет SDK для обучения](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Пакет SDK для прогнозирования](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Клонируйте или скачайте проект с [примерами пакета SDK для Java в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Перейдите в папку **Vision/CustomVision/** .

Этот проект Java создает проект обнаружения объектов Пользовательской службы визуального распознавания с именем __Sample Java OD Project__, доступ к которому можно получить через [веб-сайт Пользовательской службы визуального распознавания](https://customvision.ai/). Затем приложение передает изображения для обучения и тестирует классификатор. В этом проекте классификатор предназначен, чтобы определить, является ли дерево __болиголовом__ или __японской вишней__.

[!INCLUDE [get-keys](includes/get-keys.md)]

Эта программа настроена для хранения данных ключа в качестве переменных сред. Задайте эти переменные, перейдя в папку **Vision/CustomVision** в PowerShell. Затем введите указанные ниже команды:

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Изучение кода

Загрузите проект `Vision/CustomVision` в Java IDE и откройте файл _CustomVisionSamples.java_. Найдите метод **runSample** и закомментируйте вызов метода **ImageClassification_Sample**&mdash;. Этот метод позволяет вызвать сценарий классификации изображений, не рассматриваемый в этом руководстве. Метод **ObjectDetection_Sample** реализует основные функции этого краткого руководства. Перейдите в его определение и проверьте код. 

### <a name="create-a-new-custom-vision-service-project"></a>Создание проекта Пользовательской службы визуального распознавания

Перейдите в блок кода, который создает клиент обучения и проект обнаружения объектов. Созданный проект будет отображаться на [веб-сайте Пользовательской службы визуального распознавания](https://customvision.ai/), который вы посещали ранее. Ознакомьтесь с перегрузками метода [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_), чтобы указать другие параметры при создании проекта (см. пояснения в руководстве по [созданию средства обнаружения объектов с помощью веб-портала](get-started-build-detector.md)).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

### <a name="add-tags-to-your-project"></a>Добавление тегов в проект

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

### <a name="upload-and-tag-images"></a>Отправка и снабжение тегами изображений

При добавлении тегов к изображениям в проектах обнаружения объектов вам нужно указать регион каждого помеченного объекта, используя нормализированные координаты. Перейдите к определению карты `regionMap`. Следующий код связывает все примеры изображений с их регионами с тегами.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Перейдите к блоку кода, который добавляет изображения в проект. Изображения считываются из папки **src/main/resources** и отправляются в службу с соответствующими тегами и координатами региона.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Предыдущий фрагмент кода применяет две вспомогательные функции, которые извлекают изображения как потоки данных и загружают их в службу (можно передать до 64 изображений в одном пакете).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-project-and-publish"></a>Обучение проекта и публикация

Этот код создает первую итерацию в проекте и публикует ее в конечной точке прогнозирования. Имя, присвоенное опубликованной итерации, можно использовать для отправки запросов на прогнозирование. Итерация недоступна в конечной точке прогнозирования, пока она не будет опубликована.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

### <a name="use-the-prediction-endpoint"></a>Использование конечной точки прогнозирования

Конечная точка прогнозирования, представленная объектом `predictor`, представляет собой ссылку, которую можно использовать для отправки изображения в текущую модель и получения прогноза классификации. В этом примере `predictor` определяется в другом месте с помощью переменной среды ключа прогнозирования.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Выполнение приложения

Чтобы скомпилировать и запустить решение с помощью Maven, выполните следующую команду в каталоге проекта в PowerShell:

```powershell
mvn compile exec:java
```

Просмотрите выходные данные консоли для ведения журнала и результаты прогнозирования. Вы можете убедиться, что тестовое изображение помечено соответствующим образом и область обнаружения верна.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Дополнительная информация

Теперь вы узнали, как выполнять в коде каждый шаг процесса обнаружения объектов. В этом примере выполняется одна итерация обучения, но часто нужно несколько раз обучать и тестировать модель, чтобы сделать ее более точной. В следующем руководстве объясняется классификация изображений, но ее принципы сходны с поиском объекта.

> [!div class="nextstepaction"]
> [Тестирование и переобучение модели с помощью Пользовательской службы визуального распознавания](test-your-model.md)
