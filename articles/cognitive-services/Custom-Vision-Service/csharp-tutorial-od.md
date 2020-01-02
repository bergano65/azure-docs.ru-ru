---
title: Краткое руководство. Создание проекта обнаружения объектов с помощью пакета SDK для C# — Пользовательское визуальное распознавание
titleSuffix: Azure Cognitive Services
description: Создайте проект, добавьте теги, загрузите изображения, обучите проект и удаляйте объекты с помощью .NET SDK с C#.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: ed86a286051ba9240fe23951ef7cb72de1e96201
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74960576"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Краткое руководство. Создание проекта обнаружения объектов с помощью пакета SDK для Пользовательской службы визуального распознавания для .NET

В этой статье показано, как приступить к работе с использованием пакета SDK службы "Пользовательское визуальное распознавание" с C#, чтобы создать модель обнаружения объекта. Создав проект, вы можете добавить регионы с тегами, загрузить изображения, обучить проект, получить URL-адрес конечной точки прогнозирования по умолчанию и с помощью конечной точки программными средствами протестировать изображение. Этот пример можно использовать как шаблон для создания приложения .NET. 

## <a name="prerequisites"></a>Предварительные требования

- Любой выпуск [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/).
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Получение пакета SDK для Пользовательской службы визуального распознавания и примеров кода

Чтобы написать приложение .NET, которое использует Пользовательскую службу визуального распознавания, вам потребуются пакеты Пользовательской службы визуального распознавания NuGet. Эти пакеты включены в скачиваемый пример проекта, но будут доступны по отдельности здесь.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/);
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/).

Клонируйте или скачайте проект [Примеры .NET в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Перейдите в папку **CustomVision/ObjectDetection** и откройте файл _ObjectDetection.csproj_ в Visual Studio.

Этот проект Visual Studio создает проект Пользовательской службы визуального распознавания с именем __My New Project__, доступ к которому можно получить через [веб-сайт Пользовательской службы визуального распознавания](https://customvision.ai/). Затем приложение передает изображения для обучения и тестирует модель обнаружения. В этом проекте модель обучена обнаруживать вилки и ножницы на изображениях.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Изучение кода

Откройте файл _Program.cs_ и проверьте код. [Создайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключей обучения и прогнозирования с именами `CUSTOM_VISION_TRAINING_KEY` и `CUSTOM_VISION_PREDICTION_KEY` соответственно. Скрипт будет искать эти переменные.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Кроме того, получите URL-адрес конечной точки на странице параметров веб-сайта службы "Пользовательское визуальное распознавание". Сохраните его в переменную среды с именем `CUSTOM_VISION_ENDPOINT`. Скрипт сохраняет ссылку на нее в корне класса.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>Создание проекта Пользовательской службы визуального распознавания

Следующий фрагмент кода создает проект обнаружения объекта. Созданный проект будет отображаться на [веб-сайте Пользовательской службы визуального распознавания](https://customvision.ai/), который вы посещали ранее. Ознакомьтесь с методом [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__), чтобы указать другие параметры при создании проекта (см. пояснения в руководстве по [созданию средства обнаружения объектов с помощью веб-портала](get-started-build-detector.md)).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>Добавление тегов в проект

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Отправка и снабжение тегами изображений

При добавлении тегов к изображениям в проектах обнаружения объектов вам нужно указать регион каждого помеченного объекта, используя нормализированные координаты. Следующий код связывает все примеры изображений с отмеченным регионом.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Затем это сопоставление ассоциаций используется для отправки каждого примера изображения с координатами региона. В одном пакете можно передать до 64 изображений.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

На этом этапе вы передали все примеры изображений и пометили каждый из них (**вилка** или **ножницы**) со связанным пиксельным прямоугольником.

### <a name="train-the-project"></a>Обучение проекта

Этот код создает первую обучающую итерацию в проекте.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>Публикация текущей итерации

Имя, присвоенное опубликованной итерации, можно использовать для отправки запросов на прогнозирование. Итерация будет недоступной в конечной точке прогнозирования, пока она не будет опубликована.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>Создание конечной точки прогнозирования

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>Использование конечной точки прогнозирования

Эта часть скрипта загружает тестовое изображение, запрашивает конечную точку модели и выводит данные прогнозирования на консоль.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Выполнение приложения

По мере запуска приложения он должен открыть консольное окно и записать следующие данные вывода:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Вы можете убедиться, что тестовое изображение (в **Images/Test/** ) помечено соответствующим образом и что область обнаружения верна. На этом этапе нажмите любую клавишу для выхода из приложения.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Дополнительная информация

Теперь вы узнали, как выполнять в коде каждый шаг процесса обнаружения объектов. В этом примере выполняется одна итерация обучения, но часто нужно несколько раз обучать и тестировать модель, чтобы сделать ее более точной. В следующем руководстве объясняется классификация изображений, но ее принципы сходны с поиском объекта.

> [!div class="nextstepaction"]
> [Тестирование и переобучение модели с помощью Пользовательской службы визуального распознавания](test-your-model.md)
