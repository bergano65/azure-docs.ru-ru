---
title: Краткое руководство. Клиентская библиотека Content Moderator для Java
titleSuffix: Azure Cognitive Services
description: Узнайте, как приступить к работе с клиентской библиотекой Content Moderator для Azure Cognitive Services для Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: pafarley
ms.openlocfilehash: edc51be93ba209a1c60970e6fa1b47fca75048c6
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744442"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>Краткое руководство. Клиентская библиотека Content Moderator для Java

Приступите к работе с клиентской библиотекой Content Moderator для Java. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. Content Moderator — это когнитивная служба, которая проверяет текст, изображения и видео на наличие потенциально оскорбительных, представляющих риск или нежелательных по иным причинам материалов. При обнаружении таких материалов служба применяет к содержимому соответствующие метки (флаги). Затем приложение может обрабатывать помеченное содержимое для обеспечения соответствия нормативным требованиям или оставлять его как предполагаемую среду для пользователей.

Клиентскую библиотеку Content Moderator для Java можно использовать для такой задачи:

* модерация изображений с непристойным содержимым или содержимым для взрослых, текстом или лицами людей.

[Справочная документация](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [Артефакт (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [Примеры](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [пакета средств разработки Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Средство сборки Gradle](https://gradle.org/install/) или другой диспетчер зависимостей.

## <a name="setting-up"></a>Настройка

### <a name="create-a-content-moderator-azure-resource"></a>Создание ресурса Azure для Content Moderator

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс для Content Moderator с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [интерфейса командной строки Azure (CLI)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) на локальном компьютере. Также можно:

* Получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services/#decision) на 7 дней. После регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Просмотреть этот ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки [задайте переменную среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа с именем `AZURE_CONTENTMODERATOR_KEY`.

### <a name="create-a-new-gradle-project"></a>Создание проекта Gradle

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. 

```console
mkdir myapp && cd myapp
```
Запустите `gradle init`. Эта команда создает необходимые файлы сборки для Gradle, включая *build.gradle.kts*, который используется во время выполнения для создания и настройки приложения. Выполните следующую команду из рабочего каталога.

```console
gradle init --type basic
```

Когда появится запрос на создание скрипта DSL, выберите **Kotlin**.

Найдите файл *build.gradle.kts* и откройте его в предпочитаемой интегрированной среде разработки или текстовом редакторе. Затем скопируйте и вставьте в файл приведенную ниже конфигурацию сборки. Эта конфигурация определяет проект как приложение Java, точкой входа которого является класс **ContentModeratorQuickstart**. Она импортирует пакет SDK для Content Moderator, а также пакет SDK для Gson для сериализации JSON.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

В рабочей папке выполните следующую команду, чтобы создать исходную папку проекта.

```console
mkdir -p src/main/java
```

Затем в новой папке создайте файл с именем *ContentModeratorQuickstart.java*. Откройте файл в предпочитаемом редакторе или интегрированной среде разработки и импортируйте следующие библиотеки вверху:

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Объектная модель

Следующие классы обрабатывают некоторые основные функции пакета SDK Java для Content Moderator.

|ИМЯ|ОПИСАНИЕ|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Этот класс требуется для всех функций Content Moderator. Вы создаете его экземпляр с информацией о подписке и используете его для создания экземпляров других классов.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Этот класс предоставляет функции для анализа изображений на наличие содержимого для взрослых, личной информации или лиц людей.|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Этот класс предоставляет функции для анализа текста с целью определения языка, наличия ненормативной лексики, ошибок и личной информации.|
|[Проверки](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Этот класс предоставляет функциональные возможности API-интерфейсов проверки, в том числе методы создания заданий, настраиваемых рабочих процессов и пользовательских проверок.|


## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки Content Moderator для Java:

* [аутентификация клиента](#authenticate-the-client);
* [Модерация изображений](#moderate-images)

## <a name="authenticate-the-client"></a>Аутентификация клиента

> [!NOTE]
> В этом шаге руководства предполагается, что вы уже [создали переменную среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа Content Moderator с именем `AZURE_CONTENTMODERATOR_KEY`.

В методе `main` приложения создайте объект [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable), используя значение конечной точки подписки и переменную среды ключа подписки. 

> [!NOTE]
> Если вы создали переменную среды после запуска приложения, для доступа к переменной следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Модерация изображений

### <a name="get-images"></a>Получение изображений

В папке **src/main/** вашего проекта создайте папку **resources** и перейдите в нее. Затем создайте текстовый файл *ImageFiles.txt*. В этом файле вы добавите URL-адреса изображений для анализа (по одному URL-адресу в каждой строке). Вы можете использовать следующий пример изображений:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Определение вспомогательного класса

Затем в файле *ContentModeratorQuickstart.java* добавьте следующее определение класса в класс **ContentModeratorQuickstart**. Этот внутренний класс будет использован позже в процессе модерации изображений.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Перебор изображений

Затем добавьте следующий код в конец метода `main`. Вы также можете добавить его в отдельный метод, который вызывается из метода `main`. Этот код проверяет каждую строку файла _ImageFiles.txt_.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Проверка на наличие содержимого для взрослых и содержимого непристойного характера
Эта строка кода проверяет изображение по указанному URL-адресу на предмет содержимого для взрослых и непристойного характера. Описание такого содержимого см. в концептуальном руководстве по модерации изображений.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Проверка на наличие текста
Эта строка кода проверяет изображение на наличие видимого текста.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Проверка на наличие лиц
Эта строка кода проверяет изображение на наличие видимых лиц людей.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Наконец, сохраните возвращенную информацию в списке `EvaluationData`.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Вывод результатов

После цикла `while` добавьте следующий код, который выводит результаты на консоль и в выходной файл *src/main/resources/ModerationOutput.json*.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Закройте оператор `try` и добавьте оператор `catch` для завершения метода.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Выполнение приложения

Чтобы создать приложение, выполните следующую команду:

```console
gradle build
```

Запустите приложение, выполнив команду `gradle run`:

```console
gradle run
```

Затем перейдите к файлу *src/main/resources/ModerationOutput.json* и просмотрите результаты модерации вашего содержимого.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как использовать библиотеку Java для Content Moderator для выполнения задач модерации. Теперь узнайте больше о модерации изображений или другого мультимедиа, прочитав концептуальное руководство.

> [!div class="nextstepaction"]
>[Принципы модерации изображений](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Что такое Azure Content Moderator?](./overview.md)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/blob/master/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java).