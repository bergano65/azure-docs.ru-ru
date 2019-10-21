---
title: Краткое руководство. Распознавание речи с помощью Objective-C — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как распознавать речь в iOS с помощью Objective-C и пакета SDK службы "Речь".
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: f943f47cdef901f80aa455d3d1e02a753e0f06e4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327762"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в iOS с помощью Objective-C и пакета SDK службы "Речь"

Кроме того, доступны краткие руководства по [синтезу речи](quickstart-text-to-speech-objectivec-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как с помощью пакета SDK службы "Речь" в Azure Cognitive Services можно создать приложение iOS в Objective-C, которое будет преобразовывать в текст речь из микрофона или аудиофайла.

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы вам потребуются:

* [Ключ подписки](get-started.md) для службы "Речь".
* Компьютер под управлением macOS с [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) или более поздней версии.
* iOS 9.3 или последующей версии в качестве целевой версии.

## <a name="get-the-speech-sdk-for-ios"></a>Получение пакета SDK службы "Речь" для iOS

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Пакет SDK службы "Речь" для Cognitive Services для iOS сейчас доступен в виде Cocoa Framework.
Скачать его можно [на этом веб-сайте](https://aka.ms/csspeech/iosbinary). Загрузите файл в домашний каталог.

## <a name="create-an-xcode-project"></a>Создание проекта Xcode

Запустите Xcode и создайте проект, щелкнув **File** (Файл) > **New** (Новый) > **Project** (Проект).
В диалоговом окне выбора шаблона выберите шаблон **iOS Single View App** (Приложение iOS с одним представлением).

В следующих диалоговых окнах задайте следующие параметры.

1. В диалоговом окне **Project Options** (Параметры проекта):
    1. Введите имя приложения быстрого запуска, например *helloworld*.
    1. Если у вас уже есть учетная запись разработчика Apple, введите соответствующие название и идентификатор организации. В целях тестирования используйте соответствующее имя, например *testorg*. Чтобы подписать приложение, вам нужен соответствующий профиль подготовки. Дополнительные сведения см. на [сайте разработчиков Apple](https://developer.apple.com/).
    1. Убедитесь в том, что для проекта выбран язык **Objective-C**.
    1. Снимите все флажки для тестов и основных данных.

    ![Параметры проекта](media/sdk/qs-objectivec-project-settings.png)

1. Выберите каталог проекта:
   1. Выберите свой домашний каталог, чтобы поместить в него проект. В результате этого в домашнем каталоге будет создан каталог helloworld, содержащий все файлы проекта Xcode.
   1. Для этого примера проекта отключите создание репозитория Git.
   1. Измените пути к пакету SDK на экране параметров проекта.
      1. На вкладке **General** (Общие) под заголовком **Embedded Binaries** (Встроенные двоичные файлы) добавьте библиотеку пакета SDK в качестве платформы, выбрав **Add embedded binaries** (Добавить встроенные двоичные файлы) > **Add other** (Добавить другое). Перейдите к домашнему каталогу и выберите файл `MicrosoftCognitiveServicesSpeech.framework`. Кроме того, автоматически добавится библиотека пакета SDK в заголовок **Linked Framework and Libraries** (Связанные платформы и библиотеки).
         ![Добавленная платформа](media/sdk/qs-objectivec-framework.png)
      1. Перейдите на вкладку **Build Settings** (Параметры сборки) и выберите параметр **All** (Все).
      1. Добавьте каталог $(SRCROOT)/.. в поле **Framework Search Paths** (Пути поиска платформы) под заголовком **Search Paths** (Пути поиска).

      ![Настройка путей поиска платформы](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Настройка пользовательского интерфейса

В примере приложения используется очень простой пользовательский интерфейс: две кнопки для запуска распознавания речи из файла или из данных, поступающих в микрофон, и текстовая подпись для отображения результата. Пользовательский интерфейс настраивается в элементе проекта `Main.storyboard`. Откройте XML-представление раскадровки, щелкнув правой кнопкой мыши запись `Main.storyboard` в дереве проекта и выбрав **Open As...** (Открыть как...) > **Source Code** (Исходный код).

Замените автоматически сгенерированный XML на этот код:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Добавление примеров кода

1. Загрузите [пример файла WAV](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav), щелкнув правой кнопкой мыши ссылку и выбрав **Save target as** (Сохранить объект как).
   Добавьте в проект файл wav в качестве ресурса, перетащив его из окна Finder (Поиск) в корневой уровень представления проекта.
   В следующем диалоговом окне нажмите кнопку **Finish** (Готово) без изменения настроек.
1. Замените содержимое автоматически созданного файла `ViewController.m` на код, приведенный ниже:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Замените строку `YourSubscriptionKey` своим ключом подписки.
1. Замените строку `YourServiceRegion` на [регион](regions.md), связанный с вашей подпиской. Например, используйте `westus` для подписки с бесплатной пробной версией.
1. Добавьте запрос на доступ к микрофону. В дереве проекта щелкните правой кнопкой мыши запись `Info.plist` и выберите **Open As** > **Source Code** (Открыть как > Исходный код). Добавьте в раздел `<dict>` следующие строки, а затем сохраните файл.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

1. Сделайте вывод отладки видимым, выбрав **View** (Представление) > **Debug Area** (Область отладки) > **Activate Console** (Активировать консоль).
1. Выберите либо симулятор iOS, либо устройство iOS, подключенное к компьютеру для разработки, в качестве места назначения для приложения из списка в меню **Продукт** > **Место назначения**.
1. Соберите и выполните пример кода в симуляторе iOS, выбрав в меню **Product** (Продукт) > **Run** (Запустить). Можно также нажать кнопку **Play** (Воспроизвести).
1. Нажав кнопку **Recognize (File)** (Распознать (файл)) в приложении, вы должны увидеть содержимое звукового файла What's the weather like? в нижней части экрана.

   ![Имитированное приложение iOS](media/sdk/qs-objectivec-simulated-app.png)

1. Нажав кнопку **Recognize (Microphone)** (Распознать (микрофон)) в приложении и сказав несколько слов, вы должны увидеть произнесенный текст в нижней части экрана.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Ознакомьтесь с примерами на Objective-C на сайте GitHub](https://aka.ms/csspeech/samples)
