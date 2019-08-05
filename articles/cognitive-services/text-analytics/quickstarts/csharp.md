---
title: Краткое руководство. Вызов службы "Анализ текста" с помощью пакета SDK Azure для .NET и C#
titleSuffix: Azure Cognitive Services
description: Информация и примеры кода, которые помогут быстро приступить к работе со службой "Анализ текста" и C#.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: assafi
ms.openlocfilehash: 6bd3907392dad626c1eeb1823c929f1a35d544dd
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697672"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Краткое руководство. Вызов службы "Анализ текста" с помощью пакета SDK для .NET и C#
<a name="HOLTop"></a>

Это краткое руководство поможет научиться анализировать язык с помощью пакета Azure SDK для .NET и языка C#. Так как REST API [Анализ текста](//go.microsoft.com/fwlink/?LinkID=759711) совместим с большинством языков программирования, пакет SDK обеспечивает простой способ интеграции службы в ваши приложения.

> [!NOTE]
> В демонстрационном коде этой статьи для простоты используются синхронные методы пакета SDK .NET для Анализа текста. Но в приложениях для рабочей среды мы рекомендуем использовать пакетные асинхронные методы, чтобы обеспечить масштабируемость и высокую скорость отклика. Например, можно использовать `SentimentBatchAsync` вместо `Sentiment`.
>
> Пакетную асинхронную версию этого примера можно найти на сайте [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Технические подробности см. в [справочнике по пакету SDK службы "Анализ текста"](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet) для .NET.

## <a name="prerequisites"></a>Предварительные требования

* Любой выпуск Visual Studio 2017 или более поздней версии.
* Пакет SDK Анализа текста [для .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics).

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Создание решения Visual Studio и установка пакета SDK

1. Создайте проект "Консольное приложение (.NET Core)". [Запустите Visual Studio](https://visualstudio.microsoft.com/vs/).
1. Щелкните решение правой кнопкой мыши и выберите **Управление пакетами NuGet для решения**.
1. Выберите вкладку **обзора**. Найдите **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**.

## <a name="authenticate-your-credentials"></a>Аутентификация учетных данных

1. Добавьте следующие операторы `using` в файл основного класса (Program.cs по умолчанию).

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. Создайте класс `ApiKeyServiceClientCredentials` для хранения учетных данных и добавьте их для каждого запроса.

    ```csharp
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string apiKey;

        public ApiKeyServiceClientCredentials(string apiKey)
        {
            this.apiKey = apiKey;
        }

        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }
            request.Headers.Add("Ocp-Apim-Subscription-Key", this.apiKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. Обновите класс `Program`. Добавьте один элемент-константу для ключа API службы "Анализ текста", а другой — для конечной точки службы. Не забудьте указать правильное расположение Azure для своего ресурса Анализа текста.

    ```csharp
    //Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    //You can get the resource location from Azure Portal -> your TA resource -> Overview
    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://<your-location>.api.cognitive.microsoft.com";
    ```

> [!Tip]
> Для повышения безопасности секретов в рабочих системах мы советуем использовать [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Создание клиента Анализа текста

В функции проекта `Main` вызовите пример необходимого метода. Передайте заданные параметры `Endpoint` и `ApiKey`.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(ApiKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client);
        // DetectLanguageExample(client);
        // RecognizeEntitiesExample(client);
        // KeyPhraseExtractionExample(client);
        Console.ReadLine();
    }
```

В приведенных ниже разделах описано, как вызвать каждую функцию службы.

## <a name="perform-sentiment-analysis"></a>Анализ тональности

1. Создайте функцию `SentimentAnalysisExample()`, которая принимает созданный ранее клиент.
2. В той же функции вызовите `client.Sentiment()` и получите результат. При успешном выполнении результат будет содержать тональность `Score`. В противном случае вернется `errorMessage`. Оценка, близкая к 0, указывает на негативную тональность, а близкая к 1 — на позитивную.

    ```csharp
    var result = client.Sentiment("I had the best day of my life.", "en");

    // Printing sentiment results
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
    ```

### <a name="output"></a>Выходные данные

```console
Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>Распознавание языка

1. Создайте функцию `DetectLanguageExample()`, которая принимает созданный ранее клиент.
2. В той же функции вызовите `client.DetectLanguage()` и получите результат. При успешном выполнении результат будет содержать список обнаруженных языков в `DetectedLanguages`. В противном случае вернется `errorMessage`. Затем выведите первый возвращенный язык.

    ```csharp
    var result = client.DetectLanguage("This is a document written in English.");

    // Printing detected languages
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
    ```

> [!Tip]
> В некоторых случаях неоднозначность языков на основе входных данных может быть трудно устранить. Используйте параметр `countryHint`, чтобы указать двухбуквенный код страны. По умолчанию API использует US в качестве значения countryHint по умолчанию.Чтобы отменить это поведение, вы можете сбросить этот параметр, установив для этого значения пустую строку `countryHint = ""`.

### <a name="output"></a>Выходные данные

```console
Language: English
```

## <a name="perform-entity-recognition"></a>Распознавание сущностей

1. Создайте функцию `RecognizeEntitiesExample()`, которая принимает созданный ранее клиент.
2. В той же функции вызовите `client.Entities()` и получите результат. Затем выполните итерацию результатов. При успешном выполнении результат будет содержать список обнаруженных сущностей в `Entities`. В противном случае вернется `errorMessage`. Для каждой обнаруженной сущности выведите ее тип, подтип, имя в Википедии (если они есть), а также расположение в исходном тексте.

    ```csharp
    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");

    // Printing recognized entities
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
    ```

### <a name="output"></a>Выходные данные

```console
Entities:
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0,      Length: 9,      Score: 1.000
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25,     Length: 10,     Score: 1.000
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40,     Length: 10,     Score: 0.999
    Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54,     Length: 7,      Score: 0.800
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54,     Length: 13,     Score: 0.800
    Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89,     Length: 5,      Score: 0.800
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116,    Length: 11,     Score: 0.800
```

## <a name="perform-key-phrase-extraction"></a>Извлечение ключевых фраз

1. Создайте функцию `KeyPhraseExtractionExample()`, которая принимает созданный ранее клиент.
2. В той же функции вызовите `client.KeyPhrases()` и получите результат. При успешном выполнении результат будет содержать список обнаруженных ключевых фраз в `KeyPhrases`. В противном случае вернется `errorMessage`. Затем выведите все обнаруженные ключевые фразы.

    ```csharp
    var result = client.KeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in result.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
    ```

### <a name="output"></a>Выходные данные

```console
Key phrases:
    cat
    veterinarian
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Text Analytics with Power BI](../tutorials/tutorial-power-bi-key-phrases.md) (Анализ текста с использованием Power BI)


* [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)
* [Часто задаваемые вопросы](../text-analytics-resource-faq.md)
