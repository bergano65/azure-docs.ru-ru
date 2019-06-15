---
title: Использование токена аналитических сведений — Визуальный поиск Bing
titleSuffix: Azure Cognitive Services
description: В этой статье показано, как использовать токен аналитических сведений об изображении с помощью API визуального поиска Bing для получения полезных сведений об изображении.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 4/26/2019
ms.author: scottwhi
ms.openlocfilehash: 1860b788b9f33a6a2ec806be68a67aa2df10cbd4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65914271"
---
# <a name="use-an-insights-token-to-get-insights-for-an-image"></a>Использовать маркер insights получение аналитики для изображения

API Bing для наглядного поиска возвращает сведения об изображении, которое вы предоставляете. Изображение можно предоставить с помощью URL-адреса изображения, токена аналитики или через отправку изображения. Сведения об этих способах см. в статье [What is Bing Visual Search API?](overview.md) (Что такое API Bing для визуального поиска?). В этой статье демонстрируется использование токена аналитики. Дополнительные примеры, демонстрирующие способы отправки изображения для получения ценной информации, см. краткие руководства ([C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [Node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md)).

Если маркер изображения или URL-адрес, отправить визуального поиска Bing, в следующем примере показан данные формы необходимо включить в тексте запроса POST. Данные формы должен включать `Content-Disposition` заголовка и вам необходимо задать его `name` параметр «knowledgeRequest». Дополнительные сведения о `imageInfo` объекта, см. в разделе запрос:

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

В примерах в этой статье показано, как использовать токен аналитики. Вы получаете маркер insights из `Image` объекта в / Images/search ответ API. Сведения о получении маркера insights, см. в разделе [что такое API поиска изображений Bing?](../Bing-Image-Search/overview.md).

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFEDF3D40...",
    }
}

--boundary_1234-abcd--
```

Примеры использования токена аналитики см. в статьях о [C#](#use-with-c) | [Java](#use-with-java) | [Node.js](#use-with-nodejs) | [Python](#use-with-python).

## <a name="use-with-c"></a>Использование сC#

### <a name="c-prerequisites"></a>C#Предварительные требования

- Любая версия [Visual Studio 2019](https://www.visualstudio.com/downloads/) для получения этого кода, работающего в Windows.
- Подписка Azure. В этом кратком руководстве можно использовать [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) подписки или ключа платную подписку.

## <a name="run-the-application"></a>Выполнение приложения

Чтобы запустить это приложение, сделайте следующее:

1. Создание решения консоли в Visual Studio.
2. Замените содержимое Program.cs кодом, показанным в этом кратком руководстве.
3. Замените значение `accessKey` своим ключом подписки.
4. Замените значение `insightsToken` токеном аналитики из ответа /images/search.
5. Запустите программу.

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using System.Threading;

namespace VisualSearchInsightsToken
{

    class Program
    {
        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Update with an insights token from an image object that the /images/search API endpoint returns.
        static string insightsToken = @"ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

        static string BoundaryTemplate = "batch_{0}";

        static void Main(string[] args)
        {
            try { 
                Console.WriteLine("Getting image insights using insights token: " + insightsToken);

                var knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";
                var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());

                var json = BingVisualSearch(knowledgeRequest, boundary, uriBase, accessKey);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(json));

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

        }


        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response with insights.
        /// </summary>
        static string BingVisualSearch(string knowledgeRequest, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new StringContent(knowledgeRequest, Encoding.UTF8, "application/json"), "knowledgeRequest");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }


            return json;
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
    }
}
```

## <a name="use-with-java"></a>Использования с Java

### <a name="java-prerequisites"></a>Предварительные требования для Java

- Необходимо использовать [JDK 7 или 8](https://aka.ms/azure-jdks) для компиляции и запуска этого кода. Это интегрированная среда разработки Java можно использовать, если у вас есть избранного, но подойдет в текстовом редакторе.
- В этом кратком руководстве можно использовать [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) подписки или ключа платную подписку.

## <a name="run-the-java-application"></a>Запустите приложение Java

Чтобы запустить это приложение, сделайте следующее:

1. Скачать или установить [библиотеки Gson Java](https://github.com/google/gson). Вы также можете получить Gson через Maven.
2. Создайте проект Java в используемой вами интегрированной среде разработки или редакторе.
3. Добавьте указанный код в файл с именем `VisualSearch.java`.
4. Замените значение `subscriptionKey` своим ключом подписки.
5. Запустите программу.

```java
package insightstoken;

import java.util.*;
import java.io.*;



/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// https://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class InsightsToken {

    
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere>";

    // To get an insights, call the /images/search endpoint. Get the token from
    // the imageInsightsToken field in the Image object.
    static String insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        String knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";

        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addTextBody("knowledgeRequest", knowledgeRequest, ContentType.create("application/json"))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }


}
```

## <a name="use-with-nodejs"></a>Использование с помощью Node.js

### <a name="nodejs-prerequisites"></a>Предварительные требования для node.js

- Необходимо иметь [Node.js 6](https://nodejs.org/en/download/) для запуска этого кода.
- В этом кратком руководстве можно использовать [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) подписки или ключа платную подписку.

## <a name="run-the-javascript-application"></a>Запустите приложение JavaScript

Чтобы запустить это приложение, сделайте следующее:

1. Создайте папку для проекта (либо воспользуйтесь выбранной интегрированной средой разработки или редактором).
2. Из командной строки или терминала перейдите в эту папку.
3. Установите модули request:
  
   ```
   npm install request  
   ```
1. Установите модули form-data:  
   ```
   npm install form-data  
   ```
1. Создайте файл с именем GetVisualInsights.js и добавьте в него следующий код.
1. Замените значение `subscriptionKey` своим ключом подписки.
1. Запустите программу.  
   ```
   node GetVisualInsights.js
   ```

```javascript
var request = require('request');
var FormData = require('form-data');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';

// To get an insights, call the /images/search endpoint. Get the token from
// the imageInsightsToken field in the Image object.
var insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

var knowledgeRequest = {
    "imageInfo" : {
        "imageInsightsToken" : insightsToken
    }
};

var form = new FormData();
form.append('knowledgeRequest', JSON.stringify(knowledgeRequest));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```

## <a name="use-with-python"></a>Использование с Python

### <a name="python-prerequisites"></a>Предварительные требования для Python

- Необходимо иметь [Python 3](https://www.python.org/) для запуска этого кода.
- В рамках этого краткого руководства можно использовать ключ [бесплатной пробной](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) подписки или ключ платной подписки.

## <a name="run-the-python-application"></a>Запуск приложения Python

Чтобы запустить это приложение, сделайте следующее:

1. Создайте проект Python в любой интегрированной среде разработки или в текстовом редакторе.
2. Создайте файл с именем visualsearch.py и добавьте в него код, приведенный в этом кратком руководстве.
3. Замените значение `SUBSCRIPTION_KEY` своим ключом подписки.
4. Запустите программу.

```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from
# the imageInsightsToken field in the Image object.
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest' : (None, formData)}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Дальнейшие действия

[Create a Visual Search single-page web app](tutorial-bing-visual-search-single-page-app.md) (Создание одностраничного веб-приложения Визуального поиска)  
[Что такое API визуального поиска Bing?](overview.md)  
[Пробная версия Cognitive Services](https://aka.ms/bingvisualsearchtryforfree)  
[Получить ключ доступа бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Образы — визуального поиска](https://aka.ms/bingvisualsearchreferencedoc)
