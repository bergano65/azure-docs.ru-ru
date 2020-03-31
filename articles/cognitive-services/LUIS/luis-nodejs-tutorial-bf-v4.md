---
title: Руководство по Бот Распознавания речи версии 4 на Node.js
description: Из этого руководства вы узнаете, как создавать чат-боты, интегрированные со службой распознавания речи (LUIS), используя Node.js. Для быстрой реализации решений ботов этот чат-бот использует приложение "Управление персоналом". Бот создается с помощью Bot Framework версии 4 и бота веб-приложения Azure.
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 3ce12176957412a5599ced8b043f553969194efb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987852"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Руководство по совместному использованию бота веб-приложения и службы "Распознавание речи" в Node.js

Создавайте чат-боты, интегрированные со службой "Распознавание речи" (LUIS), используя Node.js. Бот создается с помощью ресурса [Бот веб-приложения](https://docs.microsoft.com/azure/bot-service/) Azure и [Bot Framework версии 4](https://github.com/Microsoft/botbuilder-dotnet).

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

> [!div class="checklist"]
> * Создание бота веб-приложения. Этот процесс создает новое приложение LUIS.
> * Скачивание проекта бота, созданного веб-службой ботов
> * Запуск бота и эмулятора на локальном компьютере
> * Просмотр результатов высказывания в боте

## <a name="prerequisites"></a>Предварительные требования

* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Создание ресурса бота веб-приложения

1. На [портале Azure](https://portal.azure.com) выберите **Создание ресурса**.

1. С помощью поля поиска найдите и выберите **Web App Bot** (Бот веб-приложения). Нажмите кнопку **создания**.

1. В **Службе Bot** введите необходимые сведения.

    |Параметр|Назначение|Предлагаемый параметр|
    |--|--|--|
    |Имя бота|Имя ресурса|`luis-nodejs-bot-` + `<your-name>`, например `luis-nodejs-bot-johnsmith`|
    |Подписка|Подписка, в которой нужно создать бот.|Основная подписка.
    |Группа ресурсов|Логическая группа ресурсов Azure|Создайте группу для хранения всех ресурсов, используемых с этим ботом, и назовите эту группу `luis-nodejs-bot-resource-group`.|
    |Расположение|Регион Azure не обязательно должен совпадать с регионом разработки или публикации LUIS.|`westus`|
    |Ценовая категория|Используется для ограничения запросов службы и выставления счетов.|`F0` — уровень "Бесплатный".
    |Имя приложения.|При развертывании бота в облаке имя используется в качестве поддомена (например, humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, например `luis-nodejs-bot-johnsmith`|
    |Шаблон бота|Параметры Bot Framework (см. следующую таблицу)|
    |Расположение приложения LUIS|Должно совпадать с регионом ресурса LUIS|`westus`|
    |расположение или план службы приложений;|Значение по умолчанию не следует изменять.|
    |Application Insights|Значение по умолчанию не следует изменять.|
    |Идентификатор и пароль приложения Майкрософт|Значение по умолчанию не следует изменять.|

1. Выберите следующие компоненты в **шаблоне бота**, а затем нажмите кнопку **Выбрать** под этими параметрами:

    |Параметр|Назначение|Выбор|
    |--|--|--|
    |Версия пакета SDK|Версия Bot Framework|**Пакет SDK версии 4**|
    |Язык пакета SDK|Язык программирования бота|**Node.js**|
    |Бот|Тип бота|**Базовый бот**|

1. Нажмите кнопку **создания**. В Azure будет создана и развернута служба ботов. Элементом этого процесса является создание приложения LUIS с именем `luis-nodejs-bot-XXXX`. Это имя основано на имени приложения службы Azure Bot.

    > [!div class="mx-imgBorder"]
    > [![Создание бота веб-приложения](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Прежде, чем продолжить, дождитесь создания службы ботов.

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

1. При появлении всплывающего диалогового окна с запросом **Include app settings in the downloaded zip file?** (Включить параметры приложения в скачанный ZIP-файл?) выберите **Да**. Таким образом вы получите доступ к параметрам LUIS.

1. Когда исходный код запакован, появляется сообщение со ссылкой для загрузки кода. Перейдите по ссылке.

1. Сохраните ZIP-файл на локальном компьютере и извлеките файлы. Откройте проект с помощью Visual Studio.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Проверка кода, который используется для отправки высказывания в LUIS, и получение ответа

1. Чтобы отправить речевой фрагмент пользователя в конечную точку прогнозирования LUIS, выберите **Диалоги -> FlightBookingRecognizer.js**. Это место, где фраза пользователя, введенная в бот, отправляется в LUIS. Ответ от LUIS будет получен из метода **executeLuisQuery**.

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. Выберите **Диалоги -> mainDialog**, чтобы записать речевой фрагмент и отправить его в запрос executeLuisQuery в методе actStep.


    ````javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                this.recognizer = new LuisRecognizer(config, {}, true);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Использование эмулятора бота для тестирования бота

Отправьте боту запрос с намерением заказа авиабилета.

1. Запустите эмулятор бота и выберите **Open Bot** (Открыть бот).
1. Во сплывающем диалоговом окне **Open Bot** (Открыть бот) введите URL-адрес бота, например `http://localhost:3978/api/messages`. Путь `/api/messages` — веб-адрес бота.
1. Заполните поля **Microsoft App ID** (Идентификатор приложения Майкрософт) и **Microsoft App password** (Пароль приложения Майкрософт), значения которых можно найти в файле **ENV** в корне скачанного кода бота.

1. В эмуляторе бота введите `Book a flight from Seattle to Berlin tomorrow` и получите такой же ответ от основного бота, который соответствует ответу, полученному при **Тестировании в веб-чате**.

    [![Ответ базового бота в эмуляторе](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Выберите **Да**. Бот ответит сводкой своих действий.
1. Из журнала эмулятора бота выберите строку, в которой содержится `Luis Trace`. Появится ответ в формате JSON, полученный от LUIS, для намерения и сущностей речевого фрагмента.

    [![Ответ базового бота в эмуляторе](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Дальнейшие действия

См. дополнительные [примеры](https://github.com/microsoft/botframework-solutions) чат-ботов.

> [!div class="nextstepaction"]
> [Tutorial: Build LUIS app to determine user intentions](luis-quickstart-intents-only.md) (Учебник: создание приложения LUIS для определения намерений пользователя)