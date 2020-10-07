---
title: Краткое руководство. Клиентская библиотека Анализа текста версии 3 для Node.js | Документация Майкрософт
description: Начало работы с клиентской библиотекой Анализа текста версии 3 для Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/27/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: 5d9c3c8e795b6651595a17d540aafa849ce81471
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400965"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)

[Справочная документация по версии 3](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [Исходный код библиотеки версии 3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Пакет (NPM) для версии 3](https://www.npmjs.com/package/@azure/ai-text-analytics) | [Примеры для версии 3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[Версия 3.0](#tab/version-3)

[Справочная документация по версии 3](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [Исходный код библиотеки версии 3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Пакет (NPM) для версии 3](https://www.npmjs.com/package/@azure/ai-text-analytics) | [Примеры для версии 3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

[Справочная документация по версии 2](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [Исходный код библиотеки версии 2](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [Пакет (NPM) для версии 2](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [Примеры для версии 2](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).
* Текущая версия [Node.js](https://nodejs.org/).
* Получив подписку Azure, перейдите к <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="созданию ресурса Анализа текста"  target="_blank">созданию ресурса Анализа текста<span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.
    * Для подключения приложения к API Анализа текста потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. 

```console
mkdir myapp 

cd myapp
```

Выполните команду `npm init`, чтобы создать приложение узла с помощью файла `package.json`. 

```console
npm init
```
### <a name="install-the-client-library"></a>Установка клиентской библиотеки

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)

Установите пакеты NPM `@azure/ai-text-analytics`:

```console
npm install --save @azure/ai-text-analytics@5.1.0-beta.1
```

> [!TIP]
> Хотите просмотреть готовый файл с кодом для этого краткого руководства? Его можно найти [на сайте GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), где размещены примеры кода для этого краткого руководства. 


# <a name="version-30"></a>[Версия 3.0](#tab/version-3)

Установите пакеты NPM `@azure/ai-text-analytics`:

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> Хотите просмотреть готовый файл с кодом для этого краткого руководства? Его можно найти [на сайте GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), где размещены примеры кода для этого краткого руководства. 

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Установите пакеты NPM `@azure/cognitiveservices-textanalytics`:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

> [!TIP]
> Хотите просмотреть готовый файл с кодом для этого краткого руководства? Его можно найти [на сайте GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js), где размещены примеры кода для этого краткого руководства. 

---

Файл `package.json` этого приложения будет дополнен зависимостями.
Создайте файл с именем `index.js` и добавьте следующее:

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[Версия 3.0](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

Создайте переменные для конечной точки Azure и ключа ресурса.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Объектная модель

Клиент Анализа текста представляет собой объект `TextAnalyticsClient`, который выполняет проверку подлинности в Azure с использованием ключа. Этот клиент предоставляет несколько методов для анализа текста по отдельным строкам или пакетам строк.

Текст отправляется в API в формате списка `documents` объектов `dictionary`, содержащих комбинации атрибутов `id`, `text` и `language` в зависимости от используемого метода. Атрибут `text` содержит текст для анализа в источнике `language`, а `id` может иметь любое значение. 

Объект в ответе — это список, содержащий аналитику по каждому документу. 

## <a name="code-examples"></a>Примеры кода

* [Аутентификация клиента](#client-authentication)
* [Анализ тональности](#sentiment-analysis). 
* [Пример. Как определить язык с помощью Анализа текста](#language-detection)
* [Распознавание именованных сущностей](#named-entity-recognition-ner)
* [Связывание сущностей](#entity-linking)
* Персональные данные
* [Пример. Как извлечь ключевые фразы с помощью Анализа текста](#key-phrase-extraction)

## <a name="client-authentication"></a>Аутентификация клиента

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)

Создайте новый объект `TextAnalyticsClient`, используя в качестве параметров ключ и конечную точку.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[Версия 3.0](#tab/version-3)

Создайте новый объект `TextAnalyticsClient`, используя в качестве параметров ключ и конечную точку.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Создайте объект [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient), используя `credentials` и `endpoint` в качестве параметров.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Анализ мнений

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `analyzeSentiment()` и получите возвращенный объект `SentimentBatchResult`. Выполните итерацию результатов и выведите идентификатор каждого документа, тональность на уровне документа со степенями уверенности. Результат содержит тональность на уровне предложений, а также смещения, длину и показатели уверенности для каждого документа.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

### <a name="opinion-mining"></a>Интеллектуальный анализ данных

Чтобы выполнить анализ тональности с интеллектуальным анализом мнений, создайте массив строк, содержащий документ, который необходимо проанализировать. Вызовите метод клиента `analyzeSentiment()`, добавив флаг параметра `includeOpinionMining: true`, и получите возвращенный объект `SentimentBatchResult`. Выполните итерацию результатов и выведите идентификатор каждого документа, тональность на уровне документа со степенями уверенности. Для каждого документа в результате указана тональность уровня предложения, как показано выше, а также тональность на уровне мнения и аспекта.

```javascript
async function sentimentAnalysisWithOpinionMining(client){

    const sentimentInput = [
        {
            text: "The food and service were unacceptable, but the concierge were nice",
            id: "0",
            language: "en"
        }
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
            console.log("    Mined opinions");
            for (const { aspect, opinions } of sentence.minedOpinions) {
                console.log(`      - Aspect text: ${aspect.text}`);
                console.log(`        Aspect sentiment: ${aspect.sentiment}`);
                console.log("        Aspect confidence scores:", aspect.confidenceScores);
                console.log("        Aspect opinions");
                for (const { text, sentiment } of opinions) {
                console.log(`        - Text: ${text}`);
                console.log(`          Sentiment: ${sentiment}`);
                }
            }
        });
    });
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
ID: 0
        // Document Sentiment: positive
        // Document Scores:
                // Positive: 0.84  Negative: 0.16  Neutral: 0.00
        // Sentences Sentiment(1):
                // Sentence sentiment: positive
                // Sentences Scores:
                // Positive: 0.84  Negative: 0.16  Neutral: 0.00
    // Mined opinions
      // - Aspect text: food
        // Aspect sentiment: negative
        // Aspect confidence scores: { positive: 0.01, negative: 0.99 }
        // Aspect opinions
        // - Text: unacceptable
          // Sentiment: negative
      // - Aspect text: service
        // Aspect sentiment: negative
        // Aspect confidence scores: { positive: 0.01, negative: 0.99 }
        // Aspect opinions
        // - Text: unacceptable
          // Sentiment: negative
      // - Aspect text: concierge
        // Aspect sentiment: positive
        // Aspect confidence scores: { positive: 1, negative: 0 }
        // Aspect opinions
        // - Text: nice
          // Sentiment: positive
```

# <a name="version-30"></a>[Версия 3.0](#tab/version-3)

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `analyzeSentiment()` и получите возвращенный объект `SentimentBatchResult`. Выполните итерацию результатов и выведите идентификатор каждого документа, тональность на уровне документа со степенями уверенности. Результат содержит тональность на уровне предложений, а также смещения, длину и показатели уверенности для каждого документа.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Создайте список объектов словарей, содержащих документ, который необходимо проанализировать. Вызовите метод [тональности()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) клиента и получите возвращенный [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Выполните итерацию результатов и выведите идентификатор каждого документа и оценку тональности. Оценка, близкая к 0, указывает на негативную тональность, а оценка, близкая к 1, — на позитивную.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Определение языка

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `detectLanguage()` и получите возвращенный `DetectLanguageResultCollection`. Затем выполните итерацию результатов и выведите идентификатор каждого документа с указанием соответствующего основного языка.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[Версия 3.0](#tab/version-3)

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `detectLanguage()` и получите возвращенный `DetectLanguageResultCollection`. Затем выполните итерацию результатов и выведите идентификатор каждого документа с указанием соответствующего основного языка.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
ID: 0
        Primary Language French
```

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Создайте список объектов словарей, содержащих документы. Вызовите метод [detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) клиента и получите возвращенный [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Затем выполните итерацию результатов и выведите идентификатор каждого документа и язык.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Распознавание именованных сущностей (NER)

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)

> [!NOTE]
> В версии `3.1`:
> * Связывание сущностей — это отдельный запрос, отличный от NER.

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `recognizeEntities()` и получите объект `RecognizeEntitiesResult`. Выполните итерацию по списку результатов и выведите имя сущности, тип, подтип, смещение, длину и оценку.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Связывание сущностей

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `recognizeLinkedEntities()` и получите объект `RecognizeLinkedEntitiesResult`. Выполните итерацию по списку результатов и выведите имя сущности, идентификатор, источник данных, URL-адрес, совпадения. Каждый объект в массиве `matches` будет содержать смещение, длину и оценку для этого совпадения.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

### <a name="personally-identifying-information-pii-recognition"></a>Распознавание личных сведений (PII)

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `recognizePiiEntities()` и получите объект `RecognizePIIEntitiesResult`. Выполните итерацию по списку результатов и выведите имя сущности, тип и оценку.

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[Версия 3.0](#tab/version-3)

> [!NOTE]
> В версии `3.0`:
> * Связывание сущностей — это отдельный запрос, отличный от NER.

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `recognizeEntities()` и получите объект `RecognizeEntitiesResult`. Выполните итерацию по списку результатов и выведите имя сущности, тип, подтип, смещение, длину и оценку.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Связывание сущностей

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `recognizeLinkedEntities()` и получите объект `RecognizeLinkedEntitiesResult`. Выполните итерацию по списку результатов и выведите имя сущности, идентификатор, источник данных, URL-адрес, совпадения. Каждый объект в массиве `matches` будет содержать смещение, длину и оценку для этого совпадения.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

> [!NOTE]
> В версии 2.1 в ответе NER включено связывание сущностей.

Создайте список объектов, содержащих документы. Вызовите метод[сущностей()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) клиента и получите объект [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult). Выполните итерацию списка результатов и выведите идентификатор каждого документа. Для каждой обнаруженной сущности распечатайте его имя в Википедии, тип и подтипы (если они есть), а также расположение в исходном тексте.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Извлечение ключевой фразы

# <a name="version-31-preview"></a>[Версия 3.1 (предварительная версия)](#tab/version-3-1)

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `extractKeyPhrases()` и получите возвращенный объект `ExtractKeyPhrasesResult`. Выполните итерацию результатов и выведите идентификатор каждого документа и все обнаруженные ключевые фразы.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[Версия 3.0](#tab/version-3)

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `extractKeyPhrases()` и получите возвращенный объект `ExtractKeyPhrasesResult`. Выполните итерацию результатов и выведите идентификатор каждого документа и все обнаруженные ключевые фразы.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-21"></a>[Версия 2.1](#tab/version-2)

Создайте список объектов, содержащих документы. Вызовите метод [keyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) клиента и получите возвращенный объект [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult). Выполните итерацию результатов и выведите идентификатор каждого документа и все обнаруженные ключевые фразы.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение, выполнив команду `node` для файла quickstart.

```console
node index.js
```