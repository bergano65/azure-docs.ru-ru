---
title: Краткое руководство. Клиентская библиотека Анализа текста для C# | Документация Майкрософт
description: Начало работы с клиентской библиотекой Анализа текста для C#.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/27/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 7e074bf898667bb0019d4147ec9abaea2c674d74
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91401061"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)

[Справочная документация по версии 3.1](https://docs.microsoft.com/dotnet/api/azure.ai.textanalytics?view=azure-dotnet-previews&preserve-view=true) | [Исходный код библиотеки версии 3.1](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [Пакет для разработки (NuGet) версии 3.1](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0-beta.1) | [Примеры кода версии 3.1](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-30"></a>[Версия 3.0](#tab/version-3)

[Справочная документация версии 3](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [Исходный код библиотеки версии 3](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics) | [Пакет для разработки (NuGet) версии 3](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [Образцы кода версии 3](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

[Справочная документация версии 2](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics) | [Исходный код библиотеки версии 2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Пакет для разработки (NuGet) версии 2](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [Образцы кода версии 2](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).
* [Интегрированная среда разработки Visual Studio](https://visualstudio.microsoft.com/vs/).
* Получив подписку Azure, перейдите к <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="созданию ресурса Анализа текста"  target="_blank">созданию ресурса Анализа текста<span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку.  После развертывания щелкните **Перейти к ресурсам**.
    * Для подключения приложения к API Анализа текста потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-net-core-application"></a>Создание приложения .NET Core

С помощью интегрированной среды разработки Visual Studio создайте консольное приложение .NET Core. Будет создан проект Hello World на языке C# с одним файлом исходного кода: *program.cs*.

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)

Установите клиентскую библиотеку, щелкнув правой кнопкой мыши решение в **обозревателе решений** и выбрав **Управление пакетами NuGet**. В открывшемся диспетчере пакетов выберите **Просмотр** и выполните поиск по запросу `Azure.AI.TextAnalytics`. Установите флажок **Включить предварительные версии**, выберите версию `5.1.0-beta.1` и нажмите кнопку **Установить**. Вы также можете использовать [консоль диспетчера пакетов](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

# <a name="version-30"></a>[Версия 3.0](#tab/version-3)

Установите клиентскую библиотеку, щелкнув правой кнопкой мыши решение в **обозревателе решений** и выбрав **Управление пакетами NuGet**. В открывшемся диспетчере пакетов выберите **Просмотр** и выполните поиск по запросу `Azure.AI.TextAnalytics`. Выберите версию `5.0.0`, а затем **Установить**. Вы также можете использовать [консоль диспетчера пакетов](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).


> [!TIP]
> Хотите просмотреть готовый файл с кодом для этого краткого руководства? Его можно найти [на сайте GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), где размещены примеры кода для этого краткого руководства. 

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Установите клиентскую библиотеку, щелкнув правой кнопкой мыши решение в **обозревателе решений** и выбрав **Управление пакетами NuGet**. В открывшемся диспетчере пакетов выберите **Просмотр** и выполните поиск по запросу `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Щелкните результат, а затем — **Установить**. Вы также можете использовать [консоль диспетчера пакетов](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Хотите просмотреть готовый файл с кодом для этого краткого руководства? Его можно найти [на сайте GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), где размещены примеры кода для этого краткого руководства. 

---

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)

Откройте файл *program.cs* и добавьте следующие директивы `using`:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

В классе приложения `Program` создайте переменные для ключа и конечной точки вашего ресурса.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Замените метод приложения `Main`. Вызванные методы будут определены здесь позже.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-30"></a>[Версия 3.0](#tab/version-3)

Откройте файл *program.cs* и добавьте следующие директивы `using`:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

В классе приложения `Program` создайте переменные для ключа и конечной точки вашего ресурса.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Замените метод приложения `Main`. Вызванные методы будут определены здесь позже.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Откройте файл *program.cs* и добавьте следующие директивы `using`:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

В классе приложения `Program` создайте переменные для ключа и конечной точки вашего ресурса. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Замените метод приложения `Main`. Вызванные методы будут определены здесь позже.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Объектная модель

Клиент Анализа текста является объектом `TextAnalyticsClient`, который выполняет проверку подлинности в Azure с помощью вашего ключа и предоставляет функции для приема текста в виде отдельных строк или пакета. Текст в API можно отправлять как синхронно, так и асинхронно. Объект в ответе будет содержать аналитическую информацию по каждому отправленному вами документу. 

Если вы используете версию `3.x` этой службы, можно использовать необязательный экземпляр `TextAnalyticsClientOptions` для инициализации клиента с разными параметрами по умолчанию (например, языком по умолчанию или подсказкой для страны или региона). Проверку подлинности можно также выполнить с помощью токена Azure Active Directory. 

## <a name="code-examples"></a>Примеры кода

* [Пример. Как определить тональность с помощью Анализа текста](#sentiment-analysis)
* [Пример. Как определить язык с помощью Анализа текста](#language-detection)
* [Распознавание именованных сущностей](#named-entity-recognition-ner)
* [Связывание сущностей](#entity-linking)
* [Пример. Как извлечь ключевые фразы с помощью Анализа текста](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>Аутентификация клиента

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)

Убедитесь, что основной метод из более ранней версии создает новый объект клиента с конечной точкой и учетными данными.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-30"></a>[Версия 3.0](#tab/version-3)

Убедитесь, что основной метод из более ранней версии создает новый объект клиента с конечной точкой и учетными данными.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Создайте новый `ApiKeyServiceClientCredentials` класс для хранения учетных данных и добавьте их в запросы клиента. В нем создайте переопределитель для `ProcessHttpRequestAsync()`, который добавляет ваш ключ в `Ocp-Apim-Subscription-Key` заголовок.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Создайте метод для создания экземпляра объекта [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient) с конечной точкой и объект `ApiKeyServiceClientCredentials`, содержащий ваш ключ.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Анализ мнений

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)

Создайте функцию с именем `SentimentAnalysisExample()`, которая принимает созданный ранее клиент, и вызовите вложенную в нее функцию `AnalyzeSentiment()`. Возвращаемый объект `Response<DocumentSentiment>` будет содержать метку тональности и оценку всего входного документа, а также анализ тональности для каждого предложения, если выполнение успешное. При появлении ошибки возникает исключение `RequestFailedException`.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Выходные данные

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

### <a name="opinion-mining"></a>Интеллектуальный анализ данных

Создайте функцию с именем `SentimentAnalysisWithOpinionMiningExample()`, которая принимает созданный ранее клиент, и вызовите вложенную в нее функцию `AnalyzeSentimentBatch()` с параметром `AdditionalSentimentAnalyses.OpinionMining`. Возвращаемый объект `AnalyzeSentimentResultCollection` будет содержать коллекцию `AnalyzeSentimentResult`, в которой представлен объект `Response<DocumentSentiment>`. Разница между `SentimentAnalysis()` и `SentimentAnalysisWithOpinionMiningExample()` заключается в том, что последний метод будет содержать `MinedOpinion` в каждом предложении, где показан проанализированный аспект и связанные с ним мнения. При появлении ошибки возникает исключение `RequestFailedException`.

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice.",
        "The rooms were beautiful. The AC was good and quiet.",
        "The breakfast was good, but the toilet was smelly.",
        "Loved this hotel - good breakfast - nice shuttle service - clean rooms.",
        "I had a great unobstructed view of the Microsoft campus.",
        "Nice rooms but bathrooms were old and the toilet was dirty when we arrived.",
        "We changed rooms as the toilet smelled."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        AdditionalSentimentAnalyses = AdditionalSentimentAnalyses.OpinionMining
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (MinedOpinion minedOpinion in sentence.MinedOpinions)
            {
                Console.WriteLine($"\tAspect: {minedOpinion.Aspect.Text}, Value: {minedOpinion.Aspect.Sentiment}");
                foreach (OpinionSentiment opinion in minedOpinion.Opinions)
                {
                    Console.WriteLine($"\t\tRelated Opinion: {opinion.Text}, Value: {opinion.Sentiment}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>Выходные данные

```console
Document sentiment: Positive

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Aspect: food, Value: Negative
                Related Opinion: unacceptable, Value: Negative
        Aspect: service, Value: Negative
                Related Opinion: unacceptable, Value: Negative
        Aspect: concierge, Value: Positive
                Related Opinion: nice, Value: Positive


Document sentiment: Positive

        Text: "The rooms were beautiful."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Aspect: rooms, Value: Positive
                Related Opinion: beautiful, Value: Positive
        Text: "The AC was good and quiet."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Aspect: AC, Value: Positive
                Related Opinion: good, Value: Positive
                Related Opinion: quiet, Value: Positive


Document sentiment: Negative

        Text: "The breakfast was good, but the toilet was smelly."
        Sentence sentiment: Negative
        Positive score: 0.01
        Negative score: 0.99
        Neutral score: 0.00

        Aspect: breakfast, Value: Positive
                Related Opinion: good, Value: Positive
        Aspect: toilet, Value: Negative
                Related Opinion: smelly, Value: Negative


Document sentiment: Positive

        Text: "Loved this hotel - good breakfast - nice shuttle service - clean rooms."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Aspect: hotel, Value: Positive
                Related Opinion: good, Value: Positive
        Aspect: breakfast, Value: Positive
                Related Opinion: nice, Value: Positive
        Aspect: shuttle service, Value: Positive
                Related Opinion: loved, Value: Positive
        Aspect: rooms, Value: Positive
                Related Opinion: good, Value: Positive
                Related Opinion: nice, Value: Positive
                Related Opinion: clean, Value: Positive
                Related Opinion: loved, Value: Positive


Document sentiment: Positive

        Text: "I had a great unobstructed view of the Microsoft campus."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Aspect: view, Value: Positive
                Related Opinion: great, Value: Positive
                Related Opinion: unobstructed, Value: Positive


Document sentiment: Negative

        Text: "Nice rooms but bathrooms were old and the toilet was dirty when we arrived."
        Sentence sentiment: Negative
        Positive score: 0.00
        Negative score: 1.00
        Neutral score: 0.00

        Aspect: rooms, Value: Positive
                Related Opinion: nice, Value: Positive
        Aspect: bathrooms, Value: Negative
                Related Opinion: old, Value: Negative
        Aspect: toilet, Value: Negative
                Related Opinion: dirty, Value: Negative


Document sentiment: Neutral

        Text: "We changed rooms as the toilet smelled."
        Sentence sentiment: Neutral
        Positive score: 0.01
        Negative score: 0.03
        Neutral score: 0.96

        Aspect: rooms, Value: Negative
                Related Opinion: smelled, Value: Negative
        Aspect: toilet, Value: Negative
                Related Opinion: smelled, Value: Negative
```

# <a name="version-30"></a>[Версия 3.0](#tab/version-3)

Создайте функцию с именем `SentimentAnalysisExample()`, которая принимает созданный ранее клиент, и вызовите вложенную в нее функцию `AnalyzeSentiment()`. Возвращаемый объект `Response<DocumentSentiment>` будет содержать метку тональности и оценку всего входного документа, а также анализ тональности для каждого предложения, если выполнение успешное. При появлении ошибки возникает исключение `RequestFailedException`.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Выходные данные

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Создайте новую функцию с именем `SentimentAnalysisExample()`, которая принимает созданного ранее клиента и вызывает его функцию[Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment) (Тональность). Возвращаемый объект[SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult) будет содержать тональность `Score`, если выполнение успешное и значение `errorMessage`, если нет. 

Оценка, близкая к 0, указывает на негативную тональность, а близкая к 1 — на позитивную.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Определение языка

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)


Создайте функцию с именем `LanguageDetectionExample()`, которая принимает созданный ранее клиент, и вызовите вложенную в нее функцию `DetectLanguage()`. Возвращенный объект `Response<DetectedLanguage>` будет содержать обнаруженный язык вместе с его именем и кодом ISO-6391. При появлении ошибки возникает исключение `RequestFailedException`.

> [!Tip]
> В некоторых случаях неоднозначность языков на основе входных данных может быть трудно устранить. Используйте параметр `countryHint`, чтобы указать двухбуквенный код страны или региона. По умолчанию API использует US в качестве значения countryHint. Чтобы отменить это поведение, вы можете сбросить этот параметр, установив для этого значения пустую строку `countryHint = ""`. Чтобы установить другое значение по умолчанию, задайте свойство `TextAnalyticsClientOptions.DefaultCountryHint` и передайте его во время инициализации клиента.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Выходные данные

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-30"></a>[Версия 3.0](#tab/version-3)


Создайте функцию с именем `LanguageDetectionExample()`, которая принимает созданный ранее клиент, и вызовите вложенную в нее функцию `DetectLanguage()`. Возвращенный объект `Response<DetectedLanguage>` будет содержать обнаруженный язык вместе с его именем и кодом ISO-6391. При появлении ошибки возникает исключение `RequestFailedException`.

> [!Tip]
> В некоторых случаях неоднозначность языков на основе входных данных может быть трудно устранить. Используйте параметр `countryHint`, чтобы указать двухбуквенный код страны или региона. По умолчанию API использует US в качестве значения countryHint. Чтобы отменить это поведение, вы можете сбросить этот параметр, установив для этого значения пустую строку `countryHint = ""`. Чтобы установить другое значение по умолчанию, задайте свойство `TextAnalyticsClientOptions.DefaultCountryHint` и передайте его во время инициализации клиента.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Выходные данные

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Создайте новую функцию с именем `languageDetectionExample()`, которая принимает созданного ранее клиента и вызывает функцию[DetectLanguage(](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Возвращенный объект[LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult) будет содержать список обнаруженных языков `DetectedLanguages` если выполнение успешное и значение `errorMessage`, если нет. Выведите первый возвращенный язык.

> [!Tip]
> В некоторых случаях неоднозначность языков на основе входных данных может быть трудно устранить. Используйте параметр `countryHint`, чтобы указать двухбуквенный код страны или региона. По умолчанию API использует US в качестве значения countryHint по умолчанию.Чтобы отменить это поведение, вы можете сбросить этот параметр, установив для этого значения пустую строку `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Выходные данные

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Распознавание именованных сущностей (NER)

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)


Создайте функцию с именем `EntityRecognitionExample()`, которая принимает созданный ранее клиент, вызовите вложенную в нее функцию `RecognizeEntities()` и просмотрите все результаты. Возвращенный объект `Response<CategorizedEntityCollection>` будет содержать коллекцию обнаруженных сущностей `CategorizedEntity`. При появлении ошибки возникает исключение `RequestFailedException`.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>Выходные данные

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

### <a name="entity-linking"></a>Связывание сущностей

Создайте функцию с именем `EntityLinkingExample()`, которая принимает созданный ранее клиент, вызовите вложенную в нее функцию `RecognizeLinkedEntities()` и просмотрите все результаты. Возвращенный объект `Response<LinkedEntityCollection>` будет содержать коллекцию обнаруженных сущностей `LinkedEntity`. При появлении ошибки возникает исключение `RequestFailedException`. Так как связанные сущности уникально идентифицируются, вхождения одной и той же сущности группируются в объекте `LinkedEntity` как список объектов `LinkedEntityMatch`.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>Выходные данные

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

### <a name="personally-identifiable-information-recognition"></a>Распознавание личных сведений

Создайте функцию с именем `RecognizePIIExample()`, которая принимает созданный ранее клиент, вызовите вложенную в нее функцию `RecognizePiiEntities()` и просмотрите все результаты. Возвращаемый объект `PiiEntityCollection` представляет список обнаруженных сущностей личных сведений. При появлении ошибки возникает исключение `RequestFailedException`.

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>Выходные данные

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

# <a name="version-30"></a>[Версия 3.0](#tab/version-3)


> [!NOTE]
> Новое в версии `3.0`.
> * Связывание сущностей теперь отделено от их распознавания.


Создайте функцию с именем `EntityRecognitionExample()`, которая принимает созданный ранее клиент, вызовите вложенную в нее функцию `RecognizeEntities()` и просмотрите все результаты. Возвращенный объект `Response<IReadOnlyCollection<CategorizedEntity>>` будет содержать список обнаруженных сущностей. При появлении ошибки возникает исключение `RequestFailedException`.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Выходные данные

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

### <a name="entity-linking"></a>Связывание сущностей

Создайте функцию с именем `EntityLinkingExample()`, которая принимает созданный ранее клиент, вызовите вложенную в нее функцию `RecognizeLinkedEntities()` и просмотрите все результаты. Возвращаемый `Response<IReadOnlyCollection<LinkedEntity>>` представляет список обнаруженных сущностей. При появлении ошибки возникает исключение `RequestFailedException`. Так как связанные сущности уникально идентифицируются, вхождения одной и той же сущности группируются в объекте `LinkedEntity` как список объектов `LinkedEntityMatch`.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Выходные данные

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

> [!NOTE]
> В версии 2.1 в ответе NER включено связывание сущностей.

Создайте новую функцию с именем `RecognizeEntitiesExample()`, которая принимает созданного ранее клиента и вызывает его функцию[Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Выполните итерацию результатов. Возвращенный объект[EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult) будет содержать список обнаруженных сущностей `Entities` если выполнение успешное и значение `errorMessage`, если нет. Для каждой обнаруженной сущности выведите ее тип, подтип, имя в Википедии (если они есть), а также расположение в исходном тексте.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


### <a name="key-phrase-extraction"></a>Извлечение ключевой фразы

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)

Создайте функцию с именем `KeyPhraseExtractionExample()`, которая принимает созданный ранее клиент, и вызовите вложенную в нее функцию `ExtractKeyPhrases()`. Возвращенный объект `<Response<KeyPhraseCollection>` будет содержать список обнаруженных ключевых фраз. При появлении ошибки возникает исключение `RequestFailedException`.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Выходные данные

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-30"></a>[Версия 3.0](#tab/version-3)

Создайте функцию с именем `KeyPhraseExtractionExample()`, которая принимает созданный ранее клиент, и вызовите вложенную в нее функцию `ExtractKeyPhrases()`. Возвращенный объект `<Response<IReadOnlyCollection<string>>` будет содержать список обнаруженных ключевых фраз. При появлении ошибки возникает исключение `RequestFailedException`.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Выходные данные

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Создайте новую функцию с именем `KeyPhraseExtractionExample()`, которая принимает созданного ранее клиента и вызывает его функцию[KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). При успешном выполнении результат будет содержать список обнаруженных ключевых фраз в `KeyPhrases`. В противном случае вернется `errorMessage`. Затем выведите все обнаруженные ключевые фразы.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Выходные данные

```console
Key phrases:
    cat
    veterinarian
```

---