---
author: areddish
ms.custom: devx-track-java
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 107cc24cc03c7f8716f4ee0577fc2372668adcd9
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604863"
---
Это руководство содержит инструкции и пример кода, которые помогут вам приступить к работе с клиентской библиотекой службы "Пользовательское визуальное распознавание" для Java и создать модель обнаружения объекта. Здесь объясняется, как создать проект, добавить теги, обучить проект и использовать URL-адрес конечной точки прогнозирования проекта для программного тестирования. Этот пример можно использовать как шаблон при создании своего приложения для распознавания изображений.

> [!NOTE]
> Если вы хотите создать и обучить модель обнаружения объекта _без_ написания кода, см. руководство по [работе со средствами на основе браузера](../../get-started-build-detector.md).

## <a name="prerequisites"></a>Предварительные требования

- Любая среда разработки Java.
- [JDK 7 или 8](https://aka.ms/azure-jdks) установлены.
- Установленный [Maven](https://maven.apache.org/).
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library"></a>Получение клиентской библиотеки службы "Пользовательское визуальное распознавание"

Чтобы написать приложение для анализа изображений с помощью службы "Пользовательское визуальное распознавание" для Java, вам потребуются пакеты Maven для этой службы. Эти пакеты включены в скачиваемый пример проекта, но будут доступны по отдельности здесь.

Клиентскую библиотеку Пользовательского визуального распознавания можно найти в репозитории Maven:
- [Пакет SDK для обучения](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Пакет SDK для прогнозирования](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Клонируйте или скачайте проект с [примерами пакета SDK для Java в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Перейдите в папку **Vision/CustomVision/**.

Этот проект Java создает проект обнаружения объектов Пользовательской службы визуального распознавания с именем __Sample Java OD Project__, доступ к которому можно получить через [веб-сайт Пользовательской службы визуального распознавания](https://customvision.ai/). Затем приложение передает изображения для обучения и тестирует классификатор. В этом проекте классификатор используется для определения того, является ли объект **вилкой** или **ножницами**.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

Эта программа настроена для создания ссылки на данные ключа в качестве переменных среды. Перейдите в папку **Vision/CustomVision** и введите следующие команды PowerShell, чтобы задать переменные среды. 

> [!NOTE]
> Если вы используете операционную систему, отличную от Windows, см. инструкции по [настройке переменных среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication).

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="examine-the-code"></a>Анализ кода

Загрузите проект `Vision/CustomVision` в Java IDE и откройте файл _CustomVisionSamples.java_. Найдите метод **runSample** и закомментируйте вызов метода **ImageClassification_Sample**&mdash;. Этот метод позволяет вызвать сценарий классификации изображений, не рассматриваемый в этом руководстве. Метод **ObjectDetection_Sample** реализует основные функции этого краткого руководства. Перейдите в его определение и проверьте код. 

## <a name="create-a-new-custom-vision-project"></a>Создание проекта службы "Пользовательское визуальное распознавание"

Перейдите в блок кода, который создает клиент обучения и проект обнаружения объектов. Созданный проект будет отображаться на [веб-сайте Пользовательской службы визуального распознавания](https://customvision.ai/), который вы посещали ранее. Ознакомьтесь с перегрузками метода [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true), чтобы указать другие параметры при создании проекта (см. пояснения в руководстве по [созданию средства обнаружения объектов с помощью веб-портала](../../get-started-build-detector.md)).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

## <a name="add-tags-to-your-project"></a>Добавление тегов в проект

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

## <a name="upload-and-tag-images"></a>Отправка и снабжение тегами изображений

При добавлении тегов к изображениям в проектах обнаружения объектов вам нужно указать регион каждого помеченного объекта, используя нормализированные координаты. Перейдите к определению карты `regionMap`. Следующий код связывает все примеры изображений с их регионами с тегами.

> [!NOTE]
> Если у вас нет служебной программы для щелчка и перетаскивания, пометьте координаты регионов с помощью пользовательского веб-интерфейса в [Customvision.ai](https://www.customvision.ai/). В этом примере координаты уже предоставлены.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Перейдите к блоку кода, который добавляет изображения в проект. Изображения считываются из папки **src/main/resources** и отправляются в службу с соответствующими тегами и координатами региона.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Предыдущий фрагмент кода применяет две вспомогательные функции, которые извлекают изображения как потоки данных и загружают их в службу (можно передать до 64 изображений в одном пакете).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project-and-publish"></a>Обучение проекта и публикация

Этот код создает первую итерацию модели прогнозирования и публикует итерацию в конечной точке прогнозирования. Имя, присвоенное опубликованной итерации, можно использовать для отправки запросов на прогнозирование. Итерация недоступна в конечной точке прогнозирования, пока она не будет опубликована.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

## <a name="use-the-prediction-endpoint"></a>Использование конечной точки прогнозирования

Конечная точка прогнозирования, представленная объектом `predictor`, представляет собой ссылку, которую можно использовать для отправки изображения в текущую модель и получения прогноза классификации. В этом примере `predictor` определяется в другом месте с помощью переменной среды ключа прогнозирования.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Выполнение приложения

Чтобы скомпилировать и запустить решение с помощью Maven, перейдите в каталог проекта (**Vision/CustomVision**) в командной строке и выполните следующую команду.

```bash
mvn compile exec:java
```

Просмотрите выходные данные консоли для ведения журнала и результаты прогнозирования. Вы можете убедиться, что тестовое изображение помечено соответствующим образом и область обнаружения верна.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Дальнейшие действия

Вы выполнили в коде каждый шаг процесса обнаружения объектов. В этом примере выполняется одна итерация обучения, но часто нужно несколько раз обучать и тестировать модель, чтобы сделать ее более точной. В следующем руководстве объясняется классификация изображений, но ее принципы сходны с поиском объекта.

> [!div class="nextstepaction"]
> [Тестирование и переобучение модели с помощью Пользовательской службы визуального распознавания](../../test-your-model.md)

* Что собой представляет Пользовательское визуальное распознавание
* [Справочная документация по пакету SDK](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/customvision?view=azure-java-stable)