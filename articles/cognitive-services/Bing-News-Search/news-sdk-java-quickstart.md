---
title: Краткое руководство. Использование пакета SDK для API Bing для поиска новостей (Java)
titleSuffix: Azure Cognitive Services
description: Узнайте, как установить консольное приложение пакета SDK для API "Поиск новостей Bing".
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/16/2018
ms.author: v-gedod
ms.openlocfilehash: 7919f13e8c5aaa592a416190d3cd11edbf31af2c
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802483"
---
# <a name="quickstart-bing-news-search-sdk-with-java"></a>Краткое руководство. Использование пакета SDK API Bing для поиска новостей для Java

Пакет SDK для API "Поиск новостей Bing" предоставляет функциональные возможности интерфейса REST API для поиска новостей и анализа результатов. 

[Исходный код примеров для пакета SDK для службы "Поиск новостей Bing" для Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) доступен на сайте GitHub.

## <a name="application-dependencies"></a>Зависимости приложения
Получите [ключ доступа Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) в разделе **Поиск**. Установите зависимости пакета SDK для API "Поиск новостей Bing" с помощью Maven, Gradle или другой системы управления зависимостями. Для файла POM Maven требуется объявление:
```
  <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies>
```
## <a name="news-search-client"></a>Клиент для API "Поиск новостей Bing"
В реализацию класса добавьте элементы import.
```
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
Реализуйте клиент **NewsSearchAPIImpl**. Для этого требуется экземпляр класса **ServiceClientCredentials**.
```
public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
    return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
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
}


```
Выполните поиск новостей по одному запросу "Quantum Computing" (квантовые вычисления). Отфильтруйте результаты поиска с помощью параметров *market* и *count*. Проверьте количество результатов. Выведите сведения о первом результате из списка новостей: имя, URL-адрес, дата публикации, описание, имя поставщика и общее количество предполагаемых совпадений.
```
public static void newsSearch(String subscriptionKey)
{
    NewsSearchAPIImpl client = getClient(subscriptionKey);

    try
    {
        NewsInner newsResults = client.searchs().list("Quantum  Computing", null, null, null,
                null, null, 100, null, "en-us",
                null, null, null, null, null,
                null, null);

        System.out.println("\r\nSearch news for query \"Quantum  Computing\" with market and count");

        if (newsResults == null)
        {
            System.out.println("Didn't see any news result data..");
        }
        else
        {
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
    }

    catch (Exception ex)
    {
        System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}

```
Выполните поиск последних новостей по запросу "Artificial Intelligence" (искусственный интеллект). Отфильтруйте результаты поиска с помощью параметров *freshness* и *sortBy*. Проверьте количество результатов. Выведите сведения о первом результате из списка новостей: имя, URL-адрес, дата публикации, описание, имя поставщика и общее количество предполагаемых совпадений.
```
/**
 * Search recent news for (Artificial Intelligence) with the freshness and sortBy parameters.
 * Verify the number of results. Print the totalEstimatedMatches, name, url, description,
 * published time, and provider name for the first news result.
 * @param subscriptionKey cognitive services subscription key
 */
public static void newsSearchWithFilters(String subscriptionKey)
{
    NewsSearchAPIImpl client = getClient(subscriptionKey);

    try
    {
        NewsInner newsResults = client.searchs().list("Artificial Intelligence", null, null, null, null, null,
                    null, Freshness.WEEK, "en-us", null, null, null,
                    null, "Date", null, null);
        System.out.println("\r\nSearch most recent news for query \"Artificial Intelligence\" with freshness and sortBy");

        if (newsResults == null)
        {
            System.out.println("Didn't see any news result data..");
        }
        else
        {
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
    }

    catch (Exception ex)
    {
        System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}

```
Выполните поиск в **категории** новостей по темам *movie and TV entertainment* (фильмы и развлекательные телепрограммы) и воспользуйтесь функцией *SafeSearch* (Безопасный поиск). Проверьте количество результатов. Выведите категорию, имя, URL-адрес, описание, дату публикации и имя поставщика для первого результата из списка новостей.
```
/**
 * Search the news category for (movie and TV entertainment) with safe search. Verify the number of results. 
 * Print the category, name, url, description, published time, and provider name for the first news result.
 * @param subscriptionKey cognitive services subscription key
 */
public static void newsCategory(String subscriptionKey)
{
    NewsSearchAPIImpl client = getClient(subscriptionKey);

    try
    {
        NewsInner newsResults = client.categorys().list(null, null, null, null, null, "Entertainment_MovieAndTV",
                null, null, "en-us", null, null, SafeSearch.STRICT,
                null, null, null);
        System.out.println("\r\nSearch category news for movie and TV entertainment with safe search");

        if (newsResults == null)
        {
            System.out.println("Didn't see any news result data..");
        }
        else
        {
            if (newsResults.value().size() > 0)
            {
                NewsArticle firstNewsResult = newsResults.value().get(0);

                System.out.println(String.format("News result count: %d", newsResults.value().size()));
                //System.out.println(String.format("First news category: %d", firstNewsResult.category()));
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
    }

    catch (Exception ex)
    {
        System.out.println("Encountered exception. " + ex.getLocalizedMessage()
        );
    }
}

```
Выполните поиск новостей, набирающих популярность. Проверьте количество результатов. Выведите имя, текст запроса, URL-адрес веб-поиска и URL-адрес поиска новостей для первого результата из списка новостей.
```
public static void trendingTopics(String subscriptionKey)
{
    NewsSearchAPIImpl client = getClient(subscriptionKey);

    try
    {
        TrendingTopicsInner trendingTopics = client.trendings().list(null, null, null, null, null, null,
                "en-us", null, null, null, null, null, null, null);
        System.out.println("\r\nSearch news trending topics in Bing");

        if (trendingTopics == null)
        {
            System.out.println("Didn't see any news trending topics..");
        }
        else
        {
            if (trendingTopics.value().size() > 0)
            {
                NewsTopic firstTopic = trendingTopics.value().get(0);

                System.out.println(String.format("Trending topics count: %s", trendingTopics.value().size()));
                System.out.println(String.format("First topic name: %s", firstTopic.name()));
                System.out.println(String.format("First topic query: %s", firstTopic.query().text()));
                System.out.println(String.format("First topic image url: %s", firstTopic.image().url()));
                System.out.println(String.format("First topic webSearchUrl: %s", firstTopic.webSearchUrl()));
                System.out.println(String.format("First topic newsSearchUrl: %s", firstTopic.newsSearchUrl()));
            }
            else
            {
                System.out.println("Couldn't find news trending topics!");
            }
        }
    }

    catch (Exception ex)
    {
        System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}
```
Добавьте методы, описанные в этой статье, в класс с функцией main для выполнения кода.
```
package javaNewsSDK;
import com.microsoft.azure.cognitiveservices.newssearch.*;

public class NewsSearchSDK {
    

    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch("YOUR-SUBSCRIPTION-KEY");
        NewsSearchSDK.newsSearchWithFilters("YOUR-SUBSCRIPTION-KEY");
        NewsSearchSDK.newsCategory("YOUR-SUBSCRIPTION-KEY");
        NewsSearchSDK.trendingTopics("YOUR-SUBSCRIPTION-KEY");
    }

    // Include the methods described in this article.
}
```
## <a name="next-steps"></a>Дополнительная информация

[Примеры пакета SDK для Java в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)


