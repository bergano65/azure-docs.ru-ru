---
title: Краткое руководство. Пользовательский поиск Bing для Java | Документация Майкрософт
description: Начало работы с клиентской библиотекой Пользовательского поиска Bing для Java, с запроса результатов поиска из экземпляра Пользовательского поиска Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: aahi
ms.openlocfilehash: 7d1a7b2cdba082c78a1753ea0dcce6ead02ab036
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148323"
---
# <a name="quickstart-bing-custom-search-client-library-for-java"></a>Краткое руководство. Клиентская библиотека Пользовательского поиска Bing для Java

Приступите к работе с клиентской библиотекой службы Пользовательский поиск Bing для Java. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. Служба "API пользовательского поиска Bing" позволяет создавать специально адаптированные интерфейсы поиска без рекламы по темам, которые действительно важны для вас.

Используйте клиентскую библиотеку Пользовательского поиска Bing для Java, чтобы:

* Найти результаты поиска в Интернете из вашего экземпляра API Пользовательского поиска Bing. 

[Справочная документация](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [Артефакт (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [Примеры](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [пакета средств разработки Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Средство сборки Gradle](https://gradle.org/install/) или другой диспетчер зависимостей.
* Экземпляр службы "Пользовательский поиск Bing". См. [Краткое руководство. Создание первого экземпляра службы "Пользовательский поиск Bing"](quick-start.md), чтобы получить дополнительные сведения.

## <a name="setting-up"></a>Настройка

### <a name="create-a-bing-custom-search-azure-resource"></a>Создание ресурса для Пользовательского поиска Bing

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс для Пользовательского поиска Bing с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) на локальном компьютере. Также можно:

* получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services/#decision) на 7 дней. После регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Просмотреть этот ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки [задайте переменную среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа с именем `AZURE_BING_CUSTOM_SEARCH_API_KEY`.

### <a name="create-a-new-gradle-project"></a>Создание проекта Gradle

> [!TIP]
> Если вы не используете Gradle, тогда клиентскую библиотеку и сведения для других диспетчеров зависимостей можно найти в [центральном репозитории Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. 

```console
mkdir myapp && cd myapp
```

Выполните команду `gradle init` из рабочей папки. Эта команда создает необходимые файлы сборки для Gradle, включая файл *build.gradle.kts*, который используется во время выполнения для настройки приложения.

```console
gradle init --type basic
```

Когда появится запрос на выбор **предметно-ориентированного языка**, выберите **Kotlin**.

## <a name="install-the-client-library"></a>Установка клиентской библиотеки 

Найдите файл *build.gradle.kts* и откройте его в предпочитаемой интегрированной среде разработки или текстовом редакторе. Затем скопируйте и вставьте в файл эту конфигурацию сборки. Не забудьте включить клиентскую библиотеку в `dependencies`:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Создайте папку для примера приложения. Выполните следующие команды из рабочего каталога:

```console
mkdir src/main/java
```

Перейдите в новую папку и создайте файл с именем *BingCustomSearchSample.java*. Откройте его `import` и добавьте следующие операторы:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Создайте класс с именем `BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

В классе `main` создайте метод и переменные для конечной точки и ключа Azure вашего ресурса. Если вы создали переменную среды после запуска приложения, для доступа к переменной закройте и повторно откройте редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена. Эти методы будут определены позже.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Объектная модель

Клиент службы "Пользовательский поиск Bing" является объектом [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable), созданным из объекта [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable), а именно его метода [аутентификации ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_). Запрос поиска можно отправить с помощью метода клиента [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__).

Ответ API — это объект [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable), содержащий информацию о запросе и результатах поиска.

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки службы "Пользовательский поиск Bing" для Java:

* [аутентификация клиента](#authenticate-the-client);
* [Получение результатов поиска из пользовательского экземпляра](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Аутентификация клиента

Ваш основной метод должен включать в себя объект [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable), который берет ваш ключ и вызывает его `authenticate()`.

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Получение результатов поиска из пользовательского экземпляра

Используйте функцию клиента [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__), чтобы отправить поисковый запрос в пользовательский экземпляр. Установите `withCustomConfig` в свой пользовательский идентификатор конфигурации или по умолчанию на `1`. После получения ответа от API проверьте, были ли найдены результаты поиска. Если да, то получите первый результат поиска, вызвав функцию ответа `webPages().value().get()` и выполнив печать имени результата и URL-адреса. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Выполнение приложения

Выполните сборку приложения с помощью следующей команды из главного каталога проекта:

```console
gradle build
```

Запустите приложение, выполнив цель `run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание веб-страницы пользовательского поиска](./tutorials/custom-search-web-page.md)
