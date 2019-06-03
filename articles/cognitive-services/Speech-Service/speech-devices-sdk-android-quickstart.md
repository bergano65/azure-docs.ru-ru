---
title: Краткое руководство. Запуск пакета SDK для речевых устройств на Android - службы распознавания речи
titleSuffix: Azure Cognitive Services
description: Предварительные требования и инструкции по началу работы с Android Speech SDK устройства.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: bd0160073898a5a83af2e7b2bba2dba007c04513
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66425666"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Краткое руководство. Запуск примера приложения Speech SDK устройства в Android

В этом кратком руководстве вы узнаете, как построить проект с поддержкой речевых функций или используйте его как с помощью пакета SDK устройства речи для Android [расшифровка дикторского текста для диалога](conversation-transcription-service.md) устройства.

С этим руководством требуется [Azure Cognitive Services](get-started.md) учетной записи с ресурсом служб речи. Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](https://azure.microsoft.com/try/cognitive-services/), чтобы получить ключ подписки.

Исходный код для примера приложения входит в состав пакета SDK для речевых устройств. Он также [доступен на веб-сайте GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Технические условия

Прежде чем начать, с помощью пакета SDK устройства речи, вам потребуется:

* Следуйте инструкциям, прилагаемым к [пакета средств разработки](get-speech-devices-sdk.md) питания на устройстве.

* Скачайте последнюю версию [Speech SDK устройств](https://aka.ms/sdsdk-download)и извлеките ZIP-файл в рабочий каталог.
   > [!NOTE]
   > В Android пример Release.zip файле содержится Android примера приложения, и в этом кратком руководстве предполагается, что приложение извлечен в C:\SDSDK\Android-Sample-Release

* Чтобы получить [ключ подписки Azure для службы распознавания речи](get-started.md)

* Если вы планируете использовать расшифровка дикторского текста для сообщений необходимо использовать [циклическая микрофон устройством](get-speech-devices-sdk.md) и служба в данный момент доступна только для «en US» и «zh-CN», в регионах, «centralus» и «eastasia». Необходимо иметь ключ речи в одном из этих регионов, для использования расшифровка дикторского текста для диалога.

* Если вы планируете использовать служб речи для определения намерения (или действия) из фразы для пользователя, нужно будет [службы Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) подписки. Дополнительные сведения о LUIS и распознавание сути высказывания, см. в разделе [распознавания речи намерения с помощью LUIS, C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

    Вы можете [создать простую модель LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) или использовать пример такой модели — LUIS-example.json, доступный в пакете [SDK для речевых устройств на сайте скачивания](https://aka.ms/sdsdk-luis). Чтобы отправить файл JSON модели на [портал LUIS](https://www.luis.ai/home), выберите **Import new app** (Импорт нового приложения) и затем файл JSON.

* Установите [Android Studio](https://developer.android.com/studio/) и [Vysor](https://vysor.io/download/) на вашем компьютере.

## <a name="set-up-the-device"></a>Настройка устройства

1. Запустите Vysor на компьютере.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. Устройство должно быть указано в области **Choose a device** (Выберите устройство). Нажмите кнопку **View** (Просмотреть) рядом с устройством.

1. Подключитесь к беспроводной сети, щелкнув значок папки и выбрав **Параметры** > **WLAN**.

    ![Беспроводная локальная сеть Vysor](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > Если в организации существуют политики, касающиеся подключения устройств к системе Wi-Fi, вам нужно получить MAC-адрес и обратиться в ИТ-отдел, чтобы получить сведения о подключении к беспроводной сети организации.
    >
    > Чтобы узнать MAC-адрес комплекта разработки, щелкните значок папки на рабочем столе комплекта разработки.
    >
    >  ![Папка файлов Vysor](media/speech-devices-sdk/qsg-10.png)
    >
    > Выберите элемент **Параметры**. Найдите элемент "MAC-адрес" и щелкните **MAC-адрес** > **Advanced WLAN** (Дополнительные параметры беспроводной сети). Запишите MAC-адрес, отображаемый в нижней части диалогового окна.
    >
    > ![MAC-адрес Vysor](media/speech-devices-sdk/qsg-11.png)
    >
    > У некоторых организаций может быть ограничение подключения устройства к системе Wi-Fi по времени. Возможно, через определенное количество дней понадобится продлить регистрацию комплекта разработки в системе Wi-Fi.

## <a name="run-the-sample-application"></a>Запуск примера приложения

Проверка настройки пакета средств разработки, построения и установите образец приложения:

1. Запустите Android Studio.

1. Выберите **Открыть существующий проект Android Studio**.

   ![Android Studio. Команда открытия существующего проекта](media/speech-devices-sdk/qsg-5.png)

1. Перейдите в каталог C:\SDSDK\Android-Sample-Release\example. Нажмите кнопку **ОК**, чтобы открыть пример проекта.

1. Добавьте ключ подписки речи к исходному коду. Если вы хотите попробовать распознать намерения, также добавьте свой ключ подписки [службы "Распознавание речи"](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) и идентификатор приложения.

   Для распознавания речи и LUIS данные переходит в MainActivity.java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Если вы используете расшифровка дикторского текста для диалога, голосовых ключ и области данных также нужны в conversation.java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. По умолчанию слово для пробуждения (ключевое слово) — "Компьютер". Можно также попробовать одно из других предоставленных слов для пробуждения — "Машина" или "Помощник". Файлы ресурсов для этих альтернативных слов пробуждения находятся в пакете SDK для речевых устройств в папке "keyword". Например, C:\SDSDK\Android-Sample-Release\keyword\Computer содержит файлы, используемые для слова пробуждения "Компьютер".

   > [!TIP]
   > Кроме того, вы можете [создать пользовательское слово пробуждения](speech-devices-sdk-create-kws.md).

    Чтобы использовать новое слово пробуждения, обновите следующие две строки в `MainActivity.java`и скопируйте пакет пробуждения word в приложение. Например, чтобы использовать wake слово «Компьютер» из kws пакета word wake-machine.zip:

   * Скопируйте пакет пробуждения word в папку «C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\».
   * Обновление `MainActivity.java` с помощью ключевого слова и имя пакета:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Обновите следующие строки, содержащие параметры геометрии микрофонной решетки:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Ниже приведены поддерживаемые значения:

   |Переменная|Значение|Доступные значения|
   |--------|-------|----------------|
   |`DeviceGeometry`|Физическая конфигурация микрофона|Для кольцевого комплекта разработки: `Circular6+1` |
   |||Для линейного комплекта разработки: `Linear4`|
   |`SelectedGeometry`|Программная конфигурация микрофона|Для кольцевого комплекта разработки, использующего все микрофоны: `Circular6+1`|
   |||Для кольцевого комплекта разработки, использующего четыре микрофона: `Circular3+1`|
   |||Для линейного комплекта разработки, использующего все микрофоны: `Linear4`|
   |||Для линейного комплекта разработки, использующего два микрофона: `Linear2`|

1. Чтобы создать приложение, в меню **Запуск** выберите **Run 'app'** (Запустить "Приложение"). Отображается диалоговое окно **Select Deployment Target** (Выбор цели развертывания).

1. Выберите ваше устройство и нажмите кнопку **ОК** для развертывания приложения на устройстве.

    ![Диалоговое окно "Select Deployment Target" (Выбор цели развертывания)](media/speech-devices-sdk/qsg-7.png)

1. Запускается пример приложения пакета SDK для речевых устройств, отображающий следующие параметры:

   ![Пример приложения пакета SDK для речевых устройств и параметры](media/speech-devices-sdk/qsg-8.png)

1. Попробуйте новую демонстрацию с использованием расшифровка дикторского текста для диалога. Запустите фотографировать с запустить сеанс. По умолчанию все является гостем. Тем не менее, при наличии участника голосовой подписи они могут быть помещены в файл `/video/participants.properties` на устройстве. Для создания подписи голоса, рассмотрим [транскрипция диалогов (SDK)](how-to-use-conversation-transcription-service.md).

   ![Расшифровка дикторского текста для диалога демонстрационного приложения](media/speech-devices-sdk/qsg-15.png)

1. Экспериментируйте!

## <a name="troubleshooting"></a>Устранение неполадок

   Если не удается подключиться к устройству речи. Введите следующую команду в окне командной строки. Он возвращает список устройств:

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > Эта команда использует Android Debug Bridge `adb.exe`, который является частью установки Android Studio. Это средство находится в каталоге C:\Users\[имя пользователя]\AppData\Local\Android\Sdk\platform-tools. Вы можете добавить этот каталог в путь, чтобы было удобнее вызывать `adb`. В противном случае нужно указывать полный путь к установке adb.exe в каждой команде, вызывающей `adb`.
   >
   > Если появится сообщение об ошибке `no devices/emulators found` затем проверить подключение кабеля USB и убедиться, что кабель высокого качества.
   >

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Просмотр заметки о выпуске](devices-sdk-release-notes.md)
