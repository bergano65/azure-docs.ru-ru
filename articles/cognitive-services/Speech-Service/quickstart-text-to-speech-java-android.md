---
title: Краткое руководство. Синтезирование речи с помощью Java (Android) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как синтезировать речь в Android, используя Java и пакет SDK службы "Речь"
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 17fb0c1a4ba5495506ceb658eb61d0ac2959caf8
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803764"
---
# <a name="quickstart-synthesize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Краткое руководство. Синтезирование речи в приложении Java для Android с помощью пакета SDK для службы "Речь"

Также доступны краткие руководства по [распознаванию речи](quickstart-java-android.md) и [виртуальному помощнику для обработки голоса](quickstart-virtual-assistant-java-android.md).

Из этой статьи вы узнаете, как с помощью пакета SDK службы "Речь" в Cognitive Services создать приложение Java для Android, которое будет синтезировать речь в текст.
Приложение основано на пакете SDK Maven версии 1.7.0 для службы "Речь", а также Android Studio 3.3.
Пакет SDK службы "Речь" сейчас совместим с устройствами Android, в которых установлены 32- или 64-разрядные процессоры ARM и процессоры Intel, совместимые с набором команд x86-x64.

> [!NOTE]
> Ознакомьтесь с пакетом SDK службы "Речь" и устройством Roobo в разделе [Сведения о пакете SDK для речевых устройств](speech-devices-sdk.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется ключ подписки службы "Речь". Получить бесплатно Дополнительные сведения см. в статье [Бесплатная пробная подписка на службу "Речь"](get-started.md).

## <a name="create-and-configure-a-project"></a>Создание и настройка проекта

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Создание пользовательского интерфейса

Для этого приложения мы создадим базовый пользовательский интерфейс. Измените макет для основного действия, `activity_main.xml`. В макете должны отображаться имя вашего приложения в заголовке окна и элемент TextView, содержащий текст "Hello World!".

1. Щелкните элемент TextView. Измените его атрибут ID в правом верхнем углу, указав `outputMessage`, и перетащите его на нижний экран. Удалите его текст.

1. Перетащите элемент button (кнопка) из палитры в левом верхнем углу окна `activity_main.xml` на пустое место над текстом.

1. В области атрибутов кнопки справа для атрибута `onClick` введите значение `onSpeechButtonClicked`. Мы создадим метод с таким именем для обработки события кнопки.  Измените его атрибут ID в правом верхнем углу, указав `button`.

1. Перетащите обычный текст в пространство над кнопкой. Измените атрибут "ID" на `speakText` и измените атрибут "Text" на `Hi there!`.

1. Используйте значок волшебной палочки в верхней части окна конструктора, чтобы вывести ограничения макета.


    ![Снимок экрана со значком "волшебная палочка"](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Теперь текстовая и графическая версии пользовательского интерфейса должны выглядеть следующим образом:

![](media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте исходный файл `MainActivity.java`. Замените все содержимое этого файла приведенным ниже кодом.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Метод `onSpeechButtonClicked`, как упоминалось ранее, является обработчиком нажатия кнопки. Нажатие кнопки активирует синтез речи.

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Подключите устройство Android к компьютеру разработки. Убедитесь, что вы включили на устройстве [режим разработки и отладку по USB](https://developer.android.com/studio/debug/dev-options). Кроме того, можно создать [эмулятор Android](https://developer.android.com/studio/run/emulator).

1. Чтобы выполнить сборку приложения, нажмите комбинацию клавиш Ctrl + F9 или выберите в строке меню **Сборка** > **Make Project** (Создать проект).

1. Чтобы запустить приложение, нажмите комбинацию клавиш Shift+F10 или выберите**Запуск** > **Run 'app'** (Запустить "приложение").

1. В отобразившемся окне целей развертывания выберите свое устройство Android или эмулятор.

   ![Снимок экрана окна Select Deployment Target (Выбор цели развертывания)](media/sdk/qs-java-android-12-deploy.png)

Чтобы начать процесс синтезирования речи, введите текст и нажмите кнопку в приложении. Вы услышите синтезированный звук из динамика по умолчанию и увидите сведения `speech synthesis succeeded` на экране.

![Снимок экрана приложения Android](media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для Java на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Настройка голосов](how-to-customize-voice-font.md)
- [Запись образцов голосов](record-custom-voice-samples.md)
