---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 5c5c2ed6b7806095dada40cc921a773d28421424
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750159"
---
<a name="HOLTop"></a>

[Справочная документация](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [Пакет (NPM)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) | [Примеры](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [Node.js](https://nodejs.org/).

## <a name="setting-up"></a>Настройка

### <a name="create-a-text-analytics-azure-resource"></a>Создание ресурса для Анализа текста в Azure

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. 

```console
mkdir myapp && cd myapp
```

Выполните команду `npm init`, чтобы создать приложение узла с помощью файла `package.json`. 

```console
npm init
```

Создайте файл с именем `index.js` и добавьте следующие библиотеки:

[!code-javascript[Const statements](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=constStatements)]

Создайте переменные для конечной точки ресурса Azure и ключа подписки. Эти значения можно получить из переменных среды `TEXT_ANALYTICS_SUBSCRIPTION_KEY` и `TEXT_ANALYTICS_ENDPOINT`. Если вы создали переменные среды после того как изменили приложение, следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, используемые для доступа к этим переменным.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

[!code-javascript[Key and endpoint vars](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyVars)]

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

Установите пакеты NPM: `@azure/ms-rest-js` и `@azure/cognitiveservices-textanalytics`.

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

Файл `package.json` этого приложения будет дополнен зависимостями.

## <a name="object-model"></a>Объектная модель

Клиент Анализа текста — это объект [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest), который выполняет проверку подлинности в Azure с использованием ключа. Этот клиент предоставляет несколько методов для анализа текста по отдельным строкам или пакетам строк.

Текст отправляется в API в формате списка `documents` объектов `dictionary`, содержащих комбинации атрибутов `id`, `text` и `language` в зависимости от используемого метода. Атрибут `text` содержит текст для анализа в источнике `language`, а `id` может иметь любое значение. 

Объект в ответе — это список, содержащий аналитику по каждому документу. 

## <a name="code-examples"></a>Примеры кода

* [аутентификация клиента](#authenticate-the-client);
* [Анализ тональности](#sentiment-analysis).
* [Пример. Как определить язык с помощью Анализа текста](#language-detection)
* [Распознавание сущностей](#entity-recognition)
* [Пример. Как извлечь ключевые фразы с помощью Анализа текста](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>Аутентификация клиента

Создайте объект [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest), используя `credentials` и `endpoint` в качестве параметров.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]


## <a name="sentiment-analysis"></a>Анализ мнений

Создайте список объектов словарей, содержащих документ, который необходимо проанализировать. Вызовите метод [тональности()](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest#sentiment-object-) клиента и получите возвращенный [SentimentBatchResult](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/sentimentbatchresult?view=azure-node-latest). Выполните итерацию результатов и выведите идентификатор каждого документа и оценку тональности. Оценка, близкая к 0, указывает на негативную тональность, а оценка, близкая к 1, — на позитивную.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

## <a name="language-detection"></a>Определение языка

Создайте список объектов словарей, содержащих документы. Вызовите метод [detectLanguage()](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest#detectlanguage-object-) клиента и получите возвращенный [LanguageBatchResult](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/languagebatchresult?view=azure-node-latest). Затем выполните итерацию результатов и выведите идентификатор каждого документа и язык.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Распознавание сущностей

Создайте список объектов, содержащих документы. Вызовите метод[сущностей()](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest#entities-object-) клиента и получите объект [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/entitiesbatchresult?view=azure-node-latest). Выполните итерацию списка результатов и выведите идентификатор каждого документа. Для каждой обнаруженной сущности распечатайте его имя в Википедии, тип и подтипы (если они есть), а также расположение в исходном тексте.

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

## <a name="key-phrase-extraction"></a>Извлечение ключевой фразы

Создайте список объектов, содержащих документы. Вызовите метод [keyPhrases()](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest#keyphrases-object-) клиента и получите возвращенный объект [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/keyphrasebatchresult?view=azure-node-latest). Выполните итерацию результатов и выведите идентификатор каждого документа и все обнаруженные ключевые фразы.

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

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение, выполнив команду `node` для файла quickstart.

```console
node index.js
```
