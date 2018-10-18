---
title: Руководство. Создание проектов классификации изображений с помощью Пользовательской службы визуального распознавания и Java
titlesuffix: Azure Cognitive Services
description: Создайте проект, добавьте теги, загрузите изображения, обучите проект и выполните прогнозирование с использованием конечной точки по умолчанию.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: 9a7f50e0eb33016d6a2d8f28be047b327135c51f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367361"
---
# <a name="tutorial-build-an-image-classification-project-with-java"></a>Руководство. Создание проектов классификации изображений с помощью Java

Сведения о том, как создать проект классификации изображений с помощью Пользовательской службы визуального распознавания используя Java. Создав проект, можно добавить теги, загрузить изображения, обучить проект, получить URL-адрес конечной точки прогнозирования проекта по умолчанию и с его помощью программными средствами протестировать изображение. Этот пример с открытым кодом можно применить как шаблон для создания собственного приложения с помощью API Пользовательской службы визуального распознавания.

## <a name="prerequisites"></a>Предварительные требования

- JDK 7 или 8 установлены.
- Maven установлен.

## <a name="get-the-training-and-prediction-keys"></a>Получение ключей обучения и прогнозирования

Чтобы получить ключи, используемые в этом примере, посетите [веб-страницу Пользовательской службы визуального распознавания](https://customvision.ai) и выберите __значок шестеренки__ в правом верхнем углу. В разделе __Accounts__ (Учетные записи) скопируйте значения в полях __Training Key__ (Ключ обучения) и __Prediction Key__ (Ключ прогнозирования).

![Изображение пользовательского интерфейса с ключами](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Установка пакета SDK для Пользовательской службы визуального распознавания

Можно установить пакет SDK для Пользовательской службы визуального распознавания из центрального репозитория Maven.
* [Пакет SDK для обучения](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [Пакет SDK для прогнозирования](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="understand-the-code"></a>Изучение кода

Полный проект, включая изображения, доступен в [примерах Пользовательской службы визуального распознавания Azure для репозитория Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). 

Используйте вашу интегрированную среду разработки Java, чтобы открыть проект `Vision/CustomVision`. 

В этом приложении используется ключ обучения, полученный ранее при создании проекта __Sample Java Project__. Затем приложение передает изображения для обучения и тестирует классификатор. Классификатор определяет вид дерева — __Hemlock__ или __Japanese Cherry__.

Следующие фрагменты кода реализуют основные функции для этого примера:

## <a name="create-a-custom-vision-service-project"></a>Создание проекта Пользовательской службы визуального распознавания

> [!IMPORTANT]
> Установите для `trainingApiKey` полученное ранее значение ключа обучения.

```java
final String trainingApiKey = "insert your training key here";
TrainingApi trainClient = CustomVisionTrainingManager.authenticate(trainingApiKey);

Trainings trainer = trainClient.trainings();

System.out.println("Creating project...");
Project project = trainer.createProject()
            .withName("Sample Java Project")
            .execute();
```

## <a name="add-tags-to-your-project"></a>Добавление тегов в проект

```java
// create hemlock tag
Tag hemlockTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Hemlock")
    .execute();

// create cherry tag
Tag cherryTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Japanese Cherry")
    .execute();
```

## <a name="upload-images-to-the-project"></a>Загрузка изображений в проект

Образец настроен на использование изображений в окончательном пакете. Изображения считываются с раздела "Ресурсы" и передаются в службу.

```java
System.out.println("Adding images...");
for (int i = 1; i <= 10; i++) {
    String fileName = "hemlock_" + i + ".jpg";
    byte[] contents = GetImage("/Hemlock", fileName);
    AddImageToProject(trainer, project, fileName, contents, hemlockTag.id(), null);
}

for (int i = 1; i <= 10; i++) {
    String fileName = "japanese_cherry_" + i + ".jpg";
    byte[] contents = GetImage("/Japanese Cherry", fileName);
    AddImageToProject(trainer, project, fileName, contents, cherryTag.id(), null);
}
```

Предыдущий фрагмент кода применяет две вспомогательные функции, которые извлекают изображения как потоки данных и загружают их в службу.

```java
private static void AddImageToProject(Trainings trainer, Project project, String fileName, byte[] contents, UUID tag)
{
    System.out.println("Adding image: " + fileName);

    ImageFileCreateEntry file = new ImageFileCreateEntry()
        .withName(fileName)
        .withContents(contents);

    ImageFileCreateBatch batch = new ImageFileCreateBatch()
        .withImages(Collections.singletonList(file));

    batch = batch.withTagIds(Collections.singletonList(tag));

    trainer.createImagesFromFiles(project.id(), batch);
}

private static byte[] GetImage(String folder, String fileName)
{
    try {
        return ByteStreams.toByteArray(CustomVisionSamples.class.getResourceAsStream(folder + "/" + fileName));
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    return null;
}
```

## <a name="train-the-project"></a>Обучение проекта

Это создает первую итерацию в проекте и отмечает эту итерацию как итерацию по умолчанию. 

```java
System.out.println("Training...");
Iteration iteration = trainer.trainProject(project.id());

while (iteration.status().equals("Training"))
{
    System.out.println("Training Status: "+ iteration.status());
    Thread.sleep(1000);
    iteration = trainer.getIteration(project.id(), iteration.id());
}

System.out.println("Training Status: "+ iteration.status());
trainer.updateIteration(project.id(), iteration.id(), iteration.withIsDefault(true));
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>Получение и использование конечной точки прогнозирования по умолчанию

> [!IMPORTANT]
> Установите для `predictionApiKey` полученное ранее значение ключа прогнозирования.

```java
final String predictionApiKey = "insert your prediction key here";
PredictionEndpoint predictClient = CustomVisionPredictionManager.authenticate(predictionApiKey);

// Use below for predictions from a url
// String url = "some url";
// ImagePrediction results = predictor.predictions().predictImage()
//                         .withProjectId(project.id())
//                         .withUrl(url)
//                         .execute();

// load test image
byte[] testImage = GetImage("/Test", "test_image.jpg");

// predict
ImagePrediction results = predictor.predictions().predictImage()
    .withProjectId(project.id())
    .withImageData(testImage)
    .execute();

for (Prediction prediction: results.predictions())
{
    System.out.println(String.format("\t%s: %.2f%%", prediction.tagName(), prediction.probability() * 100.0f));
}
```

## <a name="run-the-example"></a>Выполнение примера

Чтобы скомпилировать и запустить решение с помощью maven выполните следующие действия.

```
mvn compile exec:java
```

Выходные данные приложения будут выглядеть приблизительно так:

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