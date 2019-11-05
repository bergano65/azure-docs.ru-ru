---
title: Руководство по Бот распознавания речи версии 4 на C#
titleSuffix: Azure Cognitive Services
description: Создавайте чат-боты, интегрированные со службой распознавания речи (LUIS), используя C#. Бот создается с помощью Bot Framework версии 4 и службы бота веб-приложения Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 213449a78baf8fc2b7cb6c74709efeaf2a50d5b2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495468"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Руководство по совместному использованию бота веб-приложения и службы распознавания речи в C#

Использование C# для создания чат-бота для интеграции со службой распознавания речи (LUIS). Бот создается с помощью ресурса [Бот веб-приложения](https://docs.microsoft.com/azure/bot-service/) Azure и 4 [версии Bot Framework](https://github.com/Microsoft/botbuilder-dotnet).

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]


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
    |группа ресурсов.|Логическая группа ресурсов Azure|Создайте группу для хранения всех ресурсов, используемых с этим ботом, и назовите эту группу `luis-csharp-bot-resource-group`.|
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
|GetWeather|`what's the weather like?`|
|Нет|Все, что находится за пределами домена приложения.|

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

1. При появлении всплывающего диалогового окна с запросом **Include app settings in the downloaded zip file?** (Включить параметры приложения в скачанный ZIP-файл?) выберите **Да**.

1. Когда исходный код запакован, появляется сообщение со ссылкой для загрузки кода. Перейдите по ссылке. 

1. Сохраните ZIP-файл на локальном компьютере и извлеките файлы. Откройте проект с помощью Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Проверка кода, который используется для отправки высказывания в LUIS, и получение ответа

1. Чтобы отправить речевой фрагмент пользователя в конечную точку прогнозирования LUIS, откройте файл **FlightBookingRecognizer.cs**. Это место, где фраза пользователя, введенная в бот, отправляется в LUIS. Ответ от LUIS будет получен из метода **RecognizeAsync**.  

    ```csharp
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
    ````

1. Выберите **Диалоги -> MainDialog.cs**, чтобы записать речевой фрагмент и отправить его в запрос executeLuisQuery в методе actStep. 

    ```csharp
    public class MainDialog : ComponentDialog
    {
        private readonly FlightBookingRecognizer _luisRecognizer;

        ...

        public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                    : base(nameof(MainDialog))
        {
            _luisRecognizer = luisRecognizer;
            ...
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
        
        ...

    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>Запуск кода бота в Visual Studio

Запустите бота в Visual Studio 2019. Откроется окно браузера с веб-сайтом бота веб-приложения на `http://localhost:3978/`. Сведения о боте отображаются на домашней странице.

![Сведения о боте отображаются на домашней странице.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Использование эмулятора бота для тестирования бота

1. Запустите эмулятор бота и выберите **Open Bot** (Открыть бот).
1. Во сплывающем диалоговом окне **Open Bot** (Открыть бот) введите URL-адрес бота, например `http://localhost:3978/api/messages`. Путь `/api/messages` — веб-адрес бота.
1. Введите **Microsoft App ID** (Идентификатор приложения Майкрософт) и **Microsoft App password**(Пароля приложения Майкрософт), которые можно найти в файле **appsettings.json** в корне кода бота, который был скачан.


1. В эмуляторе бота введите `Book a flight from Seattle to Berlin tomorrow` и получите такой же ответ от основного бота, который соответствует ответу, полученному при **Тестировании в веб-чате**.

    [![Ответ базового бота в эмуляторе](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Выберите **Да**. Бот ответит сводкой своих действий. 
1. Из журнала эмулятора бота выберите строку, в которой содержится `Luis Trace`. Появится ответ в формате JSON, полученный от LUIS, для намерения и сущностей речевого фрагмента.

    [![Ответ базового бота в эмуляторе](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]


## <a name="next-steps"></a>Дополнительная информация

См. дополнительные [примеры](https://github.com/microsoft/botframework-solutions) чат-ботов. 

> [!div class="nextstepaction"]
> [Tutorial: Build LUIS app to determine user intentions](luis-quickstart-intents-only.md) (Учебник: создание приложения LUIS для определения намерений пользователя)
