---
title: Краткое руководство. Запуск пакета SDK для устройств, подключаемых к службе "Речь", в Android
titleSuffix: Azure Cognitive Services
description: Предварительные требования и инструкции для начала работы с пакетом SDK для речевых устройств в Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: c3eb6ec28879a7c53feb270e33857cd67dc06b0b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111686"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Краткое руководство. Запуск примера приложения на базе пакета SDK для речевых устройств в Android

Из этого краткого руководства вы узнаете, как использовать пакет SDK для речевых устройств в Android для разработки продукта с поддержкой речевых функций или в качестве устройства [транскрибирования бесед](conversation-transcription-service.md).

Для этого руководства требуется [учетная запись Azure Cognitive Services](get-started.md) и ресурс службы "Речь". Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](https://azure.microsoft.com/try/cognitive-services/), чтобы получить ключ подписки.

Исходный код для примера приложения входит в состав пакета SDK для речевых устройств. Он также [доступен на веб-сайте GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Предварительные требования

Чтобы начать использовать пакет SDK для речевых устройств, необходимо:

- Включить устройство, выполнив инструкции, прилагаемым к [комплекту SDK](get-speech-devices-sdk.md).

- Загрузить последнюю версию [пакета SDK для речевых устройств](https://aka.ms/sdsdk-download) и извлечь ZIP-файл в свой рабочий каталог.

  > [!NOTE]
  > Файл Android-Sample-Release.zip содержит пример приложения для Android. В этом кратком руководстве предполагается, что приложение распаковано в папку "C:\SDSDK\Android-Sample-Release".

- Получить [ключ подписки Azure для служб речи](get-started.md).

- Если планируется применять транскрибирование бесед, следует использовать [всенаправленный микрофон](get-speech-devices-sdk.md). В настоящее время эта функция доступна только для языков "en-US" и "zh-CN" в регионах "centralus" и "eastasia". Для использования транскрибирования бесед вам понадобится речевой ключ в одном из упомянутых регионов.

- Если планируется использование служб речи для определения намерений (или действий) в высказываниях пользователей, потребуется подписка на службу [Language Understanding Service (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription). Дополнительные сведения о службе распознавания речи и распознавании намерений см. в статье [Распознавание намерений в речи с помощью пакета SDK службы распознавания речи для C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

  Вы можете [создать простую модель LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) или использовать пример такой модели — LUIS-example.json, доступный в пакете [SDK для речевых устройств на сайте скачивания](https://aka.ms/sdsdk-luis). Чтобы отправить файл JSON модели на [портал LUIS](https://www.luis.ai/home), выберите **Import new app** (Импорт нового приложения) и затем файл JSON.

- Установите [Android Studio](https://developer.android.com/studio/) и [Vysor](https://vysor.io/download/) на вашем компьютере.

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
   > ![Папка файлов Vysor](media/speech-devices-sdk/qsg-10.png)
   >
   > Выберите элемент **Параметры**. Найдите элемент "MAC-адрес" и щелкните **MAC-адрес** > **Advanced WLAN** (Дополнительные параметры беспроводной сети). Запишите MAC-адрес, отображаемый в нижней части диалогового окна.
   >
   > ![MAC-адрес Vysor](media/speech-devices-sdk/qsg-11.png)
   >
   > У некоторых организаций может быть ограничение подключения устройства к системе Wi-Fi по времени. Возможно, через определенное количество дней понадобится продлить регистрацию комплекта разработки в системе Wi-Fi.

## <a name="run-the-sample-application"></a>Запуск примера приложения

Чтобы проверить настройки комплекта SDK, создайте и установите пример приложения:

1. Запустите Android Studio.

1. Выберите **Открыть существующий проект Android Studio**.

   ![Android Studio. Команда открытия существующего проекта](media/speech-devices-sdk/qsg-5.png)

1. Перейдите в каталог C:\SDSDK\Android-Sample-Release\example. Нажмите кнопку **ОК**, чтобы открыть пример проекта.

1. Добавьте свой ключ подписки на речевые службы в исходный код. Если вы хотите попробовать распознать намерения, также добавьте свой ключ подписки [службы "Распознавание речи"](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) и идентификатор приложения.

   При использовании служб речи и LUIS ваша информация отправляется в MainActivity.java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Если вы используете транскрибирование бесед, ваш речевой ключ и информация о регионе должны быть также указаны в conversation.java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Ключевое слово по умолчанию — "Компьютер". Можно также попробовать одно из других предоставленных ключевых слов — "Машина" или "Помощник". Файлы ресурсов для этих альтернативных ключевых слов находятся в пакете SDK для речевых устройств в папке keyword. Например, C:\SDSDK\Android-Sample-Release\keyword\Computer содержит файлы, используемые для слова пробуждения "Компьютер".

   > [!TIP]
   > Кроме того, вы можете [создать пользовательское ключевое слово](speech-devices-sdk-create-kws.md).

   Для использования нового ключевого слова обновите следующие две строки в файле `MainActivity.java` и скопируйте пакет ключевых слов в приложение. Например, чтобы использовать ключевое слово "Машина" из файла kws-machine.zip пакета ключевых слов, сделайте следующее.

   - Скопируйте пакет ключевых слов в папку "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   - Укажите в файле `MainActivity.java` ключевое слово и имя пакета:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Обновите следующие строки, содержащие параметры геометрии микрофонной решетки:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   В таблице перечислены поддерживаемые значения:

   | Переменная | Значение | Доступные значения |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Физическая конфигурация микрофона | Для кольцевого комплекта разработки: `Circular6+1` |
   |          |         | Для линейного комплекта разработки: `Linear4` |
   | `SelectedGeometry` | Программная конфигурация микрофона | Для кольцевого комплекта разработки, использующего все микрофоны: `Circular6+1` |
   |          |         | Для кольцевого комплекта разработки, использующего четыре микрофона: `Circular3+1` |
   |          |         | Для линейного комплекта разработки, использующего все микрофоны: `Linear4` |
   |          |         | Для линейного комплекта разработки, использующего два микрофона: `Linear2` |

1. Чтобы создать приложение, в меню **Запуск** выберите **Run 'app'** (Запустить "Приложение"). Отображается диалоговое окно **Select Deployment Target** (Выбор цели развертывания).

1. Выберите ваше устройство и нажмите кнопку **ОК** для развертывания приложения на устройстве.

   ![Диалоговое окно "Select Deployment Target" (Выбор цели развертывания)](media/speech-devices-sdk/qsg-7.png)

1. Запускается пример приложения пакета SDK для речевых устройств, отображающий следующие параметры:

   ![Пример приложения пакета SDK для речевых устройств и параметры](media/speech-devices-sdk/qsg-8.png)

1. Попробуйте новую демоверсию транскрибирования бесед. Начните транскрибирование, выбрав "Start Session" (Начало сеанса). По умолчанию все являются гостями. Однако, если у вас есть голосовые подписи участника, их можно поместить в файл `/video/participants.properties` на устройстве. Сведения о том, как создать голосовую подпись, см. в статье о [транскрибировании бесед (пакет SDK)](how-to-use-conversation-transcription-service.md).

   ![Демоверсия приложения транскрибирования бесед](media/speech-devices-sdk/qsg-15.png)

1. Экспериментируйте!

## <a name="troubleshooting"></a>Устранение неполадок

Если вы не можете подключиться к службам речи, введите следующую команду в окне командной строки. Отобразится список устройств:

```powershell
 adb devices
```

> [!NOTE]
> Эта команда использует Android Debug Bridge `adb.exe`, который входит в состав установочного пакета Android Studio. Это средство находится в каталоге C:\Users\[имя пользователя]\AppData\Local\Android\Sdk\platform-tools. Вы можете добавить этот каталог в путь, чтобы было удобнее вызывать `adb`. В противном случае нужно указывать полный путь к установке adb.exe в каждой команде, вызывающей `adb`.
>
> Если вы видите ошибку `no devices/emulators found`, проверьте, подключен ли USB-кабель, и убедитесь, что он высокого качества.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Просмотр заметки о выпуске](devices-sdk-release-notes.md)
