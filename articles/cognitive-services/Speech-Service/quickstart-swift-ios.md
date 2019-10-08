---
title: Краткое руководство. Распознавание речи с помощью Swift — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как распознавать речь в Swift, используя iOS и пакет SDK службы "Речь"
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: cdd09a860c3840823661e30c9f8ebcb900574531
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803207"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в Swift с использованием iOS и пакета SDK службы "Речь"

Кроме того, доступны краткие руководства по [синтезу речи](quickstart-text-to-speech-swift-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как с помощью пакета SDK для службы "Речь" в Cognitive Services можно создать приложение iOS на Swift, которое будет преобразовывать микрофонную запись речи в текст.

## <a name="prerequisites"></a>Предварительные требования

Предварительные требования для начала работы:

* [Ключ подписки](get-started.md) для службы "Речь".
* Компьютер macOS с [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) или более поздней версии и [CocoaPods](https://cocoapods.org/).

## <a name="get-the-speech-sdk-for-ios"></a>Получение пакета SDK службы "Речь" для iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Обратите внимание, что вам не удастся выполнять инструкции этого руководства, используя пакет SDK версии, предшествующей 1.6.0.

Пакет SDK службы "Речь" в Cognitive Services для iOS сейчас доступен в виде пакета платформы.
Его можно использовать в проектах Xcode в виде диспетчера зависимостей [CocoaPods](https://cocoapods.org/) или скачать по адресу https://aka.ms/csspeech/macosbinary и выполнить привязку вручную. В этом руководстве используется диспетчер зависимостей CocoaPods.

## <a name="create-an-xcode-project"></a>Создание проекта Xcode

Запустите Xcode и создайте новый проект, щелкнув **Файл** > **Новый** > **Проект**.
В диалоговом окне выбора шаблона выберите шаблон iOS Single View App (приложение iOS с одним представлением).

В следующих диалоговых окнах задайте следующие параметры:

1. Диалоговое окно Project Options (Параметры проекта)
    1. Введите имя приложения быстрого запуска, например `helloworld`.
    1. Если у вас есть учетная запись разработчика Apple, введите соответствующие название и идентификатор организации. В целях тестирования вы можете выбрать любое имя, например `testorg`. Чтобы подписать приложение, вам нужен соответствующий профиль подготовки. Дополнительные сведения см. на [сайте разработчиков Apple](https://developer.apple.com/).
    1. Убедитесь в том, что для проекта выбран язык Swift.
    1. Снимите флажки использования раскадровок и создания приложения на основе документа. С помощью программных средств создастся простой пользовательский интерфейс для примера приложения.
    1. Снимите все флажки тестов и основных данных.
1. Выбор каталога проекта
    1. Выберите каталог для размещения проекта. В результате этого в выбранном каталоге будет создан каталог `helloworld`, содержащий все файлы проекта Xcode.
    1. Для этого примера проекта отключите создание репозитория Git.
1. Для приложения также необходимо объявить использование микрофона в файле `Info.plist`. В области обзора щелкните имя файла и добавьте ключ "Privacy — Microphone Usage Description" (Конфиденциальность — описание использования микрофона) со значением "Microphone is needed for speech recognition" (Микрофон необходим для распознавания речи).
    ![Параметры в файле Info.plist](media/sdk/qs-swift-ios-info-plist.png)
1. Закройте проект Xcode. Вы воспользуетесь другим его экземпляром позже после настройки CocoaPods.

## <a name="add-the-sample-code"></a>Добавление примеров кода

1. Поместите новый файл заголовка с именем `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` в каталог `helloworld` в проекте helloworld и вставьте в него следующий код:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Добавьте относительный путь `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` в промежуточный заголовок в параметрах проекта Swift для целевого объекта helloworld в поле *Objective-C Bridging Header* (Заголовок Objective-C) ![Свойства заголовка](media/sdk/qs-swift-ios-bridging-header.png)
1. Замените содержимое автоматически сгенерированного файла `AppDelegate.swift` на:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Замените содержимое автоматически сгенерированного файла `ViewController.swift` на:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. В `ViewController.swift` замените строку `YourSubscriptionKey` своим ключом подписки.
1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

## <a name="install-the-sdk-as-a-cocoapod"></a>Установка пакета SDK в качестве диспетчера зависимостей CocoaPods

1. Установите диспетчер зависимостей CocoaPods, следуя его [инструкциям по установке](https://guides.cocoapods.org/using/getting-started.html).
1. Перейдите в каталог с примером приложения (`helloworld`). Поместите текстовый файл с именем `Podfile` и следующим содержимым в этот каталог:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. В терминале перейдите к каталогу `helloworld` и выполните команду `pod install`. При этом будет создана рабочая область Xcode `helloworld.xcworkspace`, содержащая пример приложения и пакет SDK для службы "Речь" в виде зависимости. Эта рабочая область понадобится позже.

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

1. Откройте рабочую область `helloworld.xcworkspace` в Xcode.
1. Сделайте вывод отладки видимым: **View**(Представление) > **Debug Area**(Отладка области) > **Activate Console**(Активировать консоль).
1. Выберите либо симулятор iOS, либо устройство iOS, подключенное к компьютеру для разработки, в качестве места назначения для приложения из списка в меню **Продукт** > **Место назначения**.
1. Соберите и выполните пример кода в симуляторе iOS, выбрав в меню **Product**(Продукт) > **Run**(Запустить) или нажав кнопку **Play**(Воспроизвести).
1. Нажав кнопку Recognize (Распознать) в приложении и сказав несколько слов, вы должны увидеть произнесенный текст в нижней части экрана.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры на сайте GitHub](https://aka.ms/csspeech/samples)
