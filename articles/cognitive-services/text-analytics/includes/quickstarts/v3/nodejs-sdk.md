---
title: Краткое руководство. Клиентская библиотека Анализа текста версии 3 для Node.js | Документация Майкрософт
description: Начало работы с клиентской библиотекой Анализа текста версии 3 для Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/13/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 69899f521e73cb5af1af145a0915dbe1a017f307
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281196"
---
<a name="HOLTop"></a>

[Справочная документация](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Пакет (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [Примеры](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)

> [!NOTE]
> * В этом кратком руководстве используется версия `3.0-preview` клиентской библиотеки Анализа текста, которая включает в себя общедоступную предварительную версию улучшенных функций [анализа тональности](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) и [распознавания именованных сущностей (NER)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * В коде, приведенном в этой статье, для простоты используется незащищенное хранилище учетных данных. Чтобы повысить производительность и масштабируемость рабочих сценариев, рекомендуется отправлять строки в пакетах. Например, следует вызывать `SentimentBatchAsync()` вместо `Sentiment()`.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [Node.js](https://nodejs.org/).

## <a name="setting-up"></a>Настройка

### <a name="create-a-text-analytics-azure-resource"></a>Создание ресурса для Анализа текста в Azure

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. 

```console
mkdir myapp && cd myapp
```

Выполните команду `npm init`, чтобы создать приложение узла с помощью файла `package.json`. 

```console
npm init
```
### <a name="install-the-client-library"></a>Установка клиентской библиотеки

Установите пакеты NPM `@azure/cognitiveservices-textanalytics`:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

Файл `package.json` этого приложения будет дополнен зависимостями.

Создайте файл с именем `index.js` и добавьте следующие библиотеки:

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```

Создайте переменные для конечной точки Azure и ключа ресурса.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Объектная модель

Клиент Анализа текста представляет собой объект `TextAnalyticsClient`, который выполняет проверку подлинности в Azure с использованием ключа. Этот клиент предоставляет несколько методов для анализа текста по отдельным строкам или пакетам строк.

Текст отправляется в API в формате списка `documents` объектов `dictionary`, содержащих комбинации атрибутов `id`, `text` и `language` в зависимости от используемого метода. Атрибут `text` содержит текст для анализа в источнике `language`, а `id` может иметь любое значение. 

Объект в ответе — это список, содержащий аналитику по каждому документу. 

## <a name="code-examples"></a>Примеры кода

* [Аутентификация клиента](#client-authentication)
* [Анализ тональности](#sentiment-analysis) (общедоступная предварительная версия)
* [Пример. Как определить язык с помощью Анализа текста](#language-detection)
* [Распознавание именованных сущностей](#named-entity-recognition-public-preview) (общедоступная предварительная версия)
* [Распознавание именованных сущностей — персональные данные](#named-entity-recognition---personal-information-public-preview) (общедоступная предварительная версия)
* [Связывание сущностей](#entity-linking)
* [Пример. Как извлечь ключевые фразы с помощью Анализа текста](#key-phrase-extraction)

## <a name="client-authentication"></a>Аутентификация клиента

Создайте новый объект `TextAnalyticsClient`, используя в качестве параметров ключ и конечную точку.

```javascript
const client = new TextAnalyticsClient(endpoint,  new CognitiveServicesCredential(key));
```

## <a name="sentiment-analysis-public-preview"></a>Анализ тональности (общедоступная предварительная версия)

> [!NOTE]
> В приведенном ниже коде используется функция анализа тональности версии 3, которая находится в общедоступной предварительной версии.

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `analyzeSentiment()` и получите возвращенный объект `SentimentBatchResult`. Выполните итерацию результатов и выведите идентификатор каждого документа, тональность на уровне документа со степенями уверенности. Результат содержит тональность на уровне предложений, а также смещения, длину и показатели уверенности для каждого документа.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ]

    const sentimentResult = await client.analyzeSentiment(sentimentInput);
    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.documentScores.positive.toFixed(2)} \tNegative: ${document.documentScores.negative.toFixed(2)} \tNeutral: ${document.documentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentenceScores.positive.toFixed(2)} \tNegative: ${sentence.sentenceScores.negative.toFixed(2)} \tNeutral: ${sentence.sentenceScores.neutral.toFixed(2)}`);
            console.log(`\t\tLength: ${sentence.length}, Offset: ${sentence.offset}`);
        })
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
                Positive: 0.61  Negative: 0.01  Neutral: 0.39
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Length: 30, Offset: 0
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
                Length: 30, Offset: 31
```

## <a name="language-detection"></a>Определение языка

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `detectLanguages()` и получите возвращенный `DetectLanguageResult`. Затем выполните итерацию результатов и выведите идентификатор каждого документа с соответствующими основным и обнаруженным языком.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ]

    const languageResult = await client.detectLanguages(languageInputArray);

    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tDetected Language ${language.name}`)
        );
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
ID: 0
        Detected Language French
        Primary Language French
```

## <a name="named-entity-recognition"></a>Распознавание именованных сущностей

> [!NOTE]
> В приведенном ниже коде используется функция распознавания именованных сущностей версии 3, которая находится в общедоступной предварительной версии.

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
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
Document ID: 0
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Type: Person    Sub Type: N/A
        Offset: 25, Length: 10  Score: 0.999786376953125
        Name: Paul Allen        Type: Person    Sub Type: N/A
        Offset: 40, Length: 10  Score: 0.9988105297088623
        Name: April 4, 1975     Type: DateTime  Sub Type: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: Altair    Type: Organization      Sub Type: N/A
        Offset: 116, Length: 6  Score: 0.7996330857276917
        Name: 8800      Type: Quantity  Sub Type: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 21, Length: 9   Score: 0.9837456345558167
        Name: 21        Type: Quantity  Sub Type: Number
        Offset: 71, Length: 2   Score: 0.8
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Распознавание именованных сущностей — персональные данные (общедоступная предварительная версия)

> [!NOTE]
> Приведенный ниже код позволяет обнаружить личную информацию с помощью функции распознавания именованных сущностей версии 3, которая находится в общедоступной предварительной версии.

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `recognizePiiEntities()` и получите объект `EntitiesBatchResult`. Выполните итерацию по списку результатов и выведите имя сущности, тип, подтип, смещение, длину и оценку.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ]
    const entityResults = await client.recognizePiiEntities(entityPiiInput);

    result.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
Document ID: 0
        Name: 123-12-1234       Type: U.S. Social Security Number (SSN)         Sub Type: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>Связывание сущностей

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `recognizeLinkedEntities()` и получите объект `RecognizeLinkedEntitiesResult`. Выполните итерацию по списку результатов и выведите имя сущности, идентификатор, источник данных, URL-адрес, совпадения. Каждый объект в массиве `matches` будет содержать смещение, длину и оценку для этого совпадения.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ]
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
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
                Text: Altair 8800
                Offset: 116, Length: 11         Score: 0.650
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.243
                Text: Gates
                Offset: 161, Length: 5  Score: 0.243
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.174
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.196
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.196
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.137
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.052
```

## <a name="key-phrase-extraction"></a>Извлечение ключевой фразы

Создайте массив строк, содержащий документ, который следует проанализировать. Вызовите метод клиента `extractKeyPhrases()` и получите возвращенный объект `ExtractKeyPhrasesResult`. Выполните итерацию результатов и выведите идентификатор каждого документа и все обнаруженные ключевые фразы.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ]

    const result = await client.extractKeyPhrases(keyPhrasesInput)


    result.forEach(document => {
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

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение, выполнив команду `node` для файла quickstart.

```console
node index.js
```
