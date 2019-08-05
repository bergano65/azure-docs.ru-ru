---
title: Краткое руководство. Распознавание речи с помощью Node.js — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Сведения в этом руководстве помогут создать консольное приложение для преобразования речи в текст с помощью пакета SDK службы "Речь" для Node.js. После завершения вы сможете преобразовывать речь в текст в режиме реального времени с использованием микрофона компьютера.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 1a3d1bc62e995aff43d9538f49b436a7de16f1e9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554081"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>Краткое руководство. Распознавание речи с помощью пакета SDK службы "Речь" для Node.js

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как создать проект Node.js с помощью привязки JavaScript из пакета SDK для службы "Речь" в Cognitive Services для преобразования речи в текст.
Приложение основано на службе "Речь" пакета SDK для JavaScript, подробнее см. [здесь](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Предварительные требования

* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).
* Текущая версия [Node.js](https://nodejs.org).

## <a name="create-a-new-project"></a>Создание нового проекта

Создайте новую папку и инициализируйте проект:

```sh
npm init -f
```

Эта команда инициализирует файлы **package.json** со значениями по умолчанию. Вы, вероятно, захотите изменить этот файл позже.

## <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

Добавьте пакет SDK для службы "Речь" в проект Node.js:

```
npm install microsoft-cognitiveservices-speech-sdk
```

С помощью этой команды можете скачать и установить последнюю версию пакета SDK для службы "Речь" и все необходимые компоненты из **npmjs**. Пакет SDK будет установлен в каталог `node_modules` внутри папки проекта.

## <a name="use-the-speech-sdk"></a>Использование пакета SDK для службы "Речь"

Создайте в папке файл с именем `index.js` и откройте его в текстовом редакторе.

> [!NOTE]
> В Node.js пакет SDK для службы "Речь" не поддерживает микрофон или тип данных **файла**. Они поддерживаются только в браузерах. Вместо этого используйте интерфейс **Stream** для пакета SDK службы "Речь" с помощью `AudioInputStream.createPushStream()` или `AudioInputStream.createPullStream()`.

В этом примере используется интерфейс `PushAudioInputStream`.

Добавьте следующий код JavaScript.

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>Запуск примера

Чтобы открыть приложение, адаптируйте `YourSubscriptionKey`, `YourServiceRegion` и `YourAudioFile.wav` для своей конфигурации. Затем запустите его с помощью следующей команды.

```sh
node index.js
```

Она активирует распознавание, используя указанное имя файла. И представляет выходные данные на консоль.

В этом примере приведены выходные данные при запуске `index.js` после обновления ключа подписки и использования файла `whatstheweatherlike.wav`:

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Установка и использование пакета SDK для службы "Речь" с помощью Visual Studio Code

Вы также можете запустить пример из Visual Studio Code. Выполните следующие действия, чтобы установить, открыть и выполнить быстрый запуск.

1. Запустите Visual Studio Code. Выберите **Открыть папку**. Затем перейдите к папке быстрого запуска.

   ![Открытие папки](media/sdk/qs-js-node-01-open_project.png)

1. Откройте терминал в Visual Studio Code.

   ![Окно терминала](media/sdk/qs-js-node-02_open_terminal.png)

1. Запустите `npm`, чтобы установить зависимости.

   ![Установка npm](media/sdk/qs-js-node-03-npm_install.png)

1. Теперь можете открыть `index.js` и задать точку останова.

   ![index.js с точкой останова на 16 строке](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Чтобы начать отладку, нажмите клавишу F5 или выберите в меню **Отладка/Начать отладку**.

   ![Меню отладки](media/sdk/qs-js-node-05-start_debugging.png)

1. При достижении точки останова можете проверить стек вызова и переменные.

   ![Отладчик](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. Любые выходные данные отображаются в окне консоли отладки.

   ![Консоль отладки](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для Node.js на сайте GitHub](https://aka.ms/csspeech/samples)
