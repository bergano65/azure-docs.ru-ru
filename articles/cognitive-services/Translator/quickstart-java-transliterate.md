---
title: Краткое руководство. Транслитерация текста, Java — API перевода текстов
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства вы узнаете, как транслитерировать (преобразовывать) текст из одного сценария в другой с помощью Java и REST API перевода текстов. В этом примере японский текст транслитерируется в текст на латинице.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 12/03/2018
ms.author: erhopf
ms.openlocfilehash: e42aa7ec796b3be912614ced5f3a4db47f67c124
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210069"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-java"></a>Краткое руководство. Транслитерация текста с помощью Java и API перевода текстов

Из этого краткого руководства вы узнаете, как транслитерировать (преобразовывать) текст из одного сценария в другой с помощью Java и REST API перевода текстов. В приведенном примере японский текст транслитерируется в текст на латинице.

Для этого краткого руководства требуется [учетная запись Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с ресурсом API перевода текстов. Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](https://azure.microsoft.com/try/cognitive-services/), чтобы получить ключ подписки.

## <a name="prerequisites"></a>Предварительные требования

* [JDK 7 или более поздней версии](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* ключ подписки Azure для API перевода текстов.

## <a name="initialize-a-project-with-gradle"></a>Инициализация проекта с помощью Gradle

Начнем с создания рабочего каталога для этого проекта. Из командной строки (или терминала) выполните приведенную ниже команду.

```console
mkdir transliterate-sample
cd transliterate-sample
```

После этого вы инициализируете проект Gradle. Эта команда создает необходимые файлы сборки для Gradle, прежде всего `build.gradle.kts`, который используется во время выполнения для создания и настройки приложения. Выполните следующую команду из рабочего каталога.

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
    mainClassName = "Transliterate"
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

Затем в этой папке создайте файл с именем `Transliterate.java`.

## <a name="import-required-libraries"></a>Импорт обязательных библиотек

Откройте файл `Transliterate.java` и добавьте следующие инструкции импорта:

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
public class Transliterate {
  // All project code goes here...
}
```

Добавьте эти строки в класс `Transliterate`. Вы заметите, что кроме `api-version` в `url` добавлены два дополнительных параметра. Эти параметры позволяют указать язык ввода и наборы символов для транслитерации. В этом примере в качестве значений для параметров указан японский язык (`jpan`) и латиница (`latn`). Обязательно обновите значение ключа подписки.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
```

## <a name="create-a-client-and-build-a-request"></a>Создание клиента и выполнение запроса

Добавьте приведенную ниже строку в класс `Transliterate` для создания экземпляра `OkHttpClient`.

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Далее создайте запрос POST. Текст для транслитерации можно легко изменить.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"こんにちは\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Создайте функцию для анализа ответа

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
        Transliterate transliterateRequest = new Transliterate();
        String response = transliterateRequest.Post();
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
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с примерами кода из этого и других кратких руководств, которые демонстрируют перевод и определение языка, а также с другими примерами проектов для API перевода текстов на сайте GitHub.

> [!div class="nextstepaction"]
> [Примеры для Java на сайте GitHub](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>См. также

* [перевод текста](quickstart-java-translate.md);
* [определение языка по входным данным](quickstart-java-detect.md);
* [получение вариантов перевода](quickstart-java-dictionary.md);
* [получение списка поддерживаемых языков](quickstart-java-languages.md);
* [определение длины предложения на основе входных данных](quickstart-java-sentences.md).
