---
title: Краткое руководство. Клиентская библиотека службы "Компьютерное зрение" для Java
titleSuffix: Azure Cognitive Services
description: Приступите к работе с клиентской библиотекой службы "Компьютерное зрение" для Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: fd8abf81589f3338f9e45c6c1d23681269ccc654
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164853"
---
# <a name="quickstart-computer-vision-client-library-for-java"></a>Краткое руководство. Клиентская библиотека службы "Компьютерное зрение" для Java

Приступите к работе с клиентской библиотекой службы "Компьютерное зрение" для Java. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. Служба "Компьютерное зрение" предоставляет доступ к передовым алгоритмам обработки изображений и возврата данных.

С помощью клиентской библиотеки службы "Компьютерное зрение" для Java можно выполнять следующую операцию:

* анализ изображений на наличие тегов, текстового описания, лиц, содержимого для взрослых и многого другого.

[Справочная документация](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [Артефакт (Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Примеры](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [пакета средств разработки Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Средство сборки Gradle](https://gradle.org/install/) или другой диспетчер зависимостей.

## <a name="setting-up"></a>Настройка

### <a name="create-a-computer-vision-azure-resource"></a>Создание ресурса Компьютерного зрения в Azure

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс для Компьютерного зрения с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [интерфейса командной строки Azure (CLI)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) на локальном компьютере. Также можно:

* Получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services/#decision) на 7 дней. После регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Просмотреть этот ресурс на [портале Azure](https://portal.azure.com/).

Затем [создайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для строки ключа и конечной точки службы с именами `COMPUTER_VISION_SUBSCRIPTION_KEY` и `COMPUTER_VISION_ENDPOINT` соответственно.

### <a name="create-a-new-gradle-project"></a>Создание проекта Gradle

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. 

```console
mkdir myapp && cd myapp
```

Выполните команду `gradle init` из рабочей папки. Эта команда создает необходимые файлы сборки для Gradle, включая *build.gradle.kts*, который используется во время выполнения для создания и настройки приложения.

```console
gradle init --type basic
```

Когда появится запрос на выбор **предметно-ориентированного языка**, выберите **Kotlin**.

Найдите файл *build.gradle.kts* и откройте его в предпочитаемой интегрированной среде разработки или текстовом редакторе. Затем скопируйте и вставьте в файл приведенную ниже конфигурацию сборки. Эта конфигурация определяет проект как приложение Java, точкой входа которого является класс **ComputerVisionQuickstarts**. Он импортирует библиотеку Компьютерного зрения.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

В рабочей папке выполните следующую команду, чтобы создать исходную папку проекта.

```console
mkdir -p src/main/java
```

Перейдите в новую папку и создайте файл с именем *ComputerVisionQuickstarts.java*. Откройте его в предпочитаемом редакторе или интегрированной среде разработки и добавьте следующие операторы `import`:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_imports)]

Затем добавьте определение класса для **ComputerVisionQuickstarts**.

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

В этом кратком руководстве используется диспетчер зависимостей Gradle. Клиентскую библиотеку и информацию для других диспетчеров зависимостей можно найти в [центральном репозитории Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

В файле проекта *build.gradle.kts* добавьте клиентскую библиотеку службы "Компьютерное зрение" в качестве зависимости.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы обрабатывают некоторые основные функции пакета SDK Java для Компьютерного зрения.

|ИМЯ|ОПИСАНИЕ|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Этот класс требуется для всех функций Компьютерного зрения. Вы создаете его экземпляр с информацией о подписке и используете его для создания экземпляров других классов.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Этот класс поступает из клиентского объекта и напрямую обрабатывает все операции с изображениями, такие как анализ изображений, обнаружение текста и создание эскизов.
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Это перечисление определяет различные типы анализа изображений, которые можно выполнить в стандартной операции анализа. Набор значений VisualFeatureTypes указывается в зависимости от потребностей. |

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки службы "Компьютерное зрение" для Java:

* [аутентификация клиента](#authenticate-the-client);
* [Анализ изображения](#analyze-an-image)

## <a name="authenticate-the-client"></a>Аутентификация клиента

> [!NOTE]
> В этом кратком руководстве предполагается, что вы уже [создали переменную среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа Компьютерного зрения с именем `COMPUTER_VISION_SUBSCRIPTION_KEY`.

Следующий код добавляет метод `main` в ваш класс и создает переменные для конечной точки и ключа Azure вашего ресурса. Вам нужно будет ввести собственную строку конечной точки, которую можно найти в разделе **Обзор** на портале Azure. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Затем добавьте приведенный ниже код, чтобы создать объект [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable), и передайте его в другие методы, которые будут определены позже.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Если вы создали переменную среды после запуска приложения, для доступа к переменной следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена.

## <a name="analyze-an-image"></a>Анализ изображения

В следующем коде определен метод `AnalyzeLocalImage`, который использует клиентский объект для анализа локального изображения и вывода результатов. Метод возвращает текстовое описание, категоризацию, список тегов, обнаруженные лица, флажки содержимого для взрослых, основные цвета и тип изображения.

### <a name="set-up-test-image"></a>Настройка тестового изображения

Сначала создайте папку **resources/** в папке **src/main/** вашего проекта и добавьте изображение, которое нужно проанализировать. Затем добавьте следующее определение метода в класс **ComputerVisionQuickstarts**. При необходимости измените значение `pathToLocalImage` в соответствии с вашим файлом изображения. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

> [!NOTE]
> Кроме того, можно проанализировать удаленное изображение, используя его URL-адрес. Пример кода для сценариев, в которых используются удаленные изображения, см. на [сайте GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java).

### <a name="specify-visual-features"></a>Указание визуальных компонентов

Затем укажите, какие визуальные компоненты вы хотите извлечь при анализе. Полный список компонентов см. в описании перечисления [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Анализ
Этот метод выводит на консоль подробные результаты для каждой области анализа изображения. Мы рекомендуем заключить этот вызов метода в блок Try/Catch. Метод **analyzeImageInStream** возвращает объект **ImageAnalysis**, который содержит всю извлеченную информацию.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

В следующих разделах подробно показано, как анализировать эти сведения.

### <a name="get-image-description"></a>Получение описания изображения

Следующий код получает список созданных заголовков для изображения. Дополнительные сведения см. в статье [Описание изображений на понятном для пользователя языке](../concept-describing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Получение категории изображения

Следующий код получает обнаруженную категорию изображения. Дополнительные сведения см. в статье [Categorize images by subject matter](../concept-categorizing-images.md) (Классификация изображений по темам).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Получение тегов изображения

Следующий код получает набор обнаруженных тегов изображения. Дополнительные сведения см. в статье [Applying content tags to images](../concept-tagging-images.md) (Применение тегов содержимого к изображениям).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="get-faces"></a>Получение лиц

Следующий код возвращает обнаруженные лица на изображении с их координатами прямоугольника и атрибутами выбора лиц. Дополнительные сведения см. в статье [Определение лиц с помощью компьютерного зрения](../concept-detecting-faces.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="get-adult-or-racy-content"></a>Получение непристойного содержимого или содержимого для взрослых

Следующий код выводит сведения об обнаружении непристойного содержимого или содержимого для взрослых на изображении. Дополнительные сведения см. в статье [Обнаружение содержимого для взрослых и содержимого непристойного характера](../concept-detecting-adult-content.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Получение цветовой схемы изображения

Следующий код выводит обнаруженные атрибуты цвета на изображении, например доминирующие и акцентные цвета. Дополнительные сведения см. в статье [Обнаружение цветовых схем на изображениях](../concept-detecting-color-schemes.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Получение содержимого, связанного с определенной предметной областью

Компьютерное зрение может использовать специализированную модель для дальнейшего анализа изображений. Дополнительные сведения см. в статье [Обнаружение содержимого, связанного с определенными предметными областями](../concept-detecting-domain-content.md). 

Следующий код анализирует данные об обнаруженных известных лицах на изображении.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

Следующий код анализирует данные об обнаруженных достопримечательностях на изображении.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

## <a name="run-the-application"></a>Выполнение приложения

Чтобы создать приложение, выполните следующую команду:

```console
gradle build
```

Запустите приложение, выполнив команду `gradle run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дополнительная информация

Изучив это краткое руководство, вы узнали, как с помощью библиотеки службы "Компьютерное зрение" для Java выполнять базовые задачи. Далее ознакомьтесь со справочной документацией, чтобы узнать больше о библиотеке.

> [!div class="nextstepaction"]
>[Справочник по службе "Компьютерное зрение" для Java](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Что собой представляет Компьютерное зрение](../Home.md)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java).