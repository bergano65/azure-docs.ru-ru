---
title: Краткое руководство. Отправка запроса в API Bing для поиска местных компаний с помощью Java | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как приступить к работе с API Bing для поиска местных компаний с использованием Java.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: e55c06c7e8a2d0952cac809b622fa9a63ccee399
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959806"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-java"></a>Краткое руководство. Отправка запроса в API Bing для поиска местных компаний с помощью Java

С помощью этого краткого руководства вы сможете начать отправку запросов в API Bing для поиска местных компаний в Cognitive Services. Хотя это простое приложение написано на Java, API является веб-службой RESTful, совместимой с любым языком программирования, который может выполнять HTTP-запросы и анализировать JSON.

В этом примере приложения из API извлекаются сведения о местных компаниях по поисковому запросу `hotel in Bellevue`.

## <a name="prerequisites"></a>Предварительные требования

* [Комплект разработчика Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).

Необходима [учетная запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с API-интерфейсами поиска Bing. Для этого краткого руководства достаточно [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Вам потребуется ключ доступа, указанный при активации бесплатной пробной версии.

В этом примере приложения из API извлекаются сведения о местных компаниях по запросу *hotel in Bellevue* (отель в Бельвю).

## <a name="create-the-request"></a>Создание запроса 

Приведенный ниже код позволяет создать `WebRequest`, задать заголовок ключа доступа и добавить строку запроса "hotel in Bellevue" (отель в Бельвю).  Затем код отправляет запрос и назначает ответ строке, содержащей текст JSON.

````
    // construct URL of search request (endpoint + query string)
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "appid=AEA845921DC03F506DC317A90EDDBF33074523F7&traffictype=Internal_monitor&market=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    //connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
````

## <a name="run-the-complete-application"></a>Запуск готового приложения

API Bing для поиска местных компаний возвращает результаты из поисковой системы Bing.
1. Скачайте или установите библиотеку gson.
2. Создайте проект Java в используемой вами интегрированной среде разработки или редакторе.
3. Добавьте указанный ниже код.
4. Замените значение subscriptionKey ключом доступа, допустимым для вашей подписки.
5. Запустите программу.

````
package localSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (localSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac localSearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar localSearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class LocalSearchCls {

    // ***********************************************
    // *** Update or verify the following values. ***
    // **********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.cognitive.microsoft.com/bing";
        static String path = "/v7.0/localbusinesses/search";

        static String searchTerm = "Hotel in Bellevue";

        public static SearchResults SearchLocal (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + 
                         "&appid=" + subscriptionKey + "&traffictype=Internal_monitor&market=en-us");
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            //connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

            // receive JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();

            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);

            // extract Bing-related HTTP headers
            Map<String, List<String>> headers = connection.getHeaderFields();
            for (String header : headers.keySet()) {
                if (header == null) continue;      // may have null key
                if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                    results.relevantHeaders.put(header, headers.get(header).get(0));
                }
            }

            stream.close();
            return results;
        }

        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }

        public static void main (String[] args) {
            try {
                System.out.println("Searching the Web for: " + searchTerm);

                SearchResults result = SearchLocal(searchTerm);

                System.out.println("\nRelevant HTTP Headers:\n");
                for (String header : result.relevantHeaders.keySet())
                    System.out.println(header + ": " + result.relevantHeaders.get(header));

                System.out.println("\nJSON Response:\n");
                System.out.println(prettify(result.jsonResponse));
            }
            catch (Exception e) {
                e.printStackTrace(System.out);
                System.exit(1);
            }
        }
    }

    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }

````

## <a name="next-steps"></a>Дополнительная информация
- [Краткое руководство по API Bing для поиска местных компаний](local-quickstart.md)
- [Краткое руководство по API Bing для поиска местных компаний (Node)](local-search-node-quickstart.md)
- [Краткое руководство по API Bing для поиска местных компаний (Python)](local-search-python-quickstart.md)