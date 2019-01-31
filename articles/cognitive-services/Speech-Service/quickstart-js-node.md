---
title: Краткое руководство. Распознавание речи с помощью Node.js в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Сведения в этом руководстве помогут создать консольное приложение для преобразования речи в текст с помощью пакета SDK службы "Речь" для Node.js. После завершения вы сможете преобразовывать речь в текст в режиме реального времени с использованием микрофона компьютера.
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: fmegen
ms.openlocfilehash: c9f83368a540003e8bcc35282c664ec23e4ff068
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226508"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>Краткое руководство. Распознавание речи с помощью пакета SDK службы "Речь" для Node.js

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как создать проект Node.js, использующий привязку JavaScript из пакета SDK для службы "Речь" в Cognitive Services, который будет преобразовывать речь в текст.
Приложение создано на базе [пакета SDK для службы "Речь" в Microsoft Cognitive Services](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Предварительные требования

* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).
* Текущая версия [Node.js](https://nodejs.org).

## <a name="create-a-new-project"></a>Создание нового проекта

Создайте папку и инициализируйте проект.

```sh
npm init -f
```

В результате инициализируются файлы package.json со значениями по умолчанию. Вы, вероятно, захотите изменить этот файл позже.

## <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

Добавьте пакет SDK для службы "Речь" в проект Node.js.

```
npm install microsoft-cognitiveservices-speech-sdk
```

В результате будет скачана и установлена последняя версия пакета SDK для службы "Речь" и все необходимые компоненты из npmjs. Пакет SDK будет установлен в каталог `node_modules` внутри папки проекта.

## <a name="use-the-speech-sdk"></a>Использование пакета SDK для службы "Речь"

Создайте в папке файл `index.js` и откройте его в текстовом редакторе.

> [!NOTE]
> Обратите внимание, что в Node.js пакет SDK для службы "Речь" не поддерживает микрофон или тип данных файла. Они поддерживаются только в браузерах. Вместо этого используйте интерфейс потоковой передачи для пакета SDK для службы "Речь" с помощью `AudioInputStream.createPushStream()` либо `AudioInputStream.createPullStream()`.

В этом примере используется интерфейс `PushAudioInputStream`.

Добавьте следующий код JavaScript:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>Запуск примера

Чтобы запустить приложение, адаптируйте `YourSubscriptionKey`, `YourServiceRegion` и `YourAudioFile.wav` для своей конфигурации. После этого его можно запустить, вызвав следующую команду:

```sh
node index.js
```

Она активирует распознавание на основе предоставленного имени файла и выведет выходные данные в консоли.

Ниже приведен пример выходных данных выполнения `index.js` после обновления ключа подписки и использования файла `whatstheweatherlike.wav`.

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

Вы также можете запустить пример из Visual Studio Code. Выполните следующие действия, чтобы установить, открыть и выполнить шаблон быстрого запуска:

1. Запустите Visual Studio Code и щелкните "Открыть папку", а затем перейдите к папке шаблона быстрого запуска.

   ![Снимок экрана с открытой папкой](media/sdk/qs-js-node-01-open_project.png)

1. Откройте терминал в Visual Studio Code.

   ![Снимок экрана с окном терминала](media/sdk/qs-js-node-02_open_terminal.png)

1. Запустите npm для установки зависимостей.

   ![Снимок экрана установки npm](media/sdk/qs-js-node-03-npm_install.png)

1. Теперь вы готовы открыть `index.js` и задать точку останова.

   ![Снимок экрана index.js с точкой останова на строке 16](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Чтобы начать отладку, нажмите клавишу F5 или выберите в меню "Отладка/Начать отладку".

   ![Снимок экрана с меню отладки](media/sdk/qs-js-node-05-start_debugging.png)

1. При достижении точки останова можно проверить стек вызова и переменные.

   ![Снимок экрана отладчика](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. Любые выходные данные будут показаны в окне консоли отладки.

   ![Снимок экрана консоли отладки](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для Node.js на сайте GitHub](https://aka.ms/csspeech/samples)
