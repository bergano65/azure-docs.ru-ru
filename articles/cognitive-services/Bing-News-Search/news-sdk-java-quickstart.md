---
title: Краткое руководство. Поиск новостей с помощью пакета SDK Поиска новостей Bing для Java
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как искать новости с помощью пакета SDK Поиска новостей Bing для Java и обрабатывать ответы.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: b128f43637649f4faf104a81940f2937c2354bc6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799726"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>Краткое руководство. Поиск новостей с помощью пакета SDK API Bing для поиска новостей для Java

Используйте это краткое руководство, чтобы начать поиск новостей с помощью пакета SDK API Bing для поиска новостей для Java. Поскольку REST API Поиска новостей Bing совместим с большинством языков программирования, пакет SDK обеспечивает простой способ интеграции службы в ваши приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch).

## <a name="prerequisites"></a>Предварительные требования

Установите зависимости пакета SDK API Bing для поиска новостей с помощью Maven, Gradle или другой системы управления зависимостью. Для файла POM Maven требуется следующее объявление:

```xml
    <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
    </dependencies>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Создание и инициализация проекта

Создайте проект Java в любой интегрированной среде разработки или редакторе, а затем импортируйте в него следующие библиотеки.

```java
import com.microsoft.azure.cognitiveservices.newssearch.*;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsInner;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsSearchAPIImpl;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.TrendingTopicsInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import java.io.IOException;
```

## <a name="create-a-search-client-and-store-credentials"></a>Создание клиента для поиска и хранение учетных данных

1. Создайте метод `getClient()`, возвращающий новый клиент для поиска `NewsSearchAPIImpl`. Добавьте конечную точку в качестве первого параметра для нового объекта `NewsSearchAPIImpl` и нового объекта `ServiceClientCredentials` для хранения учетных данных.

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. Чтобы создать объект `ServiceClientCredentials`, переопределите функцию `applyCredentialsFilter()`. Передайте `OkHttpClient.Builder` в метод и используйте метод `addNetworkInterceptor()`построителя, чтобы создать учетные данные для вызова пакета SDK.

    ```java
    new ServiceClientCredentials() {
        @Override
        public void applyCredentialsFilter(OkHttpClient.Builder builder) {
            builder.addNetworkInterceptor(
                    new Interceptor() {
                        @Override
                        public Response intercept(Chain chain) throws IOException {
                            Request request = null;
                            Request original = chain.request();
                            // Request customization: add request headers.
                            Request.Builder requestBuilder = original.newBuilder()
                                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                            request = requestBuilder.build();
                            return chain.proceed(request);
                        }
                    });
        }
    });
    ```

## <a name="send-and-receive-a-search-request"></a>Отправка и получение поискового запроса

1. Создайте метод, который вызывает `getClient()` и отправляет поисковый запрос службе "Поиск новостей Bing". Фильтруйте поиск за параметрами *market* и *count*, затем введите сведения о первом результате новостей: имя, URL-адрес, дата публикации, описание, имя поставщика и общее количество предполагаемых совпадений для вашего поиска.

    ```java
    public static void newsSearch(String subscriptionKey)
    {
        NewsSearchAPIImpl client = getClient(subscriptionKey);
        String searchTerm = "Quantum Computing";
    
        NewsInner newsResults = client.searchs().list(searchTerm, null, null, null,
                null, null, 100, null, "en-us",
                null, null, null, null, null,
                null, null);
    
        if (newsResults.value().size() > 0)
        {
            NewsArticle firstNewsResult = newsResults.value().get(0);
    
            System.out.println(String.format("TotalEstimatedMatches value: %d", newsResults.totalEstimatedMatches()));
            System.out.println(String.format("News result count: %d", newsResults.value().size()));
            System.out.println(String.format("First news name: %s", firstNewsResult.name()));
            System.out.println(String.format("First news url: %s", firstNewsResult.url()));
            System.out.println(String.format("First news description: %s", firstNewsResult.description()));
            System.out.println(String.format("First news published time: %s", firstNewsResult.datePublished()));
            System.out.println(String.format("First news provider: %s", firstNewsResult.provider().get(0).name()));
        }
        else
        {
            System.out.println("Couldn't find news results!");
        }
    
    }
    
    ```

2. Добавьте ваш метод поиска к методу `main()` для выполнения кода.

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](tutorial-bing-news-search-single-page-app.md)
