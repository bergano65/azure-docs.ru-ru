---
title: Использование Персонализатора в чат-боте — Персонализатор
description: Настройте чат-бот C# .NET с помощью цикла Персонализатора, чтобы предоставить пользователю правильное содержимое на основе действий (с признаками) и признаков контекста.
ms.topic: tutorial
ms.date: 07/17/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ee037632b068b0d8d2f3ed3b6ea7a83d157c5083
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935659"
---
# <a name="tutorial-use-personalizer-in-net-chat-bot"></a>Руководство по использованию Персонализатора в чат-боте .NET

Используйте чат-бот C# .NET с помощью цикла Персонализатора, чтобы предоставить пользователю правильное содержимое. Этот чат-бот предлагает пользователю определенный кофе или чай. Пользователь может принять или отклонить это предложение. Персонализатор получает информацию, которая поможет сделать следующее предложение более подходящим.

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Настройка ресурсов Azure
> * Настройка и запуск бота.
> * Взаимодействие с ботом с помощью эмулятора.
> * Получение сведений о том, где и как бот использует Персонализатор.


## <a name="how-does-the-chat-bot-work"></a>Принцип работы чат-бота

Обычно чат-бот ведет дискуссию с пользователем. Этот чат-бот использует Персонализатор, чтобы сделать пользователю лучшее предложение (кофе или чай). Персонализатор использует алгоритмы обучения с подкреплением, чтобы сделать этот выбор.

Чат-боту необходимо следить за ходом общения. Чат-бот использует [Bot Framework](https://github.com/microsoft/botframework-sdk) для управления архитектурой и общением бота, а также использует службу Cognitive Service, [Распознавание речи](../LUIS/index.yml) (LUIS), чтобы понять намерение пользователя на естественном языке.

Чат-бот является веб-сайтом с определенным маршрутом, позволяющим отвечать на запросы, `http://localhost:3978/api/messages`. С помощью эмулятора бота можно визуально взаимодействовать с запущенным чат-ботом во время локальной разработки бота.

### <a name="user-interactions-with-the-bot"></a>Взаимодействие пользователя с ботом

Это простой чат-бот, который позволяет вводить текстовые запросы.

|Текст, вводимый пользователем|Текстовый ответ бота|Описание действий, выполняемых ботом для определения текста ответа|
|--|--|--|
|Текст не введен — бот начинает диалог.|`This is a simple chatbot example that illustrates how to use Personalizer. The bot learns what coffee or tea order is preferred by customers given some context information (such as weather, temperature, and day of the week) and information about the user.`<br>`To use the bot, just follow the prompts. To try out a new imaginary context, type “Reset” and a new one will be randomly generated.`<br>`Welcome to the coffee bot, please tell me if you want to see the menu or get a coffee or tea suggestion for today. Once I’ve given you a suggestion, you can reply with ‘like’ or ‘don’t like’. It’s Tuesday today and the weather is Snowy.`|Бот начинает общение с пояснительным текстом и предоставляет сведения о контексте: `Tuesday`, `Snowy`.|
|`Show menu`|`Here is our menu: Coffee: Cappuccino Espresso Latte Macchiato Mocha Tea: GreenTea Rooibos`|Определение намерения запроса с помощью LUIS, а затем отображение вариантов меню: кофе и чай. Признаки действий. |
|`What do you suggest`|`How about Latte?`|Определение намерения запроса с помощью LUIS, а затем вызов **API ранжирования** и отображение подходящего варианта для вопроса `How about {response.RewardActionId}?`. Также для наглядности отображается вызов и ответ JSON.|
|`I like it`|`That’s great! I’ll keep learning your preferences over time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Определение намерения запроса с помощью LUIS, а затем вызов **API вознаграждения** с вознаграждением `1`. Для наглядности отображается вызов и ответ JSON.|
|`I don't like it`|`Oh well, maybe I’ll guess better next time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Определение намерения запроса с помощью LUIS, а затем вызов **API вознаграждения** с вознаграждением `0`. Для наглядности отображается вызов и ответ JSON.|
|`Reset`|Возвращает пояснительный текст.|Определение намерения запроса с помощью LUIS, затем отображение пояснительного текста и сброс контекста.|


### <a name="personalizer-in-this-bot"></a>Использование Персонализатора в боте

В этом чат-боте используется Персонализатор для выбора подходящего действия (определенного кофе или чая) на основе списка _действий_ (определенного типа содержимого) и признаков контекста.

Бот отправляет список действий вместе с признаками контекста в цикл Персонализатора. Персонализатор возвращает одно лучшее действие боту для отображения.

В рамках этого учебника в качестве **действий** будут использоваться типы кофе и чая:

|Кофе|Чай|
|--|--|
|Капучино<br>Эспрессо<br>Латте<br>Мокко|Зеленый чай<br>Ройбос|

**API ранжирования.** Чтобы помочь Персонализатору лучше узнать о ваших действиях, бот отправляет следующие данные с каждым запросом API ранжирования:

* Действия _с признаками_.
* Признаки контекста

**Признак** модели содержит информацию о действиях и контексте, которые могут быть агрегированы (сгруппированы) для всех членов базы пользователей чат-бота. _Признак_ не является индивидуальным (как идентификатор пользователя) или высокоспециализированным (как точное время дня).

Признаки используются, чтобы действия соответствовали текущему контексту в модели. Модель — это представление прошлых знаний Персонализатора о действиях, контексте и их признаках, которые позволяют принимать обоснованные решения.

Модель, включая признаки, обновляется по расписанию, заданному в разделе **Частота обновления модели** на портале Azure.

Признаки следует выбирать в соответствии с теми же параметрами планирования и разработки, которые вы будете применять ко всем схемам или моделям в системной архитектуре. Значения признаков можно задать с помощью бизнес-логики или сторонних систем.

> [!CAUTION]
> Признаки в этом приложении предназначены для демонстрации. Их не обязательно применять в вашем веб-приложении.

#### <a name="action-features"></a>Признаки действия

Каждое действие (элемент содержимого) имеет признаки, позволяющие отличить кофе от чая.

Они не настраиваются в рамках конфигурации цикла на портале Azure. Вместо этого они отправляются в виде объекта JSON с каждым вызовом API ранжирования. Это предоставляет действиям и их признакам возможность увеличиваться, изменяться и сжиматься в течение периода времени, что позволяет Персонализатору отслеживать тенденции.

Для кофе и чая доступны следующие признаки:

* Место происхождения кофейных зерен, например Кения и Бразилия.
* Являются ли кофе или чай органическими?
* Светлая или темная обжарка кофе.

В то время как в приведенном выше списке у кофе есть три признака, у чая есть только один. Передайте в Персонализатор только те признаки, которые имеют смысл для действия. Не передавайте пустое значение для признака, если оно не применяется к действию.

#### <a name="context-features"></a>Признаки контекста

Признаки контекста помогают Персонализатору понять контекст среды, например устройство вывода, пользователя, расположение и другие признаки, относящиеся к вашему варианту использования.

Контекст для этого чат-бота включает следующее:

* Тип погоды (снежная, дождливая, солнечная).
* День недели.

В этом чат-боте выборка признаков происходит случайно. В реальном коде бота используйте для признаков контекста настоящие данные.

### <a name="design-considerations-for-this-bot"></a>Рекомендации по проектированию такого бота

Обратите внимание на некоторые предостережения, касающиеся такого общения:
* **Взаимодействие с ботом.** Общение очень простое, так как оно демонстрирует ранжирование и вознаграждение в простом варианте использования. Оно не демонстрирует полный набор функций пакета SDK для Bot Framework или эмулятора.
* **Персонализатор.** Для имитации использования признаки выбираются случайно. Не следует рандомизировать признаки в рабочем сценарии Персонализатора.
* **Распознавание речи (LUIS).** Несколько речевых фрагментов модели LUIS предназначены только для этого примера. В рабочем приложении LUIS используйте большое количество примеров речевых фрагментов.


## <a name="install-required-software"></a>Установка необходимого программного обеспечения
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Если вы предпочитаете использовать .NET Core CLI, ознакомьтесь с инструкциями в репозитории примеров для скачивания.
- [Microsoft Bot Framework Emulator](https://aka.ms/botframeworkemulator) — это классическое приложение, которое позволяет разработчикам локально или удаленно (через туннель) тестировать свои боты или выполнять их отладку.

## <a name="download-the-sample-code-of-the-chat-bot"></a>Скачивание примера кода чат-бота

Чат-бот доступен в репозитории с примерами для Персонализатора. Клонируйте или [скачайте](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/archive/master.zip) репозиторий, а затем откройте пример в каталоге `/samples/ChatbotExample` с помощью Visual Studio 2019.

Чтобы клонировать репозиторий, используйте следующую команду Git в оболочке Bash (терминал).

```bash
git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
```

## <a name="create-and-configure-personalizer-and-luis-resources"></a>Создание и настройка Персонализатора и ресурсов LUIS

### <a name="create-azure-resources"></a>Создание ресурсов Azure

Чтобы использовать этот чат-бот, необходимо создать ресурсы Azure для Персонализатора и Распознавания речи (LUIS).

* [Создание ресурсов LUIS.](../luis/luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) На шаге создания выберите **оба** ресурса (для разработки и прогнозирования).
* [Создайте ресурс Персонализатора](how-to-create-resource.md), затем скопируйте ключ и конечную точку на портале Azure. Эти значения нужно будет задать в файле `appsettings.json` проекта .NET.

### <a name="create-luis-app"></a>Создание приложения LUIS

Если вы не знакомы с LUIS, необходимо [войти](https://www.luis.ai) и незамедлительно перенести учетную запись. Вам не нужно создавать ресурсы. Вместо этого выберите ресурсы, созданные в предыдущем разделе этого учебника.

1. Чтобы создать приложение на [портале LUIS](https://www.luis.ai), выберите свою подписку и ресурс для разработки.
1. Затем на той же странице выберите **+ New app for conversation** (Создать приложение для общения), а затем — **Import as JSON** (Импортировать как JSON).
1. Во всплывающем диалоговом окне щелкните **Выбор файла**, а затем выберите файл `/samples/ChatbotExample/CognitiveModels/coffeebot.json`. Укажите имя `Personalizer Coffee bot`.
1. На правой верхней панели навигации портала LUIS нажмите кнопку **Train** (Обучить).
1. Нажмите кнопку **Publish** (Опубликовать), чтобы опубликовать приложение в **рабочем слоте** среды выполнения прогнозирования.
1. Выберите **Manage** (Управление), а затем — **Settings** (Настройка). Скопируйте значение **App ID** (Идентификатор приложения). Это значение нужно будет задать в файле `appsettings.json` проекта .NET.
1. Там же в разделе **Manage** (Управление) выберите **Azure Resources** (Ресурсы Azure). При этом отображаются связанные ресурсы приложения.
1. Выберите **Add prediction resource** (Добавить ресурс прогнозирования). Во всплывающем диалоговом окне выберите свою подписку и ресурс прогнозирования, созданный в предыдущем разделе этого учебника, а затем нажмите кнопку **Done** (Готово).
1. Скопируйте значения **первичного ключа** и **URL-адрес конечной точки**. Эти значения нужно будет задать в файле `appsettings.json` проекта .NET.

### <a name="configure-bot-with-appsettingsjson-file"></a>Настройка бота с помощью файла appsettings.json

1. Откройте файл решения чат-бота `ChatbotSamples.sln` в Visual Studio 2019.
1. Откройте `appsettings.json` в корневом каталоге проекта.
1. Задайте все пять параметров, скопированных в предыдущем разделе этого учебника.

    ```json
    {
      "PersonalizerChatbot": {
        "LuisAppId": "",
        "LuisAPIKey": "",
        "LuisServiceEndpoint": "",
        "PersonalizerServiceEndpoint": "",
        "PersonalizerAPIKey": ""
      }
    }
    ```

### <a name="build-and-run-the-bot"></a>Создание и запуск бота

После настройки `appsettings.json` можно приступать к созданию и запуску чат-бота. При этом в браузере откроется работающий веб-сайт `http://localhost:3978`.

:::image type="content" source="media/tutorial-chat-bot/chat-bot-web-site.png" alt-text="Снимок экрана: браузер, отображающий веб-сайт чат-бота.":::

Поддерживайте работу веб-сайта, так как в учебнике объясняются действия бода для вашего с ним взаимодействия.


## <a name="set-up-the-bot-emulator"></a>Настройка эмулятора бота

1. Откройте эмулятор бота и выберите **Open Bot** (Открыть бот).

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-startup.png" alt-text="Снимок экрана начального экрана эмулятора бота.":::


1. Настройте бот, используя следующий **URL-адрес бота**, а затем нажмите кнопку **Connect** (Подключить):

    `http://localhost:3978/api/messages`

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-open-bot-settings.png" alt-text="Снимок экрана эмулятора бота: параметры открытия бота.":::

    Эмулятор подключается к чат-боту и отображает пояснительный текст, а также данные журнала и отладки, полезные для локальной разработки.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-bot-conversation-first-turn.png" alt-text="Снимок экрана эмулятора бота: начало диалога.":::

## <a name="use-the-bot-in-the-bot-emulator"></a>Использование бота в эмуляторе бота

1. Сделайте запрос меню, введя `I would like to see the menu`. Чат-бот отобразит элементы.
1. Позвольте боту предложить элемент, введя `Please suggest a drink for me.`. Эмулятор отобразит запрос ранжирования и ответ в окне чата, чтобы можно было увидеть полный код JSON. И бот создает предложение примерно такого содержания `How about Latte?`
1. Если вы принимаете предложение, вы также принимаете подходящий ранжированный выбор Персонализатора — `I like it.`. Эмулятор отображает запрос на вознаграждение с оценкой вознаграждения 1 и ответ в окне чата, чтобы можно было увидеть полный код JSON. Бот ответит `That’s great! I’ll keep learning your preferences over time.` и `Would you like to get a new suggestion or reset the simulated context to a new day?`

    Если вы ответили `no`, то в Персонализатор отправляется оценка вознаграждения 0.


## <a name="understand-the-net-code-using-personalizer"></a>Общие сведения о коде .NET в Персонализаторе

Решение .NET — это простой чат-бот на платформе Bot Framework. Код, связанный с Персонализатором, находится в следующих папках:
* `/samples/ChatbotExample/Bots`
    * Файл `PersonalizerChatbot.cs` для взаимодействия бота с Персонализатором.
* `/samples/ChatbotExample/ReinforcementLearning` — управляет действиями и признаками модели Персонализатора.
* `/samples/ChatbotExample/Model` — файлы для действий и признаков Персонализатора, а также для намерений LUIS.

### <a name="personalizerchatbotcs---working-with-personalizer"></a>PersonalizerChatbot.cs — работа с Персонализатором

Класс `PersonalizerChatbot` является производным от `Microsoft.Bot.Builder.ActivityHandler`. Для управления потоком диалога используются три свойства и метода.

> [!CAUTION]
> Не копируйте код из этого учебника. Используйте пример кода из [репозитория с примерами для Персонализатора](https://github.com/Azure-Samples/cognitive-services-personalizer-samples).

```csharp
public class PersonalizerChatbot : ActivityHandler
{

    private readonly LuisRecognizer _luisRecognizer;
    private readonly PersonalizerClient _personalizerClient;

    private readonly RLContextManager _rlFeaturesManager;

    public PersonalizerChatbot(LuisRecognizer luisRecognizer, RLContextManager rlContextManager, PersonalizerClient personalizerClient)
            {
                _luisRecognizer = luisRecognizer;
                _rlFeaturesManager = rlContextManager;
                _personalizerClient = personalizerClient;
            }
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Check LUIS model
            var recognizerResult = await _luisRecognizer.RecognizeAsync(turnContext, cancellationToken);
            var topIntent = recognizerResult?.GetTopScoringIntent();
            if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
            {
                Intents intent = (Intents)Enum.Parse(typeof(Intents), topIntent.Value.intent);
                switch (intent)
                {
                    case Intents.ShowMenu:
                        await turnContext.SendActivityAsync($"Here is our menu: \n Coffee: {CoffeesMethods.DisplayCoffees()}\n Tea: {TeaMethods.DisplayTeas()}", cancellationToken: cancellationToken);
                        break;
                    case Intents.ChooseRank:
                        // Here we generate the event ID for this Rank.
                        var response = await ChooseRankAsync(turnContext, _rlFeaturesManager.GenerateEventId(), cancellationToken);
                        _rlFeaturesManager.CurrentPreference = response.Ranking;
                        await turnContext.SendActivityAsync($"How about {response.RewardActionId}?", cancellationToken: cancellationToken);
                        break;
                    case Intents.RewardLike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 1, cancellationToken);
                            await turnContext.SendActivityAsync($"That's great! I'll keep learning your preferences over time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you like. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.RewardDislike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 0, cancellationToken);
                            await turnContext.SendActivityAsync($"Oh well, maybe I'll guess better next time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you dislike. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.Reset:
                        _rlFeaturesManager.GenerateRLFeatures();
                        await SendResetMessageAsync(turnContext, cancellationToken);
                        break;
                    default:
                        break;
                }
            }
            else
            {
                var msg = @"Could not match your message with any of the following LUIS intents:
                        'ShowMenu'
                        'ChooseRank'
                        'RewardLike'
                        'RewardDislike'.
                        Try typing 'Show me the menu','What do you suggest','I like it','I don't like it'.";
                await turnContext.SendActivityAsync(msg);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Generate a new weekday and weather condition
            // These will act as the context features when we call rank with Personalizer
            _rlFeaturesManager.GenerateRLFeatures();

            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
    }

    // code removed for brevity, full sample code available for download
    private async Task SendWelcomeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendResetMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendByebyeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
    private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
}
```

Методы с префиксом `Send` управляют общением с помощью бота и LUIS. Методы `ChooseRankAsync` и `RewardAsync` взаимодействуют с Персонализатором.

#### <a name="calling-rank-api-and-display-results"></a>Вызов API ранжирования и отображение результатов

Метод `ChooseRankAsync` создает данные JSON для отправки в API ранжирования Персонализатора путем сбора действий с признаками и признаками контекста.

```csharp
private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
{
    IList<object> contextFeature = new List<object>
    {
        new { weather = _rlFeaturesManager.RLFeatures.Weather.ToString() },
        new { dayofweek = _rlFeaturesManager.RLFeatures.DayOfWeek.ToString() },
    };

    Random rand = new Random(DateTime.UtcNow.Millisecond);
    IList<RankableAction> actions = new List<RankableAction>();
    var coffees = Enum.GetValues(typeof(Coffees));
    var beansOrigin = Enum.GetValues(typeof(CoffeeBeansOrigin));
    var organic = Enum.GetValues(typeof(Organic));
    var roast = Enum.GetValues(typeof(CoffeeRoast));
    var teas = Enum.GetValues(typeof(Teas));

    foreach (var coffee in coffees)
    {
        actions.Add(new RankableAction
        {
            Id = coffee.ToString(),
            Features =
            new List<object>()
            {
                new { BeansOrigin = beansOrigin.GetValue(rand.Next(0, beansOrigin.Length)).ToString() },
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
                new { Roast = roast.GetValue(rand.Next(0, roast.Length)).ToString() },
            },
        });
    }

    foreach (var tea in teas)
    {
        actions.Add(new RankableAction
        {
            Id = tea.ToString(),
            Features =
            new List<object>()
            {
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
            },
        });
    }

    // Sending a rank request to Personalizer
    // Here we are asking Personalizer to decide which drink the user is most likely to want
    // based on the current context features (weather, day of the week generated in RLContextManager)
    // and the features of the drinks themselves
    var request = new RankRequest(actions, contextFeature, null, eventId);
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL TO RANK =====\n" +
        "This is what is getting sent to Rank:\n" +
        $"{JsonConvert.SerializeObject(request, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    var response = await _personalizerClient.RankAsync(request, cancellationToken);
    await turnContext.SendActivityAsync(
        $"===== DEBUG MESSAGE RETURN FROM RANK =====\n" +
        "This is what Rank returned:\n" +
        $"{JsonConvert.SerializeObject(response, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    return response;
}
```

#### <a name="calling-reward-api-and-display-results"></a>Вызов API вознаграждения и отображение результатов

Метод `RewardAsync` создает данные JSON для отправки в API вознаграждения Персонализатора путем определения оценки. Оценка определяется на основе намерения LUIS, указанного в тексте пользователя, и отправляется из метода `OnTurnAsync`.

```csharp
private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL REWARD =====\n" +
        "Calling Reward:\n" +
        $"eventId = {eventId}, reward = {reward}\n",
        cancellationToken: cancellationToken);

    // Sending a reward request to Personalizer
    // Here we are responding to the drink ranking Personalizer provided us
    // If the user liked the highest ranked drink, we give a high reward (1)
    // If they did not, we give a low reward (0)
    await _personalizerClient.RewardAsync(eventId, new RewardRequest(reward), cancellationToken);
}
```

## <a name="design-considerations-for-a-bot"></a>Рекомендации по проектированию бота

Этот пример предназначен для демонстрации простого комплексного решения Персонализатора в боте. Ваш вариант использования может быть более сложным.

Если вы намерены использовать Персонализатор в рабочем боте, спланируйте следующее:
* _Каждый раз_ при доступе к Персонализатору в режиме реального времени нужен список вариантов. API ранжирования не может быть пакетным или кэшированным.  Вызов вознаграждения можно отложить или выгрузить в отдельный процесс. Если вы не возвращаете вознаграждение в течение некоторого времени, то для события задается значение вознаграждений по умолчанию.
* Вычисление вознаграждения на основе варианта использования. В этом примере показано два вознаграждения (0 и 1) без диапазона значений между ними и без отрицательного значения оценки. Вашей системе может потребоваться более детальное оценивание.
* Каналы бота. В этом примере используется один канал, но если вы намерены использовать больше одного канала или разные виды ботов в одном канале, то, возможно, придется рассматривать их как часть признаков контекста модели Персонализатора.

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда вы завершите работу с этим учебником, очистите следующие ресурсы:

* Удалите пример каталога проекта.
* Удалите ресурс Персонализатора и LUIS на портале Azure.

## <a name="next-steps"></a>Дальнейшие действия
* [Принцип работы Персонализатора](how-personalizer-works.md)
* [Признаки](concepts-features.md): сведения о концепциях признаков, использующиеся для действий и контекста.
* [Вознаграждения](concept-rewards.md): сведения о вычислении оценки вознаграждения.
