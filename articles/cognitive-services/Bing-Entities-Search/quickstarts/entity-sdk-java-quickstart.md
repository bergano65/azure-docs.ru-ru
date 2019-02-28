---
title: Краткое руководство. Поиск сущностей с помощью пакета SDK для Поиска сущностей Bing для Java
titlesuffix: Azure Cognitive Services
description: Используйте это краткое руководство для поиска сущностей с помощью пакета SDK для Поиска сущностей Bing для Java.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: ea13ae5bc649ae3a803fb8446fa009fac94874a8
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673570"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>Краткое руководство. Отправка запросов для поиска в пакете SDK для Поиска сущностей Bing для Java

Используйте это краткое руководство, чтобы начать поиск сущностей с помощью пакета SDK для Поиска сущностей Bing для Java. Так как для Поиска сущностей Bing имеется REST API, совместимый с большинством языков программирования, пакет SDK обеспечивает простой способ интеграции службы в ваши приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch).

## <a name="prerequisites"></a>Предварительные требования

* [Комплект разработчика Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/).

* Пакет SDK для Поиска сущностей Bing для Java.

Установите зависимости пакета SDK для API "Поиск сущностей Bing" с помощью Maven, Gradle или другой системы управления зависимостями. Для файла POM Maven требуется объявление:

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Создание и инициализация проекта

1. Создайте проект Java в любой интегрированной среде разработки или редакторе, а затем импортируйте в него следующие библиотеки.

    ```java
    import com.microsoft.azure.cognitiveservices.entitysearch.*;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.EntitySearchAPIImpl;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.SearchResponseInner;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List;
    ```

2. Создайте переменную для ключа подписки.

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>Создание клиента для поиска

2. Реализуйте клиент `dominantEntityLookup`, для которого требуется конечная точка API и экземпляр класса `ServiceClientCredentials`.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Чтобы запустить `ServiceClientCredentials`, выполните следующие действия.

    1. Переопределите функцию `applyCredentialsFilter()`, указав в качестве параметра объект `OkHttpClient.Builder`. 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. В рамках `applyCredentialsFilter()` вызовите `builder.addNetworkInterceptor()`. Создайте новый объект `Interceptor` и переопределите его метод `intercept()`, чтобы получить объект-перехватчик `Chain`.

        ```java
        //...
        builder.addNetworkInterceptor(
            new Interceptor() {
                @Override
                public Response intercept(Chain chain) throws IOException {
                //...    
                }
            });
        ///...
        ```

    3. В рамках функции `intercept` создайте переменные для вашего запроса. Используйте `Request.Builder()`, чтобы создать запрос. Добавьте ключ подписки к заголовку `Ocp-Apim-Subscription-Key` и верните `chain.proceed()` в объекте запроса.
            
        ```java
        //...
        public Response intercept(Chain chain) throws IOException {
            Request request = null;
            Request original = chain.request();
            Request.Builder requestBuilder = original.newBuilder()
                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request = requestBuilder.build();
            return chain.proceed(request);
        }
        //...
        ```
## <a name="send-a-request-and-receive-a-response"></a>Отправка запроса и получение ответа

1. Создайте новый экземпляр клиента для поиска с помощью ключа подписки. Используйте `client.entities().search()` для отправки запроса на поиск для поискового запроса `satya nadella` и получите ответ. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

2. Если возвращены все сущности, преобразуйте их в список. Выполните итерацию и напечатайте главную сущность.

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entrys = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entrys) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }
    ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по одностраничным веб-приложениям для наглядного поиска](../tutorial-bing-entities-search-single-page-app.md)

* [Основные сведения об API Bing для поиска сущностей](../overview.md )
