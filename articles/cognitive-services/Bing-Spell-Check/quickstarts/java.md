---
title: Краткое руководство. Проверка орфографии с помощью REST API проверки орфографии Bing и Java
titleSuffix: Azure Cognitive Services
description: Приступите к работе с REST API проверки орфографии Bing для проверки орфографии и грамматики.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: 6680ddd65ce43a71873acb823f8ae57b449a56be
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423518"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Краткое руководство. Проверка орфографии с помощью REST API проверки орфографии Bing и Java

В этом кратком руководстве показано, как отправить первый вызов к REST API "Проверка орфографии Bing". Это простое приложение Java отправляет запрос к API и возвращает список предлагаемых исправлений. Хотя это приложение создано на языке Java, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования. Исходный код этого приложения доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java).

## <a name="prerequisites"></a>Предварительные требования

* Пакет разработчиков Java (JDK) версии 7 или более поздней.

* Импортируйте [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) или последнюю версию [Gson](https://github.com/google/gson). Для выполнения операций командной строки добавьте `.jar` в папку Java с классом main.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Создание и инициализация приложения

1. Создайте проект Java в предпочитаемой интегрированной среде разработки или редакторе, указав имя класса на свой выбор, а затем импортируйте в него следующие пакеты.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Создайте переменные для узла конечной точки API, пути и ключа подписки. Затем создайте переменные для вашей отрасли, режима проверки орфографии и текста, который требуется проверить.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Создание и отправка запроса API

1. Создайте функцию с именем `check()`, чтобы создать и отправить запрос API. В рамках этой функции выполните следующие действия. Создайте строку для параметров запроса. Добавьте строку, содержащую параметр `?mkt=` со значением вашей отрасли и параметр `&mode=` со значением режима проверки орфографии.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Создайте URL-адрес, объединив узел конечной точки, путь и строку параметров. Создайте объект `HttpsURLConnection`.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Установите подключение к URL-адресу. Установите метод запроса `POST`. Добавьте параметры запроса. Не забудьте добавить ключ подписки в заголовок `Ocp-Apim-Subscription-Key`.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Создайте новый объект `DataOutputStream` и отправьте запрос к API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Форматирование и считывание ответа API

1. Добавьте следующий метод в класс. Он преобразовывает JSON в более понятные выходные данные.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Создайте `BufferedReader` и считайте ответ из API. Выведите его на консоль.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line);
    }
    in.close();
    ```

## <a name="call-the-api"></a>Вызов API

В функции main приложения вызовите метод check(), созданный ранее.

    ```java
    public static void main(String[] args) {
        try {
            check();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
    ```

## <a name="run-the-application"></a>Выполнение приложения

Выполните сборку проекта и запустите его.

При использовании командной строки выполните приведенные ниже команды для сборки и запуска приложения.

**Сборка**:
```bash
javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
```

**Запуск:**
```bash
java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
```

## <a name="example-json-response"></a>Пример ответа в формате JSON

Успешный ответ возвращается в формате JSON, как показано в примере ниже.

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](../tutorials/spellcheck.md)

- [Что такое API проверки орфографии Bing?](../overview.md)
- [Справочник по API Проверки орфографии Bing версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
