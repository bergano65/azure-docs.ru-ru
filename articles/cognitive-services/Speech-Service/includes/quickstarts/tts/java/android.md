---
title: Краткое руководство. Синтезирование речи с помощью Java (Android) в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как синтезировать речь в Android, используя Java и пакет SDK службы "Речь"
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: e38b1bdd3258675dfac9a155f7cee338a1f8f806
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818456"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../get-started.md)
> * [Настроить среду разработки.](../../../../quickstarts/setup-platform.md?tabs=android)
> * [Создать пустой пример проекта.](../../../../quickstarts/create-project.md?tabs=android)

## <a name="create-user-interface"></a>Создание пользовательского интерфейса

Для этого приложения мы создадим базовый пользовательский интерфейс. Измените макет для основного действия, `activity_main.xml`. В макете должны отображаться имя вашего приложения в заголовке окна и элемент TextView, содержащий текст "Hello World!".

1. Щелкните элемент TextView. Измените его атрибут ID в правом верхнем углу, указав `outputMessage`, и перетащите его на нижний экран. Удалите его текст.

1. Перетащите элемент button (кнопка) из палитры в левом верхнем углу окна `activity_main.xml` на пустое место над текстом.

1. В области атрибутов кнопки справа для атрибута `onClick` введите значение `onSpeechButtonClicked`. Мы создадим метод с таким именем для обработки события кнопки.  Измените его атрибут ID в правом верхнем углу, указав `button`.

1. Перетащите обычный текст в пространство над кнопкой. Измените атрибут "ID" на `speakText` и измените атрибут "Text" на `Hi there!`.

1. Используйте значок волшебной палочки в верхней части окна конструктора, чтобы вывести ограничения макета.


    ![Снимок экрана со значком "волшебная палочка"](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

Теперь текстовая и графическая версии пользовательского интерфейса должны выглядеть следующим образом:

![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте исходный файл `MainActivity.java`. Замените все содержимое этого файла приведенным ниже кодом.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Метод `onSpeechButtonClicked`, как упоминалось ранее, является обработчиком нажатия кнопки. Нажатие кнопки активирует синтез речи.

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](~/articles/cognitive-services/Speech-Service/regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Подключите устройство Android к компьютеру разработки. Убедитесь, что вы включили на устройстве [режим разработки и отладку по USB](https://developer.android.com/studio/debug/dev-options). Кроме того, можно создать [эмулятор Android](https://developer.android.com/studio/run/emulator).

1. Чтобы выполнить сборку приложения, нажмите комбинацию клавиш Ctrl + F9 или выберите в строке меню **Сборка** > **Make Project** (Создать проект).

1. Чтобы запустить приложение, нажмите комбинацию клавиш Shift+F10 или выберите**Запуск** > **Run 'app'** (Запустить "приложение").

1. В отобразившемся окне целей развертывания выберите свое устройство Android или эмулятор.

   ![Снимок экрана окна Select Deployment Target (Выбор цели развертывания)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Чтобы начать процесс синтезирования речи, введите текст и нажмите кнопку в приложении. Вы услышите синтезированный звук из динамика по умолчанию и увидите сведения `speech synthesis succeeded` на экране.

![Снимок экрана приложения Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>См. также

- [Создание пользовательского голоса](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
