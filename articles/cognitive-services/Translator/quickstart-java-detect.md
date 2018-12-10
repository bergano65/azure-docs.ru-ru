---
title: Краткое руководство. Определение языка текста с помощью Java и API перевода текстов
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства вы узнаете, как с помощью REST API перевода текстов и Java определить язык заданного текста.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 12/03/2018
ms.author: erhopf
ms.openlocfilehash: d810b282936db1a31cdeb0133ce3c5bf0059850b
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890792"
---
# <a name="quickstart-use-the-translator-text-api-to-detect-text-language-using-java"></a>Краткое руководство. Определение языка текста с помощью Java и API перевода текстов

Из этого краткого руководства вы узнаете, как с помощью REST API перевода текстов и Java определить язык заданного текста.

Для этого краткого руководства требуется [учетная запись Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с ресурсом API перевода текстов. Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](https://azure.microsoft.com/try/cognitive-services/), чтобы получить ключ подписки.

## <a name="prerequisites"></a>Предварительные требования

* [JDK 7 или более поздней версии](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* ключ подписки Azure для API перевода текстов.

## <a name="initialize-a-project-with-gradle"></a>Инициализация проекта с помощью Gradle

Начнем с создания рабочего каталога для этого проекта. Из командной строки (или терминала) выполните приведенную ниже команду.

```console
mkdir detect-sample
cd detect-sample
```

После этого вы инициализируете проект Gradle. Что самое важное, эта команда создает необходимый файл сборки для Gradle (`build.gradle.kts`), который используется во время выполнения для создания и настройки приложения. Выполните следующую команду из рабочего каталога.

```console
gradle init --type basic
```

Когда появится запрос на выбор **предметно-ориентированного языка**, выберите **Kotlin**.

## <a name="configure-the-build-file"></a>Настройка файла сборки

Найдите файл `build.gradle.kts` и откройте его с помощью любой интегрированной среды разработки или текстового редактора. Затем скопируйте и вставьте в файл эту конфигурацию сборки:

```
plugins {
    java
    application
}
application {
    mainClassName = "Detect"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Примите во внимание, что этот пример зависим от OkHttp для HTTP-запросов и Gson для обработки и анализа JSON. Дополнительные сведения о конфигурациях сборки см. в разделе [Creating New Gradle Builds](https://guides.gradle.org/creating-new-gradle-builds/) (Создание сборок Gradle).

## <a name="create-a-java-file"></a>Создание файла Java

Создайте папку для примера приложения. Выполните следующую команду из рабочего каталога:

```console
mkdir -p src/main/java
```

Затем в этой папке создайте файл с именем `Detect.java`.

## <a name="import-required-libraries"></a>Импорт обязательных библиотек

Откройте файл `Detect.java` и добавьте следующие инструкции импорта:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Определение переменных

Сначала создайте открытый класс для проекта.

```java
public class Detect {
  // All project code goes here...
}
```

Добавьте эти строки в класс `Detect`:

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0";
```

## <a name="create-a-client-and-build-a-request"></a>Создание клиента и выполнение запроса

Добавьте приведенную ниже строку в класс `Detect` для создания экземпляра `OkHttpClient`.

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Затем создайте запрос POST. Текст для распознавания языка можно легко изменить.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Salve mondo!\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Создание функции для анализа ответа

Эта простая функция анализирует и упорядочивает ответ JSON от службы "Перевод текстов".

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Сборка

Последний шаг — это сделать запрос и получить ответ. Добавьте следующие строки в функцию:

```java
public static void main(String[] args) {
    try {
        Detect detectRequest = new Detect();
        String response = detectRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Запуск примера приложения

Теперь все готово к запуску примера приложения. В командной строке (или сеансе терминала) перейдите в основную часть каталога проекта и выполните следующую команду.

```console
gradle build
```

## <a name="sample-response"></a>Пример ответа

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с примерами кода из этого и других кратких руководств, включая перевод и транслитерацию, а также с другими примерами проектов перевода текстов в GitHub.

> [!div class="nextstepaction"]
> [Примеры для Java на сайте GitHub](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>См. также

* [перевод текста](quickstart-java-translate.md);
* [транслитерация текста](quickstart-java-transliterate.md);
* [определение языка по входным данным](quickstart-java-detect.md);
* [получение вариантов перевода](quickstart-java-dictionary.md);
* [получение списка поддерживаемых языков](quickstart-java-languages.md);
* [определение длины предложения на основе входных данных](quickstart-java-sentences.md).
