---
title: Краткое руководство. Отправка запроса в API "Поиск местных компаний Bing" с помощью C#
titleSuffix: Azure Cognitive Services
description: С помощью этого краткого руководства вы сможете начать отправку запросов в Bing Local Business Search API в Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 127834034cf1de00b61794c6cfd51a041a37a8c3
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88925153"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-c"></a>Краткое руководство. Отправка запроса в API Bing Local Business Search с помощью C#

Из этого краткого руководства вы узнаете, как отправлять запросы в API Поиска местных компаний Bing в Azure Cognitive Services. Хотя это простое приложение написано на C#, API является веб-службой RESTful, совместимой с любым языком программирования, который может выполнять HTTP-запросы и анализировать JSON.

В этом примере приложения из API извлекаются сведения о местных компаниях по поисковому запросу.

## <a name="prerequisites"></a>Предварительные требования

* подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/).
* Любой выпуск [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Если вы используете Linux или MacOS, это приложение можно запустить с помощью [Mono](https://www.mono-project.com/).
* Получив подписку Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="создайте ресурс Поиска Bing"  target="_blank">Create a Bing Search resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.

## <a name="create-the-request"></a>Создание запроса 

Следующий код создает `WebRequest`, задает заголовок ключа доступа и добавляет строку запроса *restaurant in Bellevue* (ресторан в Бельвю).  Затем код отправляет запрос и назначает ответ строке, содержащей текст JSON.

```csharp
    // Replace the accessKey string value with your valid access key.
    const string accessKey = "enter key here";

    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search";   

    const string searchTerm = "restaurant in Bellevue";
    // Construct the URI of the search request
    var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + mkt=en-us;

    // Run the Web request and get response.
    WebRequest request = HttpWebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
```

## <a name="run-the-complete-application"></a>Запуск готового приложения

Следующий код использует API Поиска местных компаний Bing для возврата локализованных результатов поиска из поисковой системы Bing. Этот код можно использовать, выполнив приведенные ниже действия.
1. В Visual Studio (подойдет выпуск Community Edition) создайте консольное решение.
2. Замените файл Program.cs кодом, указанным ниже.
3. Замените значение `accessKey` ключом доступа, допустимым для подписки.
4. Запустите программу.

```csharp
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.IO;

namespace localSearch
{
    class Program
    {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace the accessKey string value with your valid access key.
        const string accessKey = "enter key here";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search";   

        const string searchTerm = "restaurant in Bellevue";

        // Returns search results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("Searching locally for: " + searchTerm);

            SearchResult result = BingLocalSearch(searchTerm, accessKey);

            Console.WriteLine("\nRelevant HTTP Headers:\n");
            foreach (var header in result.relevantHeaders)
                Console.WriteLine(header.Key + ": " + header.Value);

            Console.WriteLine("\nJSON Response:\n");
            Console.WriteLine(JsonPrettyPrint(result.jsonResult));

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Performs a Bing Local business search and return the results as a SearchResult.
        /// </summary>
        static SearchResult BingLocalSearch(string searchQuery, string key)
        {
            // Construct the URI of the search request
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + "&mkt=en-us";

            // Perform the Web request and get the response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = key; 

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            // Create result object for return
            var searchResult = new SearchResult();
            searchResult.jsonResult = json;
            searchResult.relevantHeaders = new Dictionary<String, String>();

            // Extract Bing HTTP headers
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    searchResult.relevantHeaders[header] = response.Headers[header];
            }

            return searchResult;
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
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
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
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }
    }
}

```

## <a name="next-steps"></a>Дальнейшие действия
- [Краткое руководство. Отправка запроса в API Bing для поиска местных компаний с помощью Java](local-search-java-quickstart.md)
- [Краткое руководство. Использование Поиска местных компаний с помощью Node.js](local-search-node-quickstart.md)
- [Краткое руководство. Отправка запроса в API Bing Local Business Search с помощью Python](local-search-python-quickstart.md)
