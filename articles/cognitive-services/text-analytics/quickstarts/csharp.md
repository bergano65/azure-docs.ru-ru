---
title: Краткое руководство. Вызов API анализа текста с использованием C#
titleSuffix: Azure Cognitive Services
description: Информация и примеры кода, которые помогут вам приступить к работе с API анализа текста.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: ce3629a140db97e922a28792c6230d9566682982
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269332"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Краткое руководство по использованию C# для вызова API анализа текста Cognitive Services
<a name="HOLTop"></a>

В этой статье содержатся сведения об обнаружении языка, анализе тональности и извлечении ключевых фраз с использованием [API анализа текста ](//go.microsoft.com/fwlink/?LinkID=759711) для C#. Код был написан для работы в приложении .NET Core с минимальными ссылками на внешние библиотеки, поэтому его также можно выполнять в Linux или MacOS.

Техническую документацию по API-интерфейсам см. в разделе [API definitions](//go.microsoft.com/fwlink/?LinkID=759346) (Определения API).

## <a name="prerequisites"></a>Предварительные требования

Необходимо иметь [учетную запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с **API анализа текста**. Для выполнения действий, указанных в этом кратком руководстве, можно использовать подписку **уровня "Бесплатный" на 5000 транзакций в месяц**.

Также требуются [конечная точка и ключ доступа](../How-tos/text-analytics-how-to-access-key.md), созданный автоматически во время регистрации. 


## <a name="install-the-nuget-sdk-package"></a>Установка пакета SDK для пакетов NuGet
1. В Visual Studio создайте консольное приложение.
1. Щелкните решение правой кнопкой мыши и выберите пункт **Управление пакетами NuGet для решения**.
1. Установите флажок **Включить предварительные выпуски**.
1. Откройте вкладку **Обзор** и найдите **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**.
1. Выберите пакет NuGet и установите его.

> [!Tip]
>  Несмотря на то, что [конечные точки HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) можно вызывать напрямую из C#, пакет SDK Microsoft.Azure.CognitiveServices.Language значительно упрощает процесс вызова службы без сериализации и десериализации JSON.
>
> Несколько полезных ссылок
> - [Страница пакета SDK для NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Код пакета SDK](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>Вызов API анализа текста с помощью пакета SDK
1. Замените файл Program.cs на код, указанный ниже. Эта программа показывает возможности API анализа текста в трех разделах (извлечение языка, извлечение ключевой фразы и анализ тональности).
1. Замените значение заголовка `Ocp-Apim-Subscription-Key` с ключом доступа, допустимым для вашей подписки.
1. Замените расположение в `Endpoint` конечной точкой, на которой в зарегистрированы. Эту конечную точку можно найти в ресурсе на портале Azure. Как правило, конечная точка начинается с "https://[регион].api.cognitive.microsoft.com". Здесь нужно указать только протокол и имя узла.
1. Запустите программу.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", "4d4705adaf4a4656b1118b68d671d5b6");
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            }; //Replace 'westus' with the correct region for your Text Analytics subscription

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result = client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine("Document ID: {0} , Language: {1}", document.Id, document.DetectedLanguages[0].Name);
            }

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing keyphrases
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }

            // Extracting sentiment
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }


            // Identify entities
            Console.WriteLine("\n\n===== ENTITIES ======");

            EntitiesBatchResult result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%.")
                        })).Result;

            // Printing entities results
            foreach (var document in result4.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Entities:");

                foreach (EntityRecord entity in document.Entities)
                {
                    Console.WriteLine("\t\t" + entity.Name);
                }
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Text Analytics with Power BI](../tutorials/tutorial-power-bi-key-phrases.md) (Анализ текста с использованием Power BI)

## <a name="see-also"></a>См. также 

 [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)  
 [Часто задаваемые вопросы](../text-analytics-resource-faq.md)

