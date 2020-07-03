---
title: Краткое руководство по использованию клиентской библиотеки Поиска видео Bing для Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: 8124afef1aa12dbf3ec51e10597cb1567fc85551
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289894"
---
Используйте это краткое руководство, чтобы начать поиск новостей с помощью клиентской библиотеки Поиска видео Bing для Java. Поскольку REST API Поиска видео Bing совместим с большинством языков программирования, клиентская библиотека обеспечивает простой способ интеграции службы в ваши приложения. Исходный код этого примера можно получить на [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch) с дополнительными возможностями и заметками.

## <a name="prerequisites"></a>Предварительные требования

* [Комплект разработчика Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html).

* [Библиотека Gson](https://github.com/google/gson).

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

Установите зависимости клиентской библиотеки Поиска видео Bing с помощью Maven, Gradle или другой системы управления зависимостями. Для файла POM Maven требуется следующее объявление:

```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-videosearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies> 
```

## <a name="create-and-initialize-a-project"></a>Создание и инициализация проекта


Создайте проект Java в любой интегрированной среде разработки или редакторе, а затем импортируйте в него следующие библиотеки.

```java
    import com.microsoft.azure.cognitiveservices.videosearch.*;
    import com.microsoft.azure.cognitiveservices.videosearch.VideoObject;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List; 
```

## <a name="create-a-search-client"></a>Создание клиента для поиска

1. Реализуйте клиент `VideoSearchAPIImpl`, для которого требуется конечная точка API и экземпляр класса `ServiceClientCredentials`.

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
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

## <a name="send-a-search-request-and-receive-the-response"></a>Отправка поискового запроса и получение ответа 

1. Создайте функцию с именем `VideoSearch()`, принимающую ключ подписки в виде строки. Создайте экземпляр для созданного ранее клиента поиска.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. В рамках `VideoSearch()` отправьте запрос поиска видео с помощью клиента, указав `SwiftKey` в качестве поискового запроса. Если API Bing для поиска видео вернул результат, получите первый результат и напечатайте его идентификатор, имя и URL-адрес, а также общее количество возвращенных видео. 
    
    ```java
    VideosInner videoResults = client.searchs().list("SwiftKey");

    if (videoResults == null){
        System.out.println("Didn't see any video result data..");
    }
    else{
        if (videoResults.value().size() > 0){
            VideoObject firstVideoResult = videoResults.value().get(0);

            System.out.println(String.format("Video result count: %d", videoResults.value().size()));
            System.out.println(String.format("First video id: %s", firstVideoResult.videoId()));
            System.out.println(String.format("First video name: %s", firstVideoResult.name()));
            System.out.println(String.format("First video url: %s", firstVideoResult.contentUrl()));
        }
        else{
            System.out.println("Couldn't find video results!");
        }
    }
    ```

3. Вызовите метод поиска из метода main.

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. Одностраничное приложение для поиска видео](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>См. также раздел 

* [Что такое API Bing для поиска видео?](../../overview.md)
* [Примеры пакета SDK для .NET в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
