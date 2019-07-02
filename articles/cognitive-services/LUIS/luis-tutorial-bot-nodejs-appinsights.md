---
title: Application Insights, Node.js
titleSuffix: Azure Cognitive Services
description: В руководстве описана процедура добавления сведений, полученных от бота и службы "Распознавание речи", в хранилище данных телеметрии Application Insights.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: 5459fb5d8304a35b3f009354c446514a2831c513
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155291"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Добавление результатов LUIS, полученных бота Node.js, в Application Insights
В руководстве описана процедура добавления сведений, полученных от бота и службы "Распознавание речи", в хранилище данных телеметрии [Application Insights](https://azure.microsoft.com/services/application-insights/). Полученные данные можно запрашивать (используя язык Kusto или Power Bi) для анализа, статистической обработки и создания отчетов по намерениям и сущностям фрагментов речи в реальном времени. Этот анализ помогает определить, следует ли добавлять или изменять намерения и сущности приложения LUIS.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * получать данные от бота и службы распознавания речи в Application Insights;
> * запрашивать данные о распознавании речи из Application Insights.

## <a name="prerequisites"></a>Предварительные требования

* Бот службы Azure Bot с подключением к Application Insights.
* Скачанный код бота из предыдущего **[руководства](luis-nodejs-tutorial-bf-v4.md)** по работе с ботами. 
* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Весь код из этого руководства доступен в репозитории GitHub [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Добавление Application Insights в проект бота веб-приложения
В настоящее время служба Application Insights использует этот бот веб-приложения и собирает общие данные телеметрии состояния для бота. Сведения службы "Распознавание речи" не собираются. 

Для сбора этих данных необходимо установить и настроить пакет NPM **[Application Insights](https://www.npmjs.com/package/applicationinsights)** для бота веб-приложения.  

1. Добавьте следующие пакеты NPM в корневой каталог проекта бота с помощью таких команд для встроенного терминала Visual Studio Code: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    Пакет **underscore** позволяет преобразовать структуру JSON LUIS в плоскую структуру, чтобы упростить использование Application Insights.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Запись и отправка результатов запроса LUIS в Application Insights

1. В Visual Studio Code создайте файл **appInsightsLog.js** и добавьте в него следующий код:

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    Этот файл принимает контекст бота и ответ LUIS, преобразует структуры обоих объектов в плоскую структуру и добавляет их в событие трассировки **Trace** в Application Insights. Событие имеет имя **LUIS**. 

1. В папке **dialogs** откройте файл **luisHelper.js**. Добавьте в него **appInsightsLog.js** как обязательный файл и получите контекста бота и ответ LUIS. Полностью код для этого файла выглядит так: 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

## <a name="add-application-insights-instrumentation-key"></a>Добавление ключа инструментирования Application Insights 

Для добавления данных в Application Insights нужен ключ инструментирования.

1. С помощью браузера на [портале Azure](https://portal.azure.com) найдите ресурс **Application Insights** своего бота. Его имя будет состоять из большей части имени бота и случайных символов в конце, например: `luis-nodejs-bot-johnsmithxqowom`. 
1. Скопируйте **ключ инструментирования** на странице **общих сведений** в ресурсе Application Insights.
1. В Visual Studio Code откройте файл **.env** из корневого каталога проекта бота. Этот файл содержит все переменные вашей среды.  
1. Добавьте новую переменную `MicrosoftApplicationInsightsInstrumentationKey` со значением ключа инструментирования. Не заключайте это значение в кавычки. 

## <a name="start-the-bot"></a>Запуск бота

1. Запустите бота из терминала Visual Studio Code.
    
    ```console
    npm start
    ```

1. Запустите эмулятор бота и откройте бота. Этот [шаг](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) описан в предыдущем руководстве.

1. Задайте боту вопрос. Этот [шаг](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) описан в предыдущем руководстве.

## <a name="view-luis-entries-in-application-insights"></a>Просмотр записей LUIS в Application Insights

Откройте Application Insights, чтобы просмотреть записи LUIS. Через несколько минут данные появятся в Application Insights.

1. На [портале Azure](https://portal.azure.com) откройте ресурс Application Insights для бота. 
1. Затем выполните **поиск** всех данных за последние **30 минут** с типом события **Трассировка**. Выберите трассировку с именем **LUIS**. 
1. Сведения, полученные от бота и LUIS, можно просмотреть в разделе **пользовательских свойств**. 

    ![Просмотр пользовательских свойств LUIS, хранимых в Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Запрос Application Insights для получения намерения, оценки и фрагмента речи
Application Insights позволяет выполнять запросы данных с помощью языка [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics), а также экспортировать их в [Power BI](https://powerbi.microsoft.com). 

1. Выберите **Log (Analytics)** . Откроется новое окно с окном запроса вверху и окном таблицы данных внизу. Если вы использовали базы данных, то такая организация будет вам знакома. В запросе отобразятся предыдущие отфильтрованные данные. В столбце **CustomDimensions** будут содержаться данные бота и LUIS.
1. Чтобы получить сведения об основном намерении, оценке и фрагменте речи, добавьте следующий код над последней строкой (строка `|top...`) в окне запроса:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Выполните запрос. Вы увидите новые столбцы для основного намерения, оценки и фрагмента речи. Выполните сортировку по столбцу topIntent.

См. дополнительные сведения о [языке запросов Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) и [экспорте данных в Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Дополнительная информация

Другие данные, которые может потребоваться добавить в данные Application Insights, включают идентификатор приложения, идентификатор версии, время последнего изменения модели, дату последнего обучения, дату последней публикации. Эти значения можно получить из URL-адреса конечной точки (идентификатор приложения и идентификатор версии) или из вызова API разработки, заданного в параметрах бота веб-приложения и полученного от него.  

При использовании одной подписки конечной точки для нескольких приложений LUIS вам также потребуется включить идентификатор подписки и свойство, сообщающее о том, что это общий ключ. 

> [!div class="nextstepaction"]
> [Подробнее о примерах фрагментов речи](luis-how-to-add-example-utterances.md)
