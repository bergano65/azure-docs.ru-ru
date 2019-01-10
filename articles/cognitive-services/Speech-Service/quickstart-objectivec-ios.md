---
title: Краткое руководство. Распознавание речи с помощью Objective-C в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как распознавать речь в Objective-C на iOS с помощью пакета SDK для службы "Речь".
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: chlandsi
ms.openlocfilehash: 75411ebd50448c5f490a1f03fbbf25a61dbffaf8
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718118"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-service-sdk"></a>Краткое руководство. Распознавание речи в Objective-C на iOS с помощью пакета SDK для службы "Речь"

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как с помощью пакета SDK для службы "Речь" в Cognitive Services можно создать приложение iOS в Objective-C, которое будет преобразовывать аудиозапись речи в текст.

## <a name="prerequisites"></a>Предварительные требования

Предварительные требования для начала работы:

* [Ключ подписки](get-started.md) для службы "Речь".
* Компьютер под управлением macOS с [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) или более поздней версии.
* iOS 11.4 или более поздней версии в качестве целевой версии.

## <a name="get-the-speech-sdk-for-ios"></a>Получение пакета SDK службы "Речь" для iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Текущая версия пакета SDK для распознавания речи для Cognitive Services — `1.2.0`.

В настоящее время пакет SDK службы "Речь" для Cognitive Services для Mac и iOS распределяется под названием Cocoa Framework.
Его можно скачать по ссылке: https://aka.ms/csspeech/iosbinary. Загрузите файл в домашний каталог.

## <a name="create-an-xcode-project"></a>Создание проекта Xcode

Запустите Xcode и создайте новый проект, щелкнув **Файл** > **Новый** > **Проект**.
В диалоговом окне выбора шаблона выберите шаблон iOS Single View App (приложение iOS с одним представлением).

В следующих диалоговых окнах задайте следующие параметры:

1. Диалоговое окно Project Options (Параметры проекта)
    1. Введите имя приложения быстрого запуска, например `helloworld`.
    1. Если у вас уже есть учетная запись разработчика Apple, введите соответствующие название и идентификатор организации. В целях тестирования вы можете выбрать любое имя, например `testorg`. Чтобы подписать приложение, вам нужен соответствующий профиль подготовки. Дополнительные сведения см. на [сайте разработчиков Apple](https://developer.apple.com/).
    1. Убедитесь в том, что для проекта выбран язык Objective-C.
    1. Снимите все флажки тестов и основных данных.
    ![Параметры проекта](media/sdk/qs-objectivec-project-settings.png)
1. Выбор каталога проекта
    1. Выберите свой домашний каталог, чтобы поместить в него проект. Это создаст в вашем домашнем каталоге каталог `helloworld`, содержащий все файлы проекта Xcode.
    1. Для этого примера проекта отключите создание репозитория Git.
    1. Измените пути к пакету SDK в *Параметрах проекта*.
        1. На вкладке **Общие** в разделе **Embedded Binaries** (Внедренные двоичные файлы) добавьте в качестве платформы библиотеку пакета SDK: Последовательно выберите **Add embedded binaries (Добавить внедренные двоичные файлы)** > **Add other... (Добавить другое...)**, перейдите к корневому каталогу и выберите файл `MicrosoftCognitiveServicesSpeech.framework`. Также автоматически добавится библиотека пакета SDK в заголовок **Linked Framework and Libraries** (Связанные платформы и библиотеки).
        ![Добавленная платформа](media/sdk/qs-objectivec-framework.png)
        1. Перейдите на вкладку **Build Settings** (Параметры сборки) и активируйте **Все** параметры.
        1. Добавьте каталог `$(SRCROOT)/..` в *Пути поиска платформы* под заголовком **Пути поиска**.
        ![Настройка путей поиска платформы](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Настройка пользовательского интерфейса

В примере приложения будет очень простой пользовательский интерфейс: две кнопки для запуска распознавания речи из файла или из данных, поступающих в микрофон, и текстовая подпись для отображения результата.
Пользовательский интерфейс настраивается в элементе проекта `Main.storyboard`.
Откройте XML-представление раскадровки, щелкнув правой кнопкой мыши запись `Main.storyboard` в дереве проекта и выбрав **Open As...**(Открыть как...) > **Source Code**(Исходный код).
Замените автоматически сгенерированный XML на этот код:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Добавление примеров кода

1. Загрузите [образец файла wav](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav), щелкнув правой кнопкой мыши ссылку и выбрав **Save target as...** (Сохранить объект как...). Добавьте в проект файл wav в качестве ресурса, перетащив его из окна Finder (Поиск) в корневой уровень представления проекта.
Нажмите в следующем диалоговом окне **Finish** (Готово) без изменения настроек.
1. Замените содержимое автоматически сгенерированного файла `ViewController.m` на:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Замените строку `YourSubscriptionKey` своим ключом подписки.
1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).
1. Добавьте запрос на доступ к микрофону. В дереве проекта щелкните правой кнопкой мыши запись `Info.plist` и выберите **Open As...** > **Source Code** ("Открыть как..." > "Исходный код"). Добавьте в раздел `<dict>` следующие строки, а затем сохраните файл.
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="building-and-running-the-sample"></a>Сборка и запуск примера

1. Сделайте вывод отладки видимым: **View**(Представление) > **Debug Area**(Отладка области) > **Activate Console**(Активировать консоль).
1. Выберите либо симулятор iOS, либо устройство iOS, подключенное к компьютеру для разработки, в качестве места назначения для приложения из списка в меню **Продукт** -> **Место назначения**.
1. Соберите и выполните пример кода в симуляторе iOS, выбрав в меню **Product**(Продукт) -> **Run**(Запустить) или нажав кнопку **Play**(Воспроизвести).
В настоящее время пакет SDK для службы "Речь" поддерживает только 64-разрядные платформы iOS.
1. Нажав кнопку Recognize (File) (Распознать (файл)) в приложении, вы должны увидеть содержимое звукового файла What's the weather like? в нижней части экрана.

 ![Сымитированное приложение iOS](media/sdk/qs-objectivec-simulated-app.png)

1. Нажав кнопку Recognize (Microphone) (Распознать (микрофон)) в приложении и сказав несколько слов, вы должны увидеть произнесенный текст в нижней части экрана.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Ознакомьтесь с примерами на Objective-C на сайте GitHub](https://aka.ms/csspeech/samples)
