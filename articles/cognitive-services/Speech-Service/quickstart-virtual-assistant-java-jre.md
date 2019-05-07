---
title: Краткое руководство. Пользовательский виртуальный помощник по обработке голоса (предварительная версия), Java (Windows, Linux). Службы распознавания речи
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как использовать пакет средств разработки программного обеспечения (SDK) для службы "Речь" в Cognitive Services в консольном приложении Java. Вы узнаете, как подключить клиентское приложение к ранее созданному боту Bot Framework, настроенному для использования канала "Речь Direct Line", и включить функцию виртуального помощника по обработке голоса.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: bidishac
ms.openlocfilehash: 4e9010bed54d0b2a7cb1a95b9e01e5ba02ea9fd5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025368"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Краткое руководство. Создание виртуального помощника по обработке голоса с помощью пакета SDK для распознавания речи, Java

Из этой статьи вы узнаете, как создать консольное приложение Java с помощью [пакета SDK для службы "Речь" в Cognitive Services](speech-sdk.md). Приложение подключится к ранее созданному боту, настроенному на использование канала "Речь Direct Line", отправит голосовой запрос и вернет действие голосового ответа (если настроено). Приложение создается с помощью пакета SDK Maven службы "Речь" и Eclipse Java IDE для Windows, Ubuntu Linux или macOS. Оно работает в 64-разрядной среде выполнения Java 8 (JRE).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* Операционная система: Windows (64-разрядная версия), Ubuntu Linux 16.04/18.04 (64-разрядная версия), а также macOS 10.13 или более поздней версии
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) или [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).
* Предварительно настроенный бот, созданный с помощью Bot Framework версии 4.2 или более поздней. Для получения речевого ввода бот необходимо подписать на новый канал "Речь Direct Line".

    > [!NOTE]
    > В настоящее время в предварительной версии канала "Речь Direct Line" поддерживается только регион **westus2**.

    > [!NOTE]
    > 30-дневная пробная версия ценовой категории "Стандартный", описанная в разделе [Try Speech Services for free](get-started.md) (Бесплатная пробная подписка на службу "Речь"), ограничена регионом **westus** (а не **westus2**) и поэтому несовместима с каналом "Речь Direct Line". Подписки в регионе **westus2** ценовых категорий "Стандартный" и "Бесплатный" совместимы с этим каналом.

Если вы используете Ubuntu 16.04 или 18.04, убедитесь, что перед запуском Eclipse у вас установлены следующие зависимости.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Если вы используете Windows (64-разрядная версия), убедитесь, что у вас установлен распространяемый компонент Microsoft Visual C++ для вашей платформы.
* [Скачать распространяемый компонент Microsoft Visual C++ для Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Необязательно: Быстрое начало работы

В этом кратком пошаговом руководстве описано, как создать простое клиентское приложение для подключения к боту с поддержкой речи. Если вы хотите сразу приступить к работе, полноценный, готовый к компиляции исходный код, используемый в этом кратком руководстве, доступен в [Примерах пакета SDK для распознавания речи](https://aka.ms/csspeech/samples) в папке `quickstart`.

## <a name="create-and-configure-project"></a>Создание и настройка проекта

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Кроме того, для включения ведения журнала обновите файл **pom.xml**, чтобы добавить следующие зависимости.

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>Добавление примеров кода

1. Выберите **Файл** > **Создать** > **Класс**, чтобы добавить пустой класс в проект Java.

1. В окне **New Java Class** (Новый класс Java) введите **speechsdk.quickstart** в поле **Пакет** и **Main** в поле **Имя**.

   ![Снимок экрана окна "Новый класс Java"](media/sdk/qs-java-jre-06-create-main-java.png)

1. Откройте только что созданный класс **Main** и замените содержимое файла `Main.java` следующим начальным кодом.

    ```java
    package speechsdk.quickstart;

    import java.io.IOException;
    import java.io.PipedOutputStream;
    import java.util.HashMap;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import com.microsoft.cognitiveservices.speech.ResultReason;
    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }
    }
    ```

1. В методе **main** сначала настройте `BotConnectorConfig` и используйте его для создания экземпляра `SpeechBotConnector`. Он будет подключаться к каналу "Речь Direct Line" для взаимодействия с ботом. Экземпляр `AudioConfig` также используется для указания источника звуковых входных данных. В этом примере микрофон по умолчанию используется с `AudioConfig.fromDefaultMicrophoneInput()`.

    * Замените строку `YourSubscriptionKey` своим ключом подписки, который вы можете получить [здесь](get-started.md).
    * Замените строку `YourServiceRegion` на [регион](regions.md), связанный с вашей подпиской.
    * Замените строку `YourChannelSecret` секретом канала "Речь Direct Line".

    > [!NOTE]
    > В настоящее время в предварительной версии канала "Речь Direct Line" поддерживается только регион **westus2**.

    > [!NOTE]
    > 30-дневная пробная версия ценовой категории "Стандартный", описанная в разделе [Try Speech Services for free](get-started.md) (Бесплатная пробная подписка на службу "Речь"), ограничена регионом **westus** (а не **westus2**) и поэтому несовместима с каналом "Речь Direct Line". Подписки в регионе **westus2** ценовых категорий "Стандартный" и "Бесплатный" совместимы с этим каналом.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // your subscription key
    final String region = "YourServiceRegion"; // Your service region. Currently assumed to be westus2
    final BotConnectorConfig botConnectorConfig = BotConnectorConfig.fromSecretKey(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a SpeechjBotConnector instance
    final SpeechBotConnector botConnector = new SpeechBotConnector(botConnectorConfig, audioConfig);
    ```

1. `SpeechBotConnector` использует несколько событий, чтобы сообщать о работе бота, результатах распознавания речи и других данных. Далее добавьте эти прослушиватели событий.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    botConnector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    botConnector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        botConnector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        String act = activityEventArgs.getActivity().serialize();
        log.info("Received activity: {}", act);
    });
    ```

1. Подключите `SpeechBotConnector` к каналу "Речь Direct Line", вызвав метод `connectAsync()`. Чтобы протестировать свой бот, вы можете вызвать метод `listenOnceAsync` для отправки звуковых входных данных со своего микрофона. Вы можете также использовать метод `sendActivityAsync` для отправки пользовательского действия в виде сериализованной строки. Эти пользовательские действия могут предоставить дополнительные данные, которые ваш бот будет использовать в разговоре.

    ```java
    botConnector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    botConnector.listenOnceAsync();

    // botConnector.sendActivityAsync(...)
    ```

1. Сохраните изменения в файле `Main`.

1. Для поддержки воспроизведения ответов добавьте дополнительный класс, который будет состоять из служебных методов для поддержки аудио. Чтобы включить аудио, добавьте пустой класс в проект Java, выбрав **Файл** > **Создать** > **Класс**.

1. В окне **New Java Class** (Новый класс Java) введите **speechsdk.quickstart** в поле **Пакет** и **AudioPlayer** в поле **Имя**.

   ![Снимок экрана окна "Новый класс Java"](media/sdk/qs-java-jre-06-create-main-java.png)

1. Откройте только что созданный класс **AudioPlayer** и вставьте в него приведенный ниже код.

    ```java
    import static javax.sound.sampled.AudioFormat.Encoding.PCM_SIGNED;

    import java.io.InputStream;
    import java.io.PipedInputStream;
    import java.io.PipedOutputStream;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.concurrent.atomic.AtomicBoolean;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;


    public class AudioPlayer {

        public static final int SAMPLE_RATE = 16000; // 16Hz sampling rate
        public static final int SAMPLE_SIZE_IN_BITS = 16; // 16 bit PCM
        public static final int CHANNELS = 1; // Use Mono / Single channel

        public static final int FRAME_RATE = 16000;
        public static final int FRAME_SIZE = 2;

        private static final Logger log = LoggerFactory.getLogger(AudioPlayer.class);
        private AtomicBoolean isPlaying = new AtomicBoolean(false);
        private ExecutorService executorService = Executors.newSingleThreadExecutor();

        public boolean isPlaying() {
            return isPlaying.get();
        }

        public void stopPlaying() {
            isPlaying.set(false);
        }

        public void play(final PipedOutputStream pipedOutputStream) {
            // The current audio supported by the Microsoft Bot framework ~ 16-bit PCM encoding, 16KHz sampling rate.
            final AudioFormat defaultFormat = new AudioFormat(PCM_SIGNED, SAMPLE_RATE, SAMPLE_SIZE_IN_BITS, CHANNELS, FRAME_SIZE, FRAME_RATE, false);
            try {
                final PipedInputStream inputStream = new PipedInputStream(pipedOutputStream);

                executorService.submit(() -> {
                    try {
                        isPlaying.set(true);
                        play(inputStream, defaultFormat);
                        inputStream.close();
                    } catch (Exception e) {
                        log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
                    }
                });
            } catch (Exception e) {
                log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
            }
        }

        private void play(final InputStream inputStream, final AudioFormat targetFormat) throws Exception {
            final byte[] buffer = new byte[1024];
            final DataLine.Info info = new DataLine.Info(SourceDataLine.class, targetFormat);
            final SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
            line.open();
            if (line != null) {
                line.start();
                int bytesRead = 0;
                while (bytesRead != -1) {
                    bytesRead = inputStream.read(buffer, 0, buffer.length);
                    if (bytesRead != -1) {
                        line.write(buffer, 0, bytesRead);
                    }
                }
                line.drain();
                line.stop();
                line.close();
            }
        }
    }
    ```

1. Сохраните изменения в файле `AudioPlayer`.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

Нажмите клавишу F11 или выберите **Запустить** > **Отладка**.
На консоли отобразится сообщение Say something. В этот момент вы можете произнести фразу или предложение на английском языке, которое бот сможет распознать. Ваша речь будет передана боту через канал "Речь Direct Line", где она будет распознана и обработана ботом, а ответ будет возвращен в качестве действия. Если ваш бот в качестве ответа возвращает речь, аудио будет воспроизводиться с помощью класса `AudioPlayer`.

![Снимок экрана выходных данных консоли после успешного распознавания](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры, такие как чтение речи из аудиофайла, доступны на сайте GitHub.

> [!div class="nextstepaction"]
> [Примеры для Java на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Краткое руководство Перевод речи с помощью пакета SDK службы "Речь" для Java"](quickstart-translate-speech-java-jre.md)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
