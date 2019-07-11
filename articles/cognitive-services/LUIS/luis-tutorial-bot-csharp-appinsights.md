---
title: Application Insights, C#
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
ms.openlocfilehash: 720352403fd5f5937669f9838f3974cb0d3f8797
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657775"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-c"></a>Добавление результатов LUIS в Application Insights из бота C#

В руководстве описана процедура добавления сведений, полученных от бота и службы "Распознавание речи", в хранилище данных телеметрии [Application Insights](https://azure.microsoft.com/services/application-insights/). Полученные данные можно запрашивать (используя язык Kusto или Power Bi) для анализа, статистической обработки и создания отчетов по намерениям и сущностям фрагментов речи в реальном времени. Этот анализ помогает определить, следует ли добавлять или изменять намерения и сущности приложения LUIS.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * получать данные от бота и службы распознавания речи в Application Insights;
> * запрашивать данные о распознавании речи из Application Insights.

## <a name="prerequisites"></a>Предварительные требования

* Бот службы Azure Bot с подключением к Application Insights.
* Скачанный код бота из предыдущего **[руководства](luis-csharp-tutorial-bf-v4.md)** по работе с ботами. 
* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Весь код из этого руководства доступен в репозитории GitHub [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Добавление Application Insights в проект бота веб-приложения

В настоящее время служба Application Insights использует этот бот веб-приложения и собирает общие данные телеметрии состояния для бота. Сведения службы "Распознавание речи" не собираются. 

Для сбора этих данных необходимо установить и настроить пакет NuGet **[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** для бота веб-приложения.  

1. В Visual Studio добавьте зависимость в решение. В **обозревателе решений** щелкните правой кнопкой мыши имя проекта и выберите **Управление пакетами NuGet**. Диспетчер пакетов NuGet выведет список установленных пакетов. 
1. Выберите **Обзор** и выполните поиск по запросу **Microsoft.ApplicationInsights**.
1. Установите пакет. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Запись и отправка результатов запроса LUIS в Application Insights

1. Откройте файл `LuisHelper.cs` и замените его содержимое следующим кодом. Метод **LogToApplicationInsights** получает данные от бота и LUIS и отправляет их в Application Insights как событие трассировки с именем `LUIS`.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.ApplicationInsights;
    using System.Collections.Generic;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    LuisHelper.LogToApplicationInsights(configuration, turnContext, recognizerResult);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
            public static void LogToApplicationInsights(IConfiguration configuration, ITurnContext turnContext, RecognizerResult result)
            {
                // Create Application Insights object
                TelemetryClient telemetry = new TelemetryClient();
    
                // Set Application Insights Instrumentation Key from App Settings
                telemetry.InstrumentationKey = configuration["BotDevAppInsightsKey"];
    
                // Collect information to send to Application Insights
                Dictionary<string, string> logProperties = new Dictionary<string, string>();
    
                logProperties.Add("BotConversation", turnContext.Activity.Conversation.Name);
                logProperties.Add("Bot_userId", turnContext.Activity.Conversation.Id);
    
                logProperties.Add("LUIS_query", result.Text);
                logProperties.Add("LUIS_topScoringIntent_Name", result.GetTopScoringIntent().intent);
                logProperties.Add("LUIS_topScoringIntentScore", result.GetTopScoringIntent().score.ToString());
    
    
                // Add entities to collected information
                int i = 1;
                if (result.Entities.Count > 0)
                {
                    foreach (var item in result.Entities)
                    {
                        logProperties.Add("LUIS_entities_" + i++ + "_" + item.Key, item.Value.ToString());
                    }
                }
    
                // Send to Application Insights
                telemetry.TrackTrace("LUIS", ApplicationInsights.DataContracts.SeverityLevel.Information, logProperties);
    
            }
        }
    }
    ```

## <a name="add-application-insights-instrumentation-key"></a>Добавление ключа инструментирования Application Insights 

Для добавления данных в Application Insights нужен ключ инструментирования.

1. С помощью браузера на [портале Azure](https://portal.azure.com) найдите ресурс **Application Insights** своего бота. Его имя будет состоять из большей части имени бота и случайных символов в конце, например: `luis-csharp-bot-johnsmithxqowom`. 
1. Скопируйте **ключ инструментирования** на странице **общих сведений** в ресурсе Application Insights.
1. В Visual Studio откройте файл **appsettings.json** из корневого каталога проекта бота. Этот файл содержит все переменные вашей среды.
1. Добавьте новую переменную `BotDevAppInsightsKey` со значением ключа инструментирования. Значение должно быть заключено в кавычки. 

## <a name="build-and-start-the-bot"></a>Сборка и запуск бота

1. В Visual Studio выполните сборку и запуск бота. 
1. Запустите эмулятор бота и откройте бота. Этот [шаг](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) описан в предыдущем руководстве.

1. Задайте боту вопрос. Этот [шаг](luis-csharp-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) описан в предыдущем руководстве.

## <a name="view-luis-entries-in-application-insights"></a>Просмотр записей LUIS в Application Insights

Откройте Application Insights, чтобы просмотреть записи LUIS. Через несколько минут данные появятся в Application Insights.

1. На [портале Azure](https://portal.azure.com) откройте ресурс Application Insights для бота. 
1. Затем выполните **поиск** всех данных за последние **30 минут** с типом события **Трассировка**. Выберите трассировку с именем **LUIS**. 
1. Сведения, полученные от бота и LUIS, можно просмотреть в разделе **пользовательских свойств**. 

    ![Просмотр пользовательских свойств LUIS, хранимых в Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Запрос Application Insights для получения намерения, оценки и фрагмента речи
Application Insights позволяет выполнять запросы данных с помощью языка [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use), а также экспортировать их в [Power BI](https://powerbi.microsoft.com). 

1. Выберите **Log (Analytics)** . Откроется новое окно с окном запроса вверху и окном таблицы данных внизу. Если вы использовали базы данных, то такая организация будет вам знакома. В запросе отобразятся предыдущие отфильтрованные данные. В столбце **CustomDimensions** будут содержаться данные бота и LUIS.
1. Чтобы получить сведения об основном намерении, оценке и фрагменте речи, добавьте следующий код над последней строкой (строка `|top...`) в окне запроса:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Выполните запрос. Вы увидите новые столбцы для основного намерения, оценки и фрагмента речи. Выполните сортировку по столбцу topIntent.

См. дополнительные сведения о [языке запросов Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) и [экспорте данных в Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Дополнительные сведения о платформе Bot Framework

Дополнительные сведения о [платформе Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Дополнительная информация

Другие данные, которые может потребоваться добавить в данные Application Insights, включают идентификатор приложения, идентификатор версии, время последнего изменения модели, дату последнего обучения, дату последней публикации. Эти значения можно получить из URL-адреса конечной точки (идентификатор приложения и идентификатор версии) или из вызова API разработки, заданного в параметрах бота веб-приложения и полученного от него.  

При использовании одной подписки конечной точки для нескольких приложений LUIS вам также потребуется включить идентификатор подписки и свойство, сообщающее о том, что это общий ключ.

> [!div class="nextstepaction"]
> [Подробнее о примерах фрагментов речи](luis-how-to-add-example-utterances.md)
