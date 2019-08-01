---
title: Бот распознавания речи версии 4 на C#
titleSuffix: Azure Cognitive Services
description: Создавайте чат-боты, интегрированные со службой распознавания речи (LUIS), используя C#. Бот создается с помощью Bot Framework версии 4 и службы бота веб-приложения Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 210724e8a8b9b585a3e308b8e321d809e4e897a1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560651"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Руководство по совместному использованию бота веб-приложения и службы распознавания речи в C#

Использование C# для создания чат-бота для интеграции со службой распознавания речи (LUIS). Бот создается с помощью ресурса [Бот веб-приложения](https://docs.microsoft.com/azure/bot-service/) Azure и 4 [версии Bot Framework](https://github.com/Microsoft/botbuilder-dotnet).

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

> [!div class="checklist"]
> * Создание бота веб-приложения. Этот процесс создает новое приложение LUIS.
> * Скачивание проекта бота, созданного веб-службой ботов
> * Запуск бота и эмулятора на локальном компьютере
> * Просмотр результатов высказывания в боте

## <a name="prerequisites"></a>Предварительные требования

* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Создание ресурса бота веб-приложения

1. На [портале Azure](https://portal.azure.com) выберите **Создание ресурса**.

1. С помощью поля поиска найдите и выберите **Web App Bot** (Бот веб-приложения). Нажмите кнопку **Создать**.

1. В **Службе Bot** введите необходимые сведения.

    |Параметр|Назначение|Предлагаемый параметр|
    |--|--|--|
    |Имя бота|Имя ресурса|`luis-csharp-bot-` + `<your-name>`, например `luis-csharp-bot-johnsmith`|
    |Subscription|Подписка, в которой нужно создать бот.|Основная подписка.
    |Resource group|Логическая группа ресурсов Azure|Создайте группу для хранения всех ресурсов, используемых с этим ботом, и назовите эту группу `luis-csharp-bot-resource-group`.|
    |Location|Регион Azure не обязательно должен совпадать с регионом разработки или публикации LUIS.|`westus`|
    |Ценовая категория|Используется для ограничения запросов службы и выставления счетов.|`F0` — уровень "Бесплатный".
    |Имя приложения.|При развертывании бота в облаке имя используется в качестве поддомена (например, humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, например `luis-csharp-bot-johnsmith`|
    |Шаблон бота|Параметры Bot Framework (см. следующую таблицу)|
    |Расположение приложения LUIS|Должно совпадать с регионом ресурса LUIS|`westus`|
    |расположение или план службы приложений;|Значение по умолчанию не следует изменять.|
    |Application Insights|Значение по умолчанию не следует изменять.|
    |Идентификатор и пароль приложения Майкрософт|Значение по умолчанию не следует изменять.|

1. Выберите следующие компоненты в **шаблоне бота**, а затем нажмите кнопку **Выбрать** под этими параметрами:

    |Параметр|Назначение|Выбор|
    |--|--|--|
    |Версия пакета SDK|Версия Bot Framework|**Пакет SDK версии 4**|
    |Язык пакета SDK|Язык программирования бота|**C#**|
    |Бот|Тип бота|**Базовый бот**|
    
1. Нажмите кнопку **Создать**. В Azure будет создана и развернута служба ботов. Элементом этого процесса является создание приложения LUIS с именем `luis-csharp-bot-XXXX`. Это имя основано на имени приложения службы Azure Bot.

    [![Создание бота веб-приложения](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Прежде, чем продолжить, дождитесь создания службы ботов.

## <a name="the-bot-has-a-language-understanding-model"></a>Бот содержит модель распознавания речи

В процессе создания службы ботов также создается приложение LUIS с намерениями и примерами речевых фрагментов. В новом приложении LUIS бот предоставляет сопоставление намерений для следующих целей. 

|Намерения базового бота LUIS|Пример высказывания|
|--|--|
|Заказ авиабилетов|`Travel to Paris`|
|Отмена|`bye`|
|Нет|Все, что находится за пределами домена приложения.|

## <a name="test-the-bot-in-web-chat"></a>Тестирование бота в веб-чате

1. Не выходя из портала Azure для нового бота, выберите **Test in Web Chat** (Тестирование в веб-чате). 
1. В текстовом поле **Введите сообщение** введите `hello`. Ответом бота будут сведения об платформе ботов, а также примеры запросов для данной модели LUIS, например заказ авиабилетов в Париж. 

    ![Снимок экрана портала Azure, на котором введен текст "hello".](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

    Для быстрой проверки бота можно выполнить тестирование функциональных возможностей. Для выполнения более сложного тестирования, в которое входит отладка, скачайте код бота и используйте Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Скачивание исходного кода бота веб-приложения
Чтобы разработать код бота веб-приложения, загрузите код и используйте его на локальном компьютере. 

1. Выберите **Сборка** в разделе **Bot management** (Управление ботом) на портале Azure. 

1. Выберите **Download Bot source code** (Загрузка исходного кода бота). 

    [![Загрузка исходного кода бота веб-приложения для базового бота](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. При появлении всплывающего диалогового окна с запросом **Include app settings in the downloaded zip file?** (Включить параметры приложения в скачанный ZIP-файл?) выберите **Да**.

1. Когда исходный код запакован, появляется сообщение со ссылкой для загрузки кода. Перейдите по ссылке. 

1. Сохраните ZIP-файл на локальном компьютере и извлеките файлы. Откройте проект с помощью Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Проверка кода, который используется для отправки высказывания в LUIS и получение ответа

1. Откройте файл **LuisHelper.cs**. Это место, где фраза пользователя, введенная в бот, отправляется в LUIS. Ответ от LUIS будет получен из метода как объект **BookDetails**. При создании бота также необходимо создать объект для получения подробной информации с помощью LUIS. 


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
        }
    }
    ```

1. Чтобы просмотреть, как объект извлекает сведения, полученные от LUIS, откройте файл **BookingDetails.cs**. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    namespace Microsoft.BotBuilderSamples
    {
        public class BookingDetails
        {
            public string Destination { get; set; }
    
            public string Origin { get; set; }
    
            public string TravelDate { get; set; }
        }
    }
    ```

1. Чтобы понять, как объект BookingDetails используется для управлением потоком общения, откройте файл **Диалоговые окна -> BookingDialog.cs**. Подробная информация о поездке запрашивается поэтапно, а затем все бронирование подтверждается. После этого пользователю отправляется ответ. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    
    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class BookingDialog : CancelAndHelpDialog
        {
            public BookingDialog()
                : base(nameof(BookingDialog))
            {
                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(new ConfirmPrompt(nameof(ConfirmPrompt)));
                AddDialog(new DateResolverDialog());
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    DestinationStepAsync,
                    OriginStepAsync,
                    TravelDateStepAsync,
                    ConfirmStepAsync,
                    FinalStepAsync,
                }));
    
                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }
    
            private async Task<DialogTurnResult> DestinationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                if (bookingDetails.Destination == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where would you like to travel to?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Destination, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> OriginStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Destination = (string)stepContext.Result;
    
                if (bookingDetails.Origin == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where are you traveling from?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Origin, cancellationToken);
                }
            }
            private async Task<DialogTurnResult> TravelDateStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Origin = (string)stepContext.Result;
    
                if (bookingDetails.TravelDate == null || IsAmbiguous(bookingDetails.TravelDate))
                {
                    return await stepContext.BeginDialogAsync(nameof(DateResolverDialog), bookingDetails.TravelDate, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.TravelDate, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> ConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.TravelDate = (string)stepContext.Result;
    
                var msg = $"Please confirm, I have you traveling to: {bookingDetails.Destination} from: {bookingDetails.Origin} on: {bookingDetails.TravelDate}";
    
                return await stepContext.PromptAsync(nameof(ConfirmPrompt), new PromptOptions { Prompt = MessageFactory.Text(msg) }, cancellationToken);
            }
    
            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if ((bool)stepContext.Result)
                {
                    var bookingDetails = (BookingDetails)stepContext.Options;
    
                    return await stepContext.EndDialogAsync(bookingDetails, cancellationToken);
                }
                else
                {
                    return await stepContext.EndDialogAsync(null, cancellationToken);
                }
            }
    
            private static bool IsAmbiguous(string timex)
            {
                var timexProperty = new TimexProperty(timex);
                return !timexProperty.Types.Contains(Constants.TimexTypes.Definite);
            }
        }
    }
    ```


## <a name="start-the-bot-code-in-visual-studio"></a>Запуск кода бота в Visual Studio

Запустите бот в Visual Studio. Откроется окно браузера с веб-сайтом бота веб-приложения на `http://localhost:3978/`. Сведения о боте отображаются на домашней странице.

![Сведения о боте отображаются на домашней странице.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Использование эмулятора бота для тестирования бота

1. Запустите эмулятор бота и выберите **Open Bot** (Открыть бот).
1. Во сплывающем диалоговом окне **Open Bot** (Открыть бот) введите URL-адрес бота, например `http://localhost:3978/api/messages`. Путь `/api/messages` — веб-адрес бота.
1. Введите **Microsoft App ID** (Идентификатор приложения Майкрософт) и **Microsoft App password**(Пароля приложения Майкрософт), которые можно найти в файле **appsettings.json** в корне кода бота, который был скачан.

    Кроме этого, можно создать новую конфигурацию бота и скопировать `appId` и `appPassword` из файла **appsettings.json** в проекте Visual Studio для бота. Название файла конфигурации бота должно соответствовать названию бота. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from appsettings.json>",
                "appPassword": "<appPassword from appsettings.json>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. В эмуляторе бота введите `Hello` и получите такой же ответ от основного бота, который соответствует ответу, полученному при **Тестировании в веб-чате**.

    [![Ответ базового бота в эмуляторе](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Вопрос боту о заказе билет на самолет

1. В эмуляторе бота закажите билет на самолет путем введения следующего речевого фрагмента. 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    Эмулятор бота запросит подтверждение. 

1. Выберите **Да**. Бот ответит сводкой своих действий. 
1. Из журнала эмулятора бота выберите строку, в которой содержится `Luis Trace`. Появится ответ в формате JSON, полученный от LUIS, для намерения и сущностей речевого фрагмента.

    [![Ответ базового бота в эмуляторе](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные [примеры](https://github.com/microsoft/botframework-solutions) чат-ботов. 

> [!div class="nextstepaction"]
> [Tutorial: Build LUIS app to determine user intentions](luis-quickstart-intents-only.md) (Учебник: создание приложения LUIS для определения намерений пользователя)
