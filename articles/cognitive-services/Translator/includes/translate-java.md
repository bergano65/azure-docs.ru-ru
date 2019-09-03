---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 5a362d2610e6feb85de730c086070636f3afa2b9
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906664"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Инициализация проекта с помощью Gradle

Начнем с создания рабочего каталога для этого проекта. Из командной строки (или терминала) выполните приведенную ниже команду.

```console
mkdir translator-sample
cd translator-sample
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
    mainClassName = "Translate"
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

Затем в этой папке создайте файл с именем `Translate.java`.

## <a name="import-required-libraries"></a>Импорт обязательных библиотек

Откройте файл `Translate.java` и добавьте следующие инструкции импорта:

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
public class Translate {
  // All project code goes here...
}
```

Добавьте эти строки в класс `Translate`. Сначала ключ подписки и конечная точка считываются из переменных среды. Вы заметите, что кроме `api-version` в `url` были добавлены два дополнительных параметра. Эти параметры используются для настройки выходных данных перевода. В этом примере для параметров указан немецкий (`de`) и итальянский язык (`it`). 

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/translate?api-version=3.0&to=de,it";
```

Если вы используете подписку на несколько служб Cognitive Services, необходимо также включить `Ocp-Apim-Subscription-Region` в параметрах запроса. [Дополнительные сведения об аутентификации с использованием подписки на несколько служб](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Создание клиента и выполнение запроса

Добавьте приведенную ниже строку в класс `Translate` для создания экземпляра `OkHttpClient`.

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Затем создайте запрос POST. Текст для перевода можно легко изменить. Текст необходимо экранировать.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Welcome to Microsoft Translator. Guess how many languages I speak!\"\n}]");
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
        Translate translateRequest = new Translate();
        String response = translateRequest.Post();
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

По завершении сборки выполните следующую команду.

```console
gradle run
```

## <a name="sample-response"></a>Пример ответа

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Willkommen bei Microsoft Translator. Erraten Sie, wie viele Sprachen ich spreche!",
        "to": "de"
      },
      {
        "text": "Benvenuti a Microsoft Translator. Indovinate quante lingue parlo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Дополнительная информация

Просмотрите справочник по API, чтобы составить представление обо всех возможностях API "Перевод текстов".

> [!div class="nextstepaction"]
> [Справочник по API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
