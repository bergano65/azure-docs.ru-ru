---
title: Краткое руководство. Клиентская библиотека Анализа текста для Node.js | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Информация и примеры кода, которые помогут вам приступить к работе с API анализа текста.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: shthowse
ms.openlocfilehash: c3cd64d0a683a60132808bca6a7ceb4aa84db4f1
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195195"
---
# <a name="quickstart-text-analytics-client-library-for-nodejs"></a>Краткое руководство. Клиентская библиотека Анализа текста для .NET
<a name="HOLTop"></a>

Начало работы с клиентской библиотекой Анализа текста для Node.js. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. 

Используйте клиентскую библиотеку Анализа текста для Node.js, чтобы выполнить следующие действия:

* Анализ мнений
* Определение языка
* Распознавание сущностей
* Извлечение ключевой фразы

[Справочная документация](https://docs.microsoft.com/javascript/api/overview/azure/cognitiveservices/textanalytics?view=azure-node-latest) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [Пакет (NPM)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) | [Примеры](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [Node.js](https://nodejs.org/).

## <a name="setting-up"></a>Настройка

### <a name="create-a-text-analytics-azure-resource"></a>Создание ресурса для Анализа текста в Azure

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс для Анализа текста с помощью [портала Azure](../../cognitive-services-apis-create-account.md) или [Azure CLI](../../cognitive-services-apis-create-account-cli.md) на локальном компьютере. Также можно:

* получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services/#decision) на 7 дней. (после регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/));  
* Просмотреть этот ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки [задайте переменную среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа с именем `TEXTANALYTICS_SUBSCRIPTION_KEY`.

### <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. 

```console
mkdir myapp && cd myapp
```

Выполните команду `npm init`, чтобы создать приложение узла с помощью файла `package.json`. 

```console
npm init
```

Создайте файл с именем `index.js` и импортируйте следующие библиотеки:

```javascript
const CognitiveServicesCredentials = require("@azure/ms-rest-js");
const TextAnalyticsAPIClient = require("@azure/cognitiveservices-textanalytics");
```

Создайте переменные для конечной точки ресурса Azure и ключа подписки. Получите эти значения из переменных среды TEXT_ANALYTICS_SUBSCRIPTION_KEY и TEXT_ANALYTICS_ENDPOINT. Если вы создали переменные среды после начала правки приложения, следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, которые использовались для доступа к этим переменным.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```javascript
const key_var = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('please set/export the following environment variable: ' + key_var);
}
const subscription_key = process.env[key_var];

const endpoint_var = 'TEXT_ANALYTICS_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('please set/export the following environment variable: ' + endpoint_var);
}
const endpoint = process.env[endpoint_var];
```

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

```javascript
const creds = new CognitiveServicesCredentials.ApiKeyCredentials({ inHeader: { 'Ocp-Apim-Subscription-Key': subscription_key } });
const client = new TextAnalyticsAPIClient.TextAnalyticsClient(creds, endpoint);
```

## <a name="sentiment-analysis"></a>Анализ мнений

Создайте список объектов, содержащих документы, которые необходимо проанализировать.

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."}
]}
```

Вызовите `client.sentiment` и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и оценку тональности. Оценка, близкая к 0, указывает на негативную тональность, а оценка, близкая к 1, — на позитивную.

```javascript
const operation = client.sentiment({multiLanguageBatchInput: inputDocuments})
operation
.then(result => {
    console.log(result.documents);
})
.catch(err => {
    throw err;
});
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
[ { id: '1', score: 0.8723785877227783 } ]
```

## <a name="language-detection"></a>Определение языка

Создайте список объектов, содержащих документы.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." }
    ]
    };
```

Вызовите `client.detectLanguage()` и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и первый возвращенный язык.

```javascript
const operation = client.detectLanguage({
    languageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tLanguage: ${language.name}`)
        );
    });
    })
    .catch(err => {
    throw err;
    });
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
```

## <a name="entity-recognition"></a>Распознавание сущностей

Создайте список объектов, содержащих документы.

```javascript
const inputDocuments = {
    documents: [
        { language: "en", id: "1", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800" }
    ]
}
```

Вызовите `client.entities()` и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа. Для каждой обнаруженной сущности распечатайте его имя в Википедии, тип и подтипы (если они есть), а также расположение в исходном тексте.

```javascript
const operation = client.entities({
    multiLanguageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`Document ID: ${document.id}`)
        document.entities.forEach(e =>{
        console.log(`\tName: ${e.name} Type: ${e.type} Sub Type: ${e.type}`)
        e.matches.forEach(match => (
            console.log(`\t\tOffset: ${match.offset} Length: ${match.length} Score: ${match.entityTypeScore}`)
        ))
        })
    });
    })
    .catch(err => {
    throw err;
    });
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
Document ID: 1
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 0 Length: 9 Score: 1
    Name: Bill Gates Type: Person Sub Type: Person
            Offset: 25 Length: 10 Score: 0.999786376953125
    Name: Paul Allen Type: Person Sub Type: Person
            Offset: 40 Length: 10 Score: 0.9988105297088623
    Name: April 4 Type: Other Sub Type: Other
            Offset: 54 Length: 7 Score: 0.8
    Name: April 4, 1975 Type: DateTime Sub Type: DateTime
            Offset: 54 Length: 13 Score: 0.8
    Name: BASIC Type: Other Sub Type: Other
            Offset: 89 Length: 5 Score: 0.8
    Name: Altair 8800 Type: Other Sub Type: Other
            Offset: 116 Length: 11 Score: 0.8
```

## <a name="key-phrase-extraction"></a>Извлечение ключевой фразы

Создайте список объектов, содержащих документы.

```javascript
    let inputLanguage = {
    documents: [
        {language:"en", id:"1", text:"My cat might need to see a veterinarian."}
    ]
    };
```

Вызовите `client.keyPhrases()` и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и все обнаруженные ключевые фразы.

```javascript
    let operation = client.keyPhrases({
    multiLanguageBatchInput: inputLanguage
    });
    operation
    .then(result => {
        console.log(result.documents);
    })
    .catch(err => {
        throw err;
    });
```

Выполните код с помощью `node index.js` в окне консоли.

### <a name="output"></a>Выходные данные

```console
[
    { id: '1', keyPhrases: [ 'cat', 'veterinarian' ] }
]
```

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение, выполнив команду `node` для файла quickstart.

```console
node index.js
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Text Analytics with Power BI](../tutorials/tutorial-power-bi-key-phrases.md) (Анализ текста с использованием Power BI)


* [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)
* [Пример. Как определить тональность с помощью Анализа текста](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Распознавание сущностей](../how-tos/text-analytics-how-to-entity-linking.md)
* [Пример. Как определить язык с помощью Анализа текста](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Распознавание языка](../how-tos/text-analytics-how-to-language-detection.md)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).
