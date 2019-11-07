---
title: Краткое руководство. Создание проекта классификации изображений с помощью пакета SDK Пользовательской службы визуального распознавания для C#
titleSuffix: Azure Cognitive Services
description: Создайте проект, добавьте теги, загрузите изображения, обучите проект и выполните прогнозирование с помощью .NET SDK и C#
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: anroth
ms.openlocfilehash: ca21bbd77b269e3034fd69cc4685311e91295f36
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "73519126"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Краткое руководство. Создание проекта классификации изображений с помощью пакета SDK Пользовательской службы визуального распознавания для .NET

Эта статья содержит сведения и примеры кода, которые помогут вам приступить к работе с использованием пакета SDK Пользовательского визуального распознавания с C#, чтобы создать модель распознавания изображений. Создав проект, вы можете добавить теги, загрузить изображения, обучить проект, получить URL-адрес конечной точки прогнозирования проекта по умолчанию и с помощью конечной точки программными средствами протестировать изображение. Этот пример можно использовать как шаблон для создания приложения .NET. Если вы хотите создать модель классификации и использовать ее _без кода_, ознакомьтесь со статьей [Как создать классификатор с помощью Пользовательской службы визуального распознавания](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Предварительные требования

- Любой выпуск [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/).
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Получение пакета SDK для Пользовательской службы визуального распознавания и примеров кода

Чтобы написать приложение .NET, которое использует Пользовательскую службу визуального распознавания, вам потребуются пакеты Пользовательской службы визуального распознавания NuGet. Эти пакеты включены в скачиваемый пример проекта, но будут доступны по отдельности здесь.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/);
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/).

Клонируйте или скачайте проект [Примеры .NET в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Перейдите к папке **CustomVision/ImageClassification** и откройте _ImageClassification.csproj_ в Visual Studio.

Этот проект Visual Studio создает проект Пользовательской службы визуального распознавания с именем __My New Project__, доступ к которому можно получить через [веб-сайт Пользовательской службы визуального распознавания](https://customvision.ai/). Затем приложение передает изображения для обучения и тестирует классификатор. В этом проекте классификатор предназначен, чтобы определить, является ли дерево __болиголовом__ или __японской вишней__.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Изучение кода

Откройте файл _Program.cs_ и проверьте код. [Создайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключей обучения и прогнозирования с именами `CUSTOM_VISION_TRAINING_KEY` и `CUSTOM_VISION_PREDICTION_KEY` соответственно. Скрипт будет искать их.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

Кроме того, получите URL-адрес конечной точки на странице параметров веб-сайта службы "Пользовательское визуальное распознавание". Сохраните его в переменную среды с именем `CUSTOM_VISION_ENDPOINT`. Скрипт сохраняет ссылку на нее в корне класса.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

Следующие строки кода реализуют основную функциональность проекта.

### <a name="create-a-new-custom-vision-service-project"></a>Создание проекта Пользовательской службы визуального распознавания

Созданный проект будет отображаться на [веб-сайте Пользовательской службы визуального распознавания](https://customvision.ai/), который вы посещали ранее. Ознакомьтесь с методом [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__), чтобы указать другие параметры при создании проекта (см. пояснения в руководстве по [созданию классификатора с помощью веб-портала](getting-started-build-a-classifier.md)).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Создание тегов в проекте

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Отправка и снабжение тегами изображений

Изображения для этого проекта включены. Они указаны в методе**LoadImagesFromDisk** в файле _Program.cs_. В одном пакете можно передать до 64 изображений.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>Обучение и публикация классификатора

Этот код создает первую итерацию в проекте и публикует ее в конечной точке прогнозирования. Имя, присвоенное опубликованной итерации, можно использовать для отправки запросов на прогнозирование. Итерация недоступна в конечной точке прогнозирования, пока она не будет опубликована.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>Настройка конечной точки прогнозирования

Конечная точка прогнозирования является ссылкой, которую можно использовать для отправки изображения в текущую модель и получения прогноза классификации.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Отправка изображения в конечную точку прогнозирования по умолчанию

В этом скрипте тестовый образ загружается в метод **LoadImagesFromDisk**, а выходные данные прогноза модели должны отображаться в консоли. Значение переменной publishedModelName должно соответствовать значению, указанному в поле "Published as" (Опубликовано как) на вкладке **производительности** портала Пользовательского визуального распознавания. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Выполнение приложения

По мере запуска приложения он должен открыть консольное окно и записать следующие данные вывода:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Вы можете убедиться, что тестовое изображение (в **Images/Test/** ) помечено соответствующим образом. Нажмите любую клавишу для выхода из приложения. Вы также можете вернуться к [веб-сайту Пользовательской службы визуального распознавания](https://customvision.ai) и просмотреть текущее состояние созданного проекта.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Дополнительная информация

Теперь вы узнали, как выполнять в коде каждый шаг процесса классификации изображений. В этом примере выполняется одна итерация обучения, но часто нужно несколько раз обучать и тестировать модель, чтобы сделать ее более точной.

> [!div class="nextstepaction"]
> [Тестирование и переобучение модели с помощью Пользовательской службы визуального распознавания](test-your-model.md)
