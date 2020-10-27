---
title: Руководство по Бот распознавания речи версии 4 на C#
description: Создавайте чат-боты, интегрированные со службой распознавания речи (LUIS), используя C#. Бот создается с помощью Bot Framework версии 4 и службы бота веб-приложения Azure.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/22/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: a8d6481bdf3476f8f91c437138f4b6f1ebd1f1de
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058205"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Руководство по совместному использованию бота веб-приложения и службы распознавания речи в C#

Использование C# для создания чат-бота для интеграции со службой распознавания речи (LUIS). Бот создается с помощью ресурса [Бот веб-приложения](https://docs.microsoft.com/azure/bot-service/) Azure и [Bot Framework версии 4](https://github.com/Microsoft/botbuilder-dotnet).

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

> [!div class="checklist"]
> * Создание бота веб-приложения. Этот процесс создает новое приложение LUIS.
> * Скачивание проекта бота, созданного веб-службой ботов
> * Запуск бота и эмулятора на локальном компьютере
> * Просмотр результатов высказывания в боте

## <a name="prerequisites"></a>Предварительные требования

* [Эмулятор Bot Framework](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-web-app-bot-resource"></a>Создание ресурса бота веб-приложения

1. На [портале Azure](https://portal.azure.com) выберите **Создание ресурса** .

1. С помощью поля поиска найдите и выберите **Web App Bot** (Бот веб-приложения). Нажмите кнопку **создания** .

1. В **Службе Bot** введите необходимые сведения.

    |Параметр|Назначение|Предлагаемый параметр|
    |--|--|--|
    |Bot handle (Дескриптор бота)|Имя ресурса|`luis-csharp-bot-` + `<your-name>`, например `luis-csharp-bot-johnsmith`|
    |Подписка|Подписка, в которой нужно создать бот.|Основная подписка.
    |Группа ресурсов|Логическая группа ресурсов Azure|Создайте группу для хранения всех ресурсов, используемых с этим ботом, и назовите эту группу `luis-csharp-bot-resource-group`.|
    |Расположение|Регион Azure не обязательно должен совпадать с регионом разработки или публикации LUIS.|`westus`|
    |Ценовая категория|Используется для ограничения запросов службы и выставления счетов.|`F0` — уровень "Бесплатный".
    |Имя приложения.|При развертывании бота в облаке имя используется в качестве поддомена (например, humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, например `luis-csharp-bot-johnsmith`|
    |Шаблон бота|Параметры Bot Framework (см. следующую таблицу)|
    |Расположение приложения LUIS|Должно совпадать с регионом ресурса LUIS|`westus`|
    |расположение или план службы приложений;|Значение по умолчанию не следует изменять.|
    |Application Insights|Значение по умолчанию не следует изменять.|
    |Идентификатор и пароль приложения Майкрософт|Значение по умолчанию не следует изменять.|

1. Выберите следующие компоненты в **шаблоне бота** , а затем нажмите кнопку **Выбрать** под этими параметрами:

    |Параметр|Назначение|Выбор|
    |--|--|--|
    |Язык пакета SDK|Язык программирования бота|**C#**|
    |Бот|Тип бота|**Базовый бот**|

1. Нажмите кнопку **создания** . В Azure будет создана и развернута служба ботов. Элементом этого процесса является создание приложения LUIS с именем `luis-csharp-bot-XXXX`. Это имя основано на имени приложения службы Azure Bot.

    > [!div class="mx-imgBorder"]
    > [![Создание бота веб-приложения](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Прежде, чем продолжить, дождитесь создания службы ботов.

1. Выберите `Go to resource` в уведомлении, чтобы открыть страницу бота веб-приложения.

## <a name="the-bot-has-a-language-understanding-model"></a>Бот содержит модель распознавания речи

В процессе создания службы ботов также создается приложение LUIS с намерениями и примерами речевых фрагментов. В новом приложении LUIS бот предоставляет сопоставление намерений для следующих целей.

|Намерения базового бота LUIS|Пример высказывания|
|--|--|
|Заказ авиабилетов|`Travel to Paris`|
|Отмена|`bye`|
|GetWeather|`what's the weather like?`|
|None|Все, что находится за пределами домена приложения.|

## <a name="test-the-bot-in-web-chat"></a>Тестирование бота в веб-чате

1. Не выходя из портала Azure для нового бота, выберите **Test in Web Chat** (Тестирование в веб-чате).
1. В текстовом поле **Введите сообщение** введите `Book a flight from Seattle to Berlin tomorrow`. Бот реагирует с подтверждением того, что вы хотите купить билет на самолет.

    ![Снимок экрана портала Azure, на котором введен текст "hello".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Для быстрой проверки бота можно выполнить тестирование функциональных возможностей. Для выполнения более сложного тестирования, в которое входит отладка, скачайте код бота и используйте Visual Studio.

## <a name="download-the-web-app-bot-source-code"></a>Скачивание исходного кода бота веб-приложения

Чтобы разработать код бота веб-приложения, загрузите код и используйте его на локальном компьютере.

1. Выберите **Сборка** в разделе **Bot management** (Управление ботом) на портале Azure.

1. Выберите **Download Bot source code** (Загрузка исходного кода бота).

    [![Загрузка исходного кода бота веб-приложения для базового бота](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. При появлении всплывающего диалогового окна с запросом **Include app settings in the downloaded zip file?** (Включить параметры приложения в скачанный ZIP-файл?) выберите **Да** .

1. Когда исходный код запакован, появляется сообщение со ссылкой для загрузки кода. Перейдите по ссылке.

1. Сохраните ZIP-файл на локальном компьютере и извлеките файлы. Откройте проект с помощью Visual Studio.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Проверка кода, который используется для отправки высказывания в LUIS, и получение ответа

1. Чтобы отправить речевой фрагмент пользователя в конечную точку прогнозирования LUIS, откройте файл **FlightBookingRecognizer.cs** . Это место, где фраза пользователя, введенная в бот, отправляется в LUIS. Ответ от LUIS будет получен из метода **RecognizeAsync** .

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;

    namespace Microsoft.BotBuilderSamples
    {
        public class FlightBookingRecognizer : IRecognizer
        {
            private readonly LuisRecognizer _recognizer;

            public FlightBookingRecognizer(IConfiguration configuration)
            {
                var luisIsConfigured = !string.IsNullOrEmpty(configuration["LuisAppId"]) && !string.IsNullOrEmpty(configuration["LuisAPIKey"]) && !string.IsNullOrEmpty(configuration["LuisAPIHostName"]);
                if (luisIsConfigured)
                {
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]);

                    _recognizer = new LuisRecognizer(luisApplication);
                }
            }

            // Returns true if luis is configured in the appsettings.json and initialized.
            public virtual bool IsConfigured => _recognizer != null;

            public virtual async Task<RecognizerResult> RecognizeAsync(ITurnContext turnContext, CancellationToken cancellationToken)
                => await _recognizer.RecognizeAsync(turnContext, cancellationToken);

            public virtual async Task<T> RecognizeAsync<T>(ITurnContext turnContext, CancellationToken cancellationToken)
                where T : IRecognizerConvert, new()
                => await _recognizer.RecognizeAsync<T>(turnContext, cancellationToken);
        }
    }
    ```

1. Выберите **Диалоги -> MainDialog.cs** , чтобы записать речевой фрагмент и отправить его в запрос executeLuisQuery в методе actStep.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Logging;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;

    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class MainDialog : ComponentDialog
        {
            private readonly FlightBookingRecognizer _luisRecognizer;
            protected readonly ILogger Logger;

            // Dependency injection uses this constructor to instantiate MainDialog
            public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                : base(nameof(MainDialog))
            {
                _luisRecognizer = luisRecognizer;
                Logger = logger;

                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(bookingDialog);
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    IntroStepAsync,
                    ActStepAsync,
                    FinalStepAsync,
                }));

                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }

            private async Task<DialogTurnResult> IntroStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if (!_luisRecognizer.IsConfigured)
                {
                    await stepContext.Context.SendActivityAsync(
                        MessageFactory.Text("NOTE: LUIS is not configured. To enable all capabilities, add 'LuisAppId', 'LuisAPIKey' and 'LuisAPIHostName' to the appsettings.json file.", inputHint: InputHints.IgnoringInput), cancellationToken);

                    return await stepContext.NextAsync(null, cancellationToken);
                }

                // Use the text provided in FinalStepAsync or the default if it is the first time.
                var messageText = stepContext.Options?.ToString() ?? "What can I help you with today?\nSay something like \"Book a flight from Paris to Berlin on March 22, 2020\"";
                var promptMessage = MessageFactory.Text(messageText, messageText, InputHints.ExpectingInput);
                return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = promptMessage }, cancellationToken);
            }

            private async Task<DialogTurnResult> ActStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if (!_luisRecognizer.IsConfigured)
                {
                    // LUIS is not configured, we just run the BookingDialog path with an empty BookingDetailsInstance.
                    return await stepContext.BeginDialogAsync(nameof(BookingDialog), new BookingDetails(), cancellationToken);
                }

                // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt.)
                var luisResult = await _luisRecognizer.RecognizeAsync<FlightBooking>(stepContext.Context, cancellationToken);
                switch (luisResult.TopIntent().intent)
                {
                    case FlightBooking.Intent.BookFlight:
                        await ShowWarningForUnsupportedCities(stepContext.Context, luisResult, cancellationToken);

                        // Initialize BookingDetails with any entities we may have found in the response.
                        var bookingDetails = new BookingDetails()
                        {
                            // Get destination and origin from the composite entities arrays.
                            Destination = luisResult.ToEntities.Airport,
                            Origin = luisResult.FromEntities.Airport,
                            TravelDate = luisResult.TravelDate,
                        };

                        // Run the BookingDialog giving it whatever details we have from the LUIS call, it will fill out the remainder.
                        return await stepContext.BeginDialogAsync(nameof(BookingDialog), bookingDetails, cancellationToken);

                    case FlightBooking.Intent.GetWeather:
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        var getWeatherMessageText = "TODO: get weather flow here";
                        var getWeatherMessage = MessageFactory.Text(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        await stepContext.Context.SendActivityAsync(getWeatherMessage, cancellationToken);
                        break;

                    default:
                        // Catch all for unhandled intents
                        var didntUnderstandMessageText = $"Sorry, I didn't get that. Please try asking in a different way (intent was {luisResult.TopIntent().intent})";
                        var didntUnderstandMessage = MessageFactory.Text(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                        await stepContext.Context.SendActivityAsync(didntUnderstandMessage, cancellationToken);
                        break;
                }

                return await stepContext.NextAsync(null, cancellationToken);
            }

            // Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
            // In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
            // will be empty if those entity values can't be mapped to a canonical item in the Airport.
            private static async Task ShowWarningForUnsupportedCities(ITurnContext context, FlightBooking luisResult, CancellationToken cancellationToken)
            {
                var unsupportedCities = new List<string>();

                var fromEntities = luisResult.FromEntities;
                if (!string.IsNullOrEmpty(fromEntities.From) && string.IsNullOrEmpty(fromEntities.Airport))
                {
                    unsupportedCities.Add(fromEntities.From);
                }

                var toEntities = luisResult.ToEntities;
                if (!string.IsNullOrEmpty(toEntities.To) && string.IsNullOrEmpty(toEntities.Airport))
                {
                    unsupportedCities.Add(toEntities.To);
                }

                if (unsupportedCities.Any())
                {
                    var messageText = $"Sorry but the following airports are not supported: {string.Join(',', unsupportedCities)}";
                    var message = MessageFactory.Text(messageText, messageText, InputHints.IgnoringInput);
                    await context.SendActivityAsync(message, cancellationToken);
                }
            }

            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                // If the child dialog ("BookingDialog") was cancelled, the user failed to confirm or if the intent wasn't BookFlight
                // the Result here will be null.
                if (stepContext.Result is BookingDetails result)
                {
                    // Now we have all the booking details call the booking service.

                    // If the call to the booking service was successful tell the user.

                    var timeProperty = new TimexProperty(result.TravelDate);
                    var travelDateMsg = timeProperty.ToNaturalLanguage(DateTime.Now);
                    var messageText = $"I have you booked to {result.Destination} from {result.Origin} on {travelDateMsg}";
                    var message = MessageFactory.Text(messageText, messageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(message, cancellationToken);
                }

                // Restart the main dialog with a different message the second time around
                var promptMessage = "What else can I do for you?";
                return await stepContext.ReplaceDialogAsync(InitialDialogId, promptMessage, cancellationToken);
            }
        }
    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>Запуск кода бота в Visual Studio

Запустите бота в Visual Studio 2019. Откроется окно браузера с веб-сайтом бота веб-приложения на `http://localhost:3978/`. Сведения о боте отображаются на домашней странице.

![Сведения о боте отображаются на домашней странице.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-framework-emulator-to-test-the-bot"></a>Тестирование бота с помощью эмулятора Bot Framework

1. Запустите эмулятор Bot Framework и выберите **Open Bot** (Открыть бота).
1. Во сплывающем диалоговом окне **Open Bot** (Открыть бот) введите URL-адрес бота, например `http://localhost:3978/api/messages`. Путь `/api/messages` — веб-адрес бота.
1. Введите **идентификатор приложения Майкрософт** и **пароль приложения Майкрософт** , которые можно найти в файле **appsettings.json** в корне скачанного кода, а затем щелкните **Connect** (Подключить).

1. В эмуляторе Bot Framework введите `Book a flight from Seattle to Berlin tomorrow` и получите такой же ответ от основного бота, как и ответ, полученный при **тестировании в Web Chat** .

    [![Снимок экрана: Bot Framework Emulator с ответом от основного бота](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Выберите **Да** . Бот ответит сводкой своих действий.
1. Из журнала эмулятора Bot Framework выберите строку, в которой содержится `<- trace LuisV3 Trace`. Появится ответ в формате JSON, полученный от LUIS, для намерения и сущностей речевого фрагмента.

    [![Снимок экрана: ответ от основного бота с выбранной строкой "LuisV3 Trace" и выделенным ответом JSON](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Дальнейшие действия

См. дополнительные [примеры](https://github.com/microsoft/botframework-solutions) чат-ботов.

> [!div class="nextstepaction"]
> [Tutorial: Build LUIS app to determine user intentions](luis-quickstart-intents-only.md) (Учебник: создание приложения LUIS для определения намерений пользователя)
