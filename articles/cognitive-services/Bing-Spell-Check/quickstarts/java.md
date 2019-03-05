---
title: Краткое руководство. Проверка орфографии с помощью REST API проверки орфографии Bing и Java
titlesuffix: Azure Cognitive Services
description: Приступите к работе с REST API проверки орфографии Bing для проверки орфографии и грамматики.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: d2905d05dce48b705de44780425ed2b55b02555c
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888990"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Краткое руководство. Проверка орфографии с помощью REST API проверки орфографии Bing и Java

В этом кратком руководстве показано, как отправить первый вызов к REST API "Проверка орфографии Bing". Это простое приложение Java отправляет запрос к API и возвращает список предлагаемых исправлений. Хотя это приложение создано на языке Java, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования. Исходный код этого приложения доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java).

## <a name="prerequisites"></a>Предварительные требования

Пакет разработчиков Java (JDK) версии 7 или более поздней.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-an-application"></a>Создание и инициализация приложения

1. Создайте проект Java в любой интегрированной среде разработки или редакторе, а затем импортируйте в него следующие пакеты.

    ```java
    import java.io.*;
    import java.net.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Создайте переменные для узла конечной точки API, пути и ключа подписки. Затем создайте переменные для вашей отрасли, режима проверки орфографии и текста, который требуется проверить.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "ENTER YOUR KEY HERE";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Создание и отправка запроса API

1. Создайте функцию с именем `check()`, чтобы создать и отправить запрос API. В рамках этой функции выполните следующие действия. Создайте строку для параметров запроса. Добавьте строку, содержащую параметр `?mkt=` со значением вашей отрасли и параметр `&mode=` со значением режима проверки орфографии.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
   //...
   }
   ```

2. Создайте URL-адрес, объединив узел конечной точки, путь и строку параметров. Создайте новый объект `HttpsURLConnection`.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) 
    ```

3. Установите подключение к URL-адресу. Установите метод запроса `POST`. Добавьте параметры запроса. Не забудьте добавить ключ подписки в заголовок `Ocp-Apim-Subscription-Key`. 

    ```java
    url.openConnection();
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

## <a name="read-the-response"></a>Считывание ответа

1. Создайте `BufferedReader` и считайте ответ из API. Выведите его на консоль.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(line);
    }
    in.close();
    ```

2. В главной функции приложения вызовите функцию, созданную выше. 

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
- [Справочник по API проверки орфографии Bing версии 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
