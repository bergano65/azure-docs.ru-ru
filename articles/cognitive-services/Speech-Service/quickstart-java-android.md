---
title: Краткое руководство. Распознавание речи с помощью Java (Android) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как распознавать речь в Android, используя Java и пакет SDK службы "Речь"
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 2fb7bc0851868f6551ed57d742c4005550caec99
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675574"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в приложении Java для Android с помощью пакета SDK для службы "Речь"

Кроме того, доступны краткие руководства по [синтезу речи](quickstart-text-to-speech-java-android.md) и [виртуальному помощнику для обработки голоса](quickstart-virtual-assistant-java-android.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как с помощью пакета SDK Azure службы "Речь" в Cognitive Services создать приложение Java для Android, которое будет преобразовывать речь в текст.

Приложение основано на пакете SDK Maven для службы "Речь", а также Android Studio версии 3.3. Пакет SDK службы "Речь" сейчас совместим с устройствами Android, в которых установлены 32- или 64-разрядные процессоры ARM и Intel, совместимые с набором команд x86/x64.

> [!NOTE]
> Ознакомьтесь с пакетом SDK службы "Речь" и устройством Roobo в разделе [Сведения о пакете SDK для речевых устройств](speech-devices-sdk.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется ключ подписки служб распознавания речи. Получить бесплатно Дополнительные сведения см. в статье [Try Speech Services for free](get-started.md) (Бесплатная пробная подписка на службу "Речь").

## <a name="create-and-configure-a-project"></a>Создание и настройка проекта

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-a-user-interface"></a>Создание пользовательского интерфейса

Теперь для этого приложения мы создадим базовый пользовательский интерфейс. Измените макет для основного действия, `activity_main.xml`. В макете должны отображаться имя вашего приложения в заголовке окна и элемент TextView, содержащий текст "Hello World!".

* Выберите элемент TextView. Измените его атрибут ID в правом верхнем углу, указав `hello`.

* Перетащите элемент button (кнопка) из палитры в левом верхнем углу окна `activity_main.xml` на пустое место над текстом.

* В области атрибутов кнопки справа для атрибута `onClick` введите значение `onSpeechButtonClicked`. Мы создадим метод с таким именем для обработки события кнопки. Измените его атрибут ID в правом верхнем углу, указав `button`.

* Используйте значок волшебной палочки в верхней части окна конструктора, чтобы вывести ограничения макета.

  ![Снимок экрана со значком "волшебная палочка"](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Теперь текстовая и графическая версии пользовательского интерфейса должны выглядеть следующим образом:

![Пользовательский интерфейс](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте исходный файл `MainActivity.java`. Замените все содержимое этого файла приведенным ниже кодом:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Метод `onCreate` содержит код, который запрашивает разрешения на использование микрофона и подключения к интернету, а также инициализирует привязки собственной платформы. Настроить привязки собственной платформы нужно только один раз. Это следует сделать в начале инициализации приложения.

   * Метод `onSpeechButtonClicked`, как упоминалось ранее, является обработчиком нажатия кнопки. Нажатие кнопки активирует преобразование речи в текст.

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Кроме того, замените строку `YourServiceRegion` на [регион](regions.md), связанный с вашей подпиской. Например, используйте `westus` для подписки с бесплатной пробной версией.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Подключите устройство Android к компьютеру разработки. Убедитесь, что вы включили на устройстве [режим разработки и отладку по USB](https://developer.android.com/studio/debug/dev-options).

1. Чтобы выполнить сборку приложения, нажмите комбинацию клавиш Ctrl+F9 или выберите в строке меню **Сборка** > **Make Project** (Создать проект).

1. Чтобы запустить приложение, нажмите комбинацию клавиш Shift+F10 или выберите**Запуск** > **Run 'app'** (Запустить "приложение").

1. В отобразившемся окне целей развертывания выберите свое устройство Android.

   ![Снимок экрана окна Select Deployment Target (Выбор цели развертывания)](media/sdk/qs-java-android-12-deploy.png)

Чтобы начать процесс распознавания речи, нажмите кнопку в приложении. Последующие 15 секунд английской речи будут отправлены в службу "Речь" для распознавания. Его результат отображается в приложении Android и в окне "logcat" в Android Studio.

![Снимок экрана приложения Android](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для Java на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
