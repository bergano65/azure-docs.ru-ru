---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: c9f5b5e84955c1974c19d0ccff1a89560fd3e78a
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604888"
---
Это руководство содержит инструкции и пример кода, которые помогут вам приступить к работе с клиентской библиотекой службы "Пользовательское визуальное распознавание" для C# и создать модель обнаружения объекта. Здесь объясняется, как создать проект, добавить теги, обучить проект и использовать URL-адрес конечной точки прогнозирования проекта для программного тестирования. Этот пример можно использовать как шаблон при создании своего приложения для распознавания изображений.

> [!NOTE]
> Если вы хотите создать и обучить модель обнаружения объекта _без_ написания кода, см. руководство по [работе со средствами на основе браузера](../../get-started-build-detector.md).

## <a name="prerequisites"></a>Предварительные требования

- Любой выпуск [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/).
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>Установка клиентской библиотеки Пользовательского визуального распознавания

Чтобы написать приложение для анализа изображений с помощью службы "Пользовательское визуальное распознавание" для .NET, вам потребуются пакеты NuGet этой службы. Эти пакеты включены в скачиваемый пример проекта, но будут доступны по отдельности здесь.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/);
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/).

Клонируйте или скачайте проект [Примеры .NET в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Перейдите в папку **CustomVision/ObjectDetection** и откройте файл _ObjectDetection.csproj_ в Visual Studio.

Этот проект Visual Studio создает проект Пользовательской службы визуального распознавания с именем __My New Project__, доступ к которому можно получить через [веб-сайт Пользовательской службы визуального распознавания](https://customvision.ai/). Затем приложение передает изображения для обучения и тестирует модель обнаружения. В этом проекте модель обучена обнаруживать вилки и ножницы на изображениях.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="examine-the-code"></a>Анализ кода

Откройте файл _Program.cs_ и проверьте код. [Создайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключей обучения и прогнозирования с именами `CUSTOM_VISION_TRAINING_KEY` и `CUSTOM_VISION_PREDICTION_KEY` соответственно. Скрипт будет искать эти переменные.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Кроме того, получите URL-адрес конечной точки на странице параметров веб-сайта службы "Пользовательское визуальное распознавание". Сохраните его в переменную среды с именем `CUSTOM_VISION_ENDPOINT`. Скрипт сохраняет ссылку на нее в корне класса.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

## <a name="create-a-new-custom-vision-service-project"></a>Создание проекта Пользовательской службы визуального распознавания

Следующий фрагмент кода создает проект обнаружения объекта. Созданный проект будет отображаться на [веб-сайте Пользовательской службы визуального распознавания](https://customvision.ai/), который вы посещали ранее. Ознакомьтесь с методом [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true), чтобы указать другие параметры при создании проекта (см. пояснения в руководстве по [созданию средства обнаружения объектов с помощью веб-портала](../../get-started-build-detector.md)).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Добавление тегов в проект

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Отправка и снабжение тегами изображений

При добавлении тегов к изображениям в проектах обнаружения объектов вам нужно указать регион каждого помеченного объекта, используя нормализированные координаты. Следующий код связывает все примеры изображений с отмеченным регионом.

> [!NOTE]
> Если у вас нет служебной программы для щелчка и перетаскивания, пометьте координаты регионов с помощью пользовательского веб-интерфейса в [Customvision.ai](https://www.customvision.ai/). В этом примере координаты уже предоставлены.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Затем это сопоставление ассоциаций используется для отправки каждого примера изображения с координатами региона. В одном пакете можно передать до 64 изображений.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

На этом этапе вы передали все примеры изображений и пометили каждый из них (**вилка** или **ножницы**) со связанным пиксельным прямоугольником.

## <a name="train-the-project"></a>Обучение проекта

Этот код создает первую обучающую итерацию в проекте.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

> [!TIP]
> Обучение с использованием выбранных тегов
>
> При необходимости вы можете выполнить обучение с использованием только некоторых из примененных тегов. Это может потребоваться, если вы еще не применили достаточное количество определенных тегов, но у вас достаточно других. В вызове [TrainProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) используйте параметр *trainingParameters*. Создайте [TrainingParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters?view=azure-dotnet&preserve-view=true) и задайте для свойства **SelectedTags** список идентификаторов тегов, которые хотите использовать. Модель будет обучаться с распознаванием только тегов в этом списке.

## <a name="publish-the-current-iteration"></a>Публикация текущей итерации

Имя, присвоенное опубликованной итерации, можно использовать для отправки запросов на прогнозирование. Итерация будет недоступной в конечной точке прогнозирования, пока она не будет опубликована.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

## <a name="create-a-prediction-endpoint"></a>Создание конечной точки прогнозирования

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

## <a name="test-the-prediction-endpoint"></a>Тестирование конечной точки прогнозирования

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

Вы можете убедиться, что тестовое изображение (в **Images/Test/**) помечено соответствующим образом и что область обнаружения верна. На этом этапе нажмите любую клавишу для выхода из приложения.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Дальнейшие действия

Вы выполнили в коде каждый шаг процесса обнаружения объектов. В этом примере выполняется одна итерация обучения, но часто нужно несколько раз обучать и тестировать модель, чтобы сделать ее более точной. В следующем руководстве объясняется классификация изображений, но ее принципы сходны с поиском объекта.

> [!div class="nextstepaction"]
> [Тестирование и переобучение модели с помощью Пользовательской службы визуального распознавания](../../test-your-model.md)

* Что собой представляет Пользовательское визуальное распознавание
* [Справочная документация по пакету SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet)