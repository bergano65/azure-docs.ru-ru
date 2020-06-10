---
title: Краткое руководство. Клиентская библиотека Анализа текста для C# | Документация Майкрософт
description: Начало работы с клиентской библиотекой Анализа текста для C#.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 979c714ca5446d3f1eabb76e97535ee5ffa2e359
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140760"
---
<a name="HOLTop"></a>

#### <a name="version-30"></a>[Версия 3.0](#tab/version-3)

[Справочная документация версии 3](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [Исходный код библиотеки версии 3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [Пакет для разработки (NuGet) версии 3](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [Образцы кода версии 3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Версия 2.1](#tab/version-2)

[Справочная документация версии 2](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Исходный код библиотеки версии 2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Пакет для разработки (NuGet) версии 2](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [Образцы кода версии 2](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Интегрированная среда разработки Visual Studio](https://visualstudio.microsoft.com/vs/).
* Получив подписку Azure, перейдите к <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="созданию ресурса Анализа текста"  target="_blank">созданию ресурса Анализа текста<span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку.  После развертывания щелкните **Перейти к ресурсам**.
    * Для подключения приложения к API Анализа текста потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-net-core-application"></a>Создание приложения .NET Core

С помощью интегрированной среды разработки Visual Studio создайте консольное приложение .NET Core. Будет создан проект Hello World на языке C# с одним файлом исходного кода: *program.cs*.

#### <a name="version-30"></a>[Версия 3.0](#tab/version-3)

Установите клиентскую библиотеку, щелкнув правой кнопкой мыши решение в **обозревателе решений** и выбрав **Управление пакетами NuGet**. В открывшемся диспетчере пакетов выберите **Просмотр**, установите флажок **Включить предварительные версии** и выполните поиск по запросу `Azure.AI.TextAnalytics`. Выберите версию `1.0.0-preview.5`, а затем **Установить**. Вы также можете использовать [консоль диспетчера пакетов](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Хотите просмотреть готовый файл с кодом для этого краткого руководства? Его можно найти [на сайте GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), где размещены примеры кода для этого краткого руководства. 

#### <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Установите клиентскую библиотеку, щелкнув правой кнопкой мыши решение в **обозревателе решений** и выбрав **Управление пакетами NuGet**. В открывшемся диспетчере пакетов выберите **Просмотр** и выполните поиск по запросу `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Щелкните результат, а затем — **Установить**. Вы также можете использовать [консоль диспетчера пакетов](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Хотите просмотреть готовый файл с кодом для этого краткого руководства? Его можно найти [на сайте GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), где размещены примеры кода для этого краткого руководства. 

---

#### <a name="version-30"></a>[Версия 3.0](#tab/version-3)

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

#### <a name="version-21"></a>[Версия 2.1](#tab/version-2)

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

Если вы используете версию `3.0` этой службы, можно использовать необязательный экземпляр `TextAnalyticsClientOptions` для инициализации клиента с разными параметрами по умолчанию (например, языком по умолчанию или подсказкой для страны или региона). Проверку подлинности можно также выполнить с помощью токена Azure Active Directory. 

## <a name="code-examples"></a>Примеры кода

* [Пример. Как определить тональность с помощью Анализа текста](#sentiment-analysis)
* [Пример. Как определить язык с помощью Анализа текста](#language-detection)
* [Распознавание именованных сущностей](#named-entity-recognition-ner)
* [Связывание сущностей](#entity-linking)
* [Пример. Как извлечь ключевые фразы с помощью Анализа текста](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Аутентификация клиента

#### <a name="version-30"></a>[Версия 3.0](#tab/version-3)

Убедитесь, что основной метод из более ранней версии создает новый объект клиента с конечной точкой и учетными данными.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Создайте новый `ApiKeyServiceClientCredentials` класс для хранения учетных данных и добавьте их в запросы клиента. В нем создайте переопределитель для `ProcessHttpRequestAsync()`, который добавляет ваш ключ в `Ocp-Apim-Subscription-Key` заголовок.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Создайте метод для создания экземпляра объекта [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) с конечной точкой и объект `ApiKeyServiceClientCredentials`, содержащий ваш ключ.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Анализ мнений

#### <a name="version-30"></a>[Версия 3.0](#tab/version-3)

Создайте функцию с именем `SentimentAnalysisExample()`, которая принимает созданный ранее клиент, и вызовите вложенную в нее функцию `AnalyzeSentiment()`. Возвращаемый объект `Response<DocumentSentiment>` будет содержать метку тональности и оценку всего входного документа, а также анализ тональности для каждого предложения, если выполнение успешное. При появлении ошибки возникает исключение `RequestFailedException`.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
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

#### <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Создайте новую функцию с именем `SentimentAnalysisExample()`, которая принимает созданного ранее клиента и вызывает его функцию[Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) (Тональность). Возвращаемый объект[SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) будет содержать тональность `Score`, если выполнение успешное и значение `errorMessage`, если нет. 

Оценка, близкая к 0, указывает на негативную тональность, а близкая к 1 — на позитивную.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Определение языка

#### <a name="version-30"></a>[Версия 3.0](#tab/version-3)


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

#### <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Создайте новую функцию с именем `languageDetectionExample()`, которая принимает созданного ранее клиента и вызывает функцию[DetectLanguage(](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Возвращенный объект[LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) будет содержать список обнаруженных языков `DetectedLanguages` если выполнение успешное и значение `errorMessage`, если нет. Выведите первый возвращенный язык.

> [!Tip]
> В некоторых случаях неоднозначность языков на основе входных данных может быть трудно устранить. Используйте параметр `countryHint`, чтобы указать двухбуквенный код страны или региона. По умолчанию API использует US в качестве значения countryHint по умолчанию.Чтобы отменить это поведение, вы можете сбросить этот параметр, установив для этого значения пустую строку `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Выходные данные

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Распознавание именованных сущностей (NER)

#### <a name="version-30"></a>[Версия 3.0](#tab/version-3)


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

## <a name="entity-linking"></a>Связывание сущностей

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

#### <a name="version-21"></a>[Версия 2.1](#tab/version-2)

> [!NOTE]
> В версии 2.1 в ответе NER включено связывание сущностей.

Создайте новую функцию с именем `RecognizeEntitiesExample()`, которая принимает созданного ранее клиента и вызывает его функцию[Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Выполните итерацию результатов. Возвращенный объект[EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) будет содержать список обнаруженных сущностей `Entities` если выполнение успешное и значение `errorMessage`, если нет. Для каждой обнаруженной сущности выведите ее тип, подтип, имя в Википедии (если они есть), а также расположение в исходном тексте.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Извлечение ключевой фразы

#### <a name="version-30"></a>[Версия 3.0](#tab/version-3)

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

#### <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Создайте новую функцию с именем `KeyPhraseExtractionExample()`, которая принимает созданного ранее клиента и вызывает его функцию[KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). При успешном выполнении результат будет содержать список обнаруженных ключевых фраз в `KeyPhrases`. В противном случае вернется `errorMessage`. Затем выведите все обнаруженные ключевые фразы.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Выходные данные

```console
Key phrases:
    cat
    veterinarian
```

---
