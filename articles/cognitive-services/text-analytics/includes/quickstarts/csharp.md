---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: ea526648b1b37919eb41953937d3afa72f7f39e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446263"
---
<a name="HOLTop"></a>

[Справочная документация](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Пакет (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [Примеры](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

> [!NOTE]
> В коде этой статьи для простоты используются синхронные методы пакета SDK .NET для Анализа текста. Для рабочих сценариев рекомендуется использовать пакетные асинхронные методы для повышения производительности и масштабируемости. Например, вызовите [SentimentBatchAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentimentbatchasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview) вместо [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet).

## <a name="prerequisites"></a>предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Интегрированная среда разработки Visual Studio](https://visualstudio.microsoft.com/vs/).

## <a name="setting-up"></a>Настройка

### <a name="create-a-text-analytics-azure-resource"></a>Создание ресурса для Анализа текста в Azure

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-net-core-application"></a>Создание приложения .NET Core

С помощью интегрированной среды разработки Visual Studio создайте консольное приложение .NET Core. Будет создан простой проект Hello World на языке C# с одним файлом исходного кода: *program.cs*.

Установите клиентскую библиотеку, щелкнув правой кнопкой мыши решение в **обозревателе решений** и выбрав **Управление пакетами NuGet**. В открывшемся диспетчере пакетов выберите **Просмотр** и выполните поиск по запросу `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Щелкните результат, а затем — **Установить**. Вы также можете использовать [консоль диспетчера пакетов](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

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

## <a name="object-model"></a>Объектная модель

Клиент Аналитики текста является объектом [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet), который выполняет проверку подлинности в Azure с помощью вашего ключа и предоставляет функции для приема текста в виде отдельных строк или пакета. Текст в API можно отправлять как синхронно, так и асинхронно. Объект в ответе будет содержать аналитическую информацию по каждому отправленному вами документу. 

## <a name="code-examples"></a>Примеры кода

* [аутентификация клиента](#authenticate-the-client);
* [Анализ тональности](#sentiment-analysis).
* [Пример. Как определить язык с помощью Анализа текста](#language-detection)
* [Распознавание сущностей](#entity-recognition)
* [Пример. Как извлечь ключевые фразы с помощью Анализа текста](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Аутентификация клиента

Создайте новый `ApiKeyServiceClientCredentials` класс для хранения учетных данных и добавьте их в запросы клиента. В нем создайте переопределитель для `ProcessHttpRequestAsync()`, который добавляет ваш ключ в `Ocp-Apim-Subscription-Key` заголовок.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Создайте метод для создания экземпляра объекта [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) с конечной точкой и объект `ApiKeyServiceClientCredentials`, содержащий ваш ключ.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

В методе `main()` программы вызовите метод проверки подлинности для создания экземпляра клиента.

## <a name="sentiment-analysis"></a>Анализ мнений

Создайте новую функцию с именем `SentimentAnalysisExample()`, которая принимает созданного ранее клиента и вызывает его функцию[Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) (Тональность). Возвращаемый объект[SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) будет содержать тональность `Score`, если выполнение успешное и значение `errorMessage`, если нет. 

Оценка, близкая к 0, указывает на негативную тональность, а близкая к 1 — на позитивную.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

### <a name="output"></a>Выходные данные

```console
Sentiment Score: 0.87
```

## <a name="language-detection"></a>Определение языка

Создайте новую функцию с именем `languageDetectionExample()`, которая принимает созданного ранее клиента и вызывает функцию[DetectLanguage(](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Возвращенный объект[LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) будет содержать список обнаруженных языков `DetectedLanguages` если выполнение успешное и значение `errorMessage`, если нет.  Выведите первый возвращенный язык.

> [!Tip]
> В некоторых случаях неоднозначность языков на основе входных данных может быть трудно устранить. Используйте параметр `countryHint`, чтобы указать двухбуквенный код страны. По умолчанию API использует US в качестве значения countryHint по умолчанию.Чтобы отменить это поведение, вы можете сбросить этот параметр, установив для этого значения пустую строку `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Выходные данные

```console
Language: English
```

## <a name="entity-recognition"></a>Распознавание сущностей

Создайте новую функцию с именем `RecognizeEntitiesExample()`, которая принимает созданного ранее клиента и вызывает его функцию[Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Выполните итерацию результатов. Возвращенный объект[EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) будет содержать список обнаруженных сущностей `Entities` если выполнение успешное и значение `errorMessage`, если нет. Для каждой обнаруженной сущности выведите ее тип, подтип, имя в Википедии (если они есть), а также расположение в исходном тексте.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]


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

## <a name="key-phrase-extraction"></a>Извлечение ключевой фразы

Создайте новую функцию с именем `KeyPhraseExtractionExample()`, которая принимает созданного ранее клиента и вызывает его функцию[KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). При успешном выполнении результат будет содержать список обнаруженных ключевых фраз в `KeyPhrases`. В противном случае вернется `errorMessage`. Затем выведите все обнаруженные ключевые фразы.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Выходные данные

```console
Key phrases:
    cat
    veterinarian
```
