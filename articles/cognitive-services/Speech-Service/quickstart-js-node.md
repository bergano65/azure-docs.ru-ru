---
title: Краткое руководство. Распознавание речи с использованием JavaScript в Node.js с помощью пакета SDK для службы "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как распознавать речь с использованием JavaScript в Node.js с помощью пакета SDK для службы "Речь"
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: fmegen
ms.openlocfilehash: 35652b169067bc545fa0d1fcc977bbaee79ec3aa
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724441"
---
# <a name="quickstart-recognize-speech-in-javascript-in-nodejs-using-the-speech-service-sdk"></a>Краткое руководство. Распознавание речи с использованием JavaScript в Node.js с помощью пакета SDK для службы "Речь"

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как создать проект Node.js, использующий привязку JavaScript из пакета SDK для службы "Речь" в Cognitive Services, который будет преобразовывать речь в текст.
Приложение создано на базе [пакета SDK для службы "Речь" в Microsoft Cognitive Services](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Предварительные требования

* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).
* Текущая версия [Node.js](https://nodejs.org).

## <a name="create-a-new-project-folder"></a>Создание папки нового проекта

Создайте новую пустую папку и инициализируйте ее в качестве нового проекта Node.js на основе JavaScript.

```sh
npm init -f
```

В результате инициализируются файлы package.json со значениями по умолчанию. Вы, вероятно, захотите изменить этот файл позже.

## <a name="install-the-speech-sdk-for-javascript-into-that-folder"></a>Установка пакета SDK для службы "Речь" для JavaScript в созданную папку

Добавьте пакет SDK для службы "Речь" в проект Node.js с помощью команды `npm install microsoft-cognitiveservices-speech-sdk`.

В результате будет скачана и установлена последняя версия пакета SDK для службы "Речь" и все необходимые компоненты из npmjs. Пакет SDK будет установлен в каталог `node_modules` внутри папки проекта.

## <a name="using-the-speech-sdk"></a>Использование пакета SDK для службы "Речь"

Создайте в папке файл `index.js` и откройте его в текстовом редакторе.

> [!NOTE]
> Обратите внимание, что в Node.js пакет SDK для службы "Речь" не поддерживает микрофон или тип данных файла. Они поддерживаются только в браузерах. Вместо этого используйте интерфейс потоковой передачи для пакета SDK для службы "Речь" с помощью `AudioInputStream.createPushStream()` либо `AudioInputStream.createPullStream()`.

В этом примере используется интерфейс `PushAudioInputStream`.

Добавьте следующий код JavaScript:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="running-the-sample-from-command-line"></a>Запуск примера из командной строки

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

## <a name="running-the-sample-from-visual-studio-code"></a>Запуск примера Visual Studio Code

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
