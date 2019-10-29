---
title: Краткое руководство. Пользовательский виртуальный помощник по обработке голоса (предварительная версия), Java (Windows, Linux) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как использовать пакет SDK для службы "Речь" в Cognitive Services в консольном приложении Java. Вы узнаете, как подключить клиентское приложение к ранее созданному боту Bot Framework, настроенному для использования канала "Речь Direct Line", и включить функцию виртуального помощника по обработке голоса.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: bidishac
ms.openlocfilehash: ca3d650f9a53f536a00f2a11aca37b2a61556129
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675532"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Краткое руководство. Создание виртуального помощника по обработке голоса с помощью пакета SDK для распознавания речи, Java

Кроме того, доступны краткие руководства по [преобразованию речи в текст](quickstart-java-jre.md), [преобразованию текста в речь](quickstart-text-to-speech-java-jre.md) и [переводу речи](quickstart-translate-speech-java-jre.md).

Из этой статьи вы узнаете, как создать консольное приложение Java с помощью [пакета SDK для службы "Речь" в Azure Cognitive Services](speech-sdk.md). Приложение подключается к ранее созданному боту, настроенному на использование канала "Речь Direct Line", отправляет голосовой запрос и вернет действие голосового ответа (если настроено). Приложение создается с помощью пакета SDK Maven службы "Речь" и Eclipse Java IDE для Windows, Ubuntu Linux или macOS. Оно работает в 64-разрядной среде выполнения Java 8 (JRE).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* Операционная система: Windows (64-разрядная версия), Ubuntu Linux 16.04 или 18.04 (64-разрядная версия), а также macOS 10.13 или более поздней версии.
* [Eclipse Java IDE](https://www.eclipse.org/downloads/).
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) или [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Ключ подписки Azure для служб "Речь". [Получите бесплатно](get-started.md) или создайте его на [портале Azure](https://portal.azure.com).
* Предварительно настроенный бот, созданный с помощью Bot Framework версии 4.2 или более поздней. Для получения речевого ввода бот необходимо подписать на новый канал "Речь Direct Line".

    > [!NOTE]
    > Служба "Речь Direct Line" (предварительная версия) сейчас доступна в ряде регионов службы "Речь". Ознакомьтесь со [списком поддерживаемых регионов для виртуальных помощников по обработке голоса](regions.md#voice-first-virtual-assistants) и убедитесь, что ваши ресурсы развернуты в одном из этих регионов.

Если вы используете Ubuntu 16.04 или 18.04, убедитесь, что перед запуском Eclipse у вас установлены следующие зависимости.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Если вы используете Windows (64-разрядная версия), убедитесь, что у вас установлен Распространяемый компонент Microsoft Visual C++ для вашей платформы.
* [Скачать распространяемый компонент Microsoft Visual C++ для Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Необязательно: Быстрое начало работы

В этом кратком пошаговом руководстве описано, как создать простое клиентское приложение для подключения к боту с поддержкой речи. Если вы хотите сразу приступить к работе, полноценный, готовый к компиляции исходный код, используемый в этом кратком руководстве, доступен в [примерах пакета SDK для службы "Речь"](https://aka.ms/csspeech/samples) в папке `quickstart`.

## <a name="create-and-configure-project"></a>Создание и настройка проекта

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Кроме того, для включения ведения журнала обновите файл *pom.xml*, чтобы добавить следующие зависимости.

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>Добавление примеров кода

1. Выберите **Файл** > **Создать** > **Класс**, чтобы добавить пустой класс в проект Java.

1. В окне **New Java Class** (Новый класс Java) введите *speechsdk.quickstart* в поле **Пакет** и *Main* в поле **Имя**.

   ![Снимок экрана окна "Новый класс Java"](media/sdk/qs-java-jre-06-create-main-java.png)

1. Откройте только что созданный класс `Main` и замените содержимое файла `Main.java` следующим начальным кодом.

    ```java
    package speechsdk.quickstart;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;
    import java.io.InputStream;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }

        private void playAudioStream(PullAudioOutputStream audio) {
            ActivityAudioStream stream = new ActivityAudioStream(audio);
            final ActivityAudioStream.ActivityAudioFormat audioFormat = stream.getActivityAudioFormat();
            final AudioFormat format = new AudioFormat(
                    AudioFormat.Encoding.PCM_SIGNED,
                    audioFormat.getSamplesPerSecond(),
                    audioFormat.getBitsPerSample(),
                    audioFormat.getChannels(),
                    audioFormat.getFrameSize(),
                    audioFormat.getSamplesPerSecond(),
                    false);
            try {
                int bufferSize = format.getFrameSize();
                final byte[] data = new byte[bufferSize];

                SourceDataLine.Info info = new DataLine.Info(SourceDataLine.class, format);
                SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
                line.open(format);

                if (line != null) {
                    line.start();
                    int nBytesRead = 0;
                    while (nBytesRead != -1) {
                        nBytesRead = stream.read(data);
                        if (nBytesRead != -1) {
                            line.write(data, 0, nBytesRead);
                        }
                    }
                    line.drain();
                    line.stop();
                    line.close();
                }
                stream.close();

            } catch (Exception e) {
                e.printStackTrace();
            }
        }

    }
    ```

1. В методе `main` сначала настройте `DialogServiceConfig` и используйте его для создания экземпляра `DialogServiceConnector`. Этот экземпляр подключается к каналу "Речь Direct Line" для взаимодействия с ботом. Экземпляр `AudioConfig` также используется для указания источника звуковых входных данных. В этом примере микрофон по умолчанию используется с `AudioConfig.fromDefaultMicrophoneInput()`.

    * Замените строку `YourSubscriptionKey` своим ключом подписки, который вы можете получить на [этом веб-сайте](get-started.md).
    * Замените строку `YourServiceRegion` на [регион](regions.md), связанный с вашей подпиской.
    * Замените строку `YourChannelSecret` секретом канала "Речь Direct Line".

    > [!NOTE]
    > Служба "Речь Direct Line" (предварительная версия) сейчас доступна в ряде регионов службы "Речь". Ознакомьтесь со [списком поддерживаемых регионов для виртуальных помощников по обработке голоса](regions.md#voice-first-virtual-assistants) и убедитесь, что ваши ресурсы развернуты в одном из этих регионов.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
    final String region = "YourServiceRegion"; // Your speech subscription service region. Note: Only a subset of regions are currently supported.
    final DialogServiceConfig botConfig = DialogServiceConfig.fromBotSecret(channelSecret, subscriptionKey, region);

    // Configure audio input from a microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a DialogServiceConnector instance.
    final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
    ```

1. Соединитель `DialogServiceConnector` использует несколько событий, чтобы сообщать о работе бота, результатах распознавания речи и других данных. Далее добавьте эти прослушиватели событий.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed.
    connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed.
    connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn.
    connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again.
    connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition.
    connector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        connector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses Bot Framework activities.
    connector.activityReceived.addEventListener((o, activityEventArgs) -> {
        final String act = activityEventArgs.getActivity().serialize();
            log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
            if (activityEventArgs.hasAudio()) {
                playAudioStream(activityEventArgs.getAudio());
            }
        });
    ```

1. Подключите `DialogServiceConnector` к каналу "Речь Direct Line", вызвав метод `connectAsync()`. Чтобы протестировать свой бот, вы можете вызвать метод `listenOnceAsync` для отправки звуковых входных данных со своего микрофона. Вы можете также использовать метод `sendActivityAsync` для отправки пользовательского действия в виде сериализованной строки. Эти пользовательские действия могут предоставить дополнительные данные, которые ваш бот использует в разговоре.

    ```java
    connector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    connector.listenOnceAsync();

    // connector.sendActivityAsync(...)
    ```

1. Сохраните изменения в файле `Main`.

1. Чтобы поддержать воспроизведение ответа, добавьте дополнительный класс, который преобразовывает объект PullAudioOutputStream, возвращенный из API getAudio(), в InputStream для Java, чтобы упростить обработку. `ActivityAudioStream` — это специализированный класс, который обрабатывает аудиоотклик из канала "Речь Direct Line". Он предоставляет методы доступа для получения сведений о звуковом формате для обработки воспроизведения. Для этого выберите**File** (Файл) > **New** (Создать) > **Class** (Класс).

1. В окне **New Java Class** (Новый класс Java) введите *speechsdk.quickstart* в поле **Пакет** и *ActivityAudioStream* — в поле **Имя**.

1. Откройте только что созданный класс `ActivityAudioStream` и замените его содержимое следующим кодом.

    ```java
    package com.speechsdk.quickstart;

    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

    import java.io.IOException;
    import java.io.InputStream;


    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second (16 kHz).
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format (16 bits).
         */
        public static final int BITS_PER_SECOND = 16;
        /**
         * The number of audio channels in this format (1 for mono).
         */
        public static final int CHANNELS = 1;
        /**
         * The number of bytes in each frame of a sound that has this format (2).
         */
        public static final int FRAME_SIZE = 2;

        /**
         * Reads up to a specified maximum number of bytes of data from the audio
         * stream, putting them into the given byte array.
         *
         * @param b   the buffer into which the data is read
         * @param off the offset, from the beginning of array <code>b</code>, at which
         *            the data will be written
         * @param len the maximum number of bytes to read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b, int off, int len) {
            byte[] tempBuffer = new byte[len];
            int n = (int) this.pullStreamImpl.read(tempBuffer);
            for (int i = 0; i < n; i++) {
                if (off + i > b.length) {
                    throw new ArrayIndexOutOfBoundsException(b.length);
                }
                b[off + i] = tempBuffer[i];
            }
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Reads the next byte of data from the activity audio stream if available.
         *
         * @return the next byte of data, or -1 if the end of the stream is reached
         * @see #read(byte[], int, int)
         * @see #read(byte[])
         * @see #available
         * <p>
         */
        @Override
        public int read() {
            byte[] data = new byte[1];
            int temp = read(data);
            if (temp <= 0) {
                // we have a weird situation if read(byte[]) returns 0!
                return -1;
            }
            return data[0] & 0xFF;
        }

        /**
         * Reads up to a specified maximum number of bytes of data from the activity audio stream,
         * putting them into the given byte array.
         *
         * @param b the buffer into which the data is read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b) {
            int n = (int) pullStreamImpl.read(b);
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Skips over and discards a specified number of bytes from this
         * audio input stream.
         *
         * @param n the requested number of bytes to be skipped
         * @return the actual number of bytes skipped
         * @throws IOException if an input or output error occurs
         * @see #read
         * @see #available
         */
        @Override
        public long skip(long n) {
            if (n <= 0) {
                return 0;
            }
            if (n <= Integer.MAX_VALUE) {
                byte[] tempBuffer = new byte[(int) n];
                return read(tempBuffer);
            }
            long count = 0;
            for (long i = n; i > 0; i -= Integer.MAX_VALUE) {
                int size = (int) Math.min(Integer.MAX_VALUE, i);
                byte[] tempBuffer = new byte[size];
                count += read(tempBuffer);
            }
            return count;
        }

        /**
         * Closes this audio input stream and releases any system resources associated
         * with the stream.
         */
        @Override
        public void close() {
            this.pullStreamImpl.close();
        }

        /**
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample, and the # channels.
         *
         * @return instance of the ActivityAudioFormat associated with the stream
         */
        public ActivityAudioStream.ActivityAudioFormat getActivityAudioFormat() {
            return activityAudioFormat;
        }

        /**
         * Returns the maximum number of bytes that can be read (or skipped over) from this
         * audio input stream without blocking.
         *
         * @return the number of bytes that can be read from this audio input stream without blocking.
         * As this implementation does not buffer, this will be defaulted to 0
         */
        @Override
        public int available() {
            return 0;
        }

        public ActivityAudioStream(final PullAudioOutputStream stream) {
            pullStreamImpl = stream;
            this.activityAudioFormat = new ActivityAudioStream.ActivityAudioFormat(SAMPLE_RATE, BITS_PER_SECOND, CHANNELS, FRAME_SIZE, AudioEncoding.PCM_SIGNED);
        }

        private PullAudioOutputStream pullStreamImpl;

        private ActivityAudioFormat activityAudioFormat;

        /**
         * ActivityAudioFormat is an internal format which contains metadata regarding the type of arrangement of
         * audio bits in this activity audio stream.
         */
        static class ActivityAudioFormat {

            private long samplesPerSecond;
            private int bitsPerSample;
            private int channels;
            private int frameSize;
            private AudioEncoding encoding;

            public ActivityAudioFormat(long samplesPerSecond, int bitsPerSample, int channels, int frameSize, AudioEncoding encoding) {
                this.samplesPerSecond = samplesPerSecond;
                this.bitsPerSample = bitsPerSample;
                this.channels = channels;
                this.encoding = encoding;
                this.frameSize = frameSize;
            }

            /**
             * Fetch the number of samples played per second for the associated audio stream format.
             *
             * @return the number of samples played per second
             */
            public long getSamplesPerSecond() {
                return samplesPerSecond;
            }

            /**
             * Fetch the number of bits in each sample of a sound that has this audio stream format.
             *
             * @return the number of bits per sample
             */
            public int getBitsPerSample() {
                return bitsPerSample;
            }

            /**
             * Fetch the number of audio channels used by this audio stream format.
             *
             * @return the number of channels
             */
            public int getChannels() {
                return channels;
            }

            /**
             * Fetch the default number of bytes in a frame required by this audio stream format.
             *
             * @return the number of bytes
             */
            public int getFrameSize() {
                return frameSize;
            }

            /**
             * Fetch the audio encoding type associated with this audio stream format.
             *
             * @return the encoding associated
             */
            public AudioEncoding getEncoding() {
                return encoding;
            }
        }

        /**
         * Enum defining the types of audio encoding supported by this stream.
         */
        public enum AudioEncoding {
            PCM_SIGNED("PCM_SIGNED");

            String value;

            AudioEncoding(String value) {
                this.value = value;
            }
        }
    }

    ```

1. Сохраните изменения в файле `ActivityAudioStream`.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

Нажмите клавишу F11 или выберите **Запустить** > **Отладка**.
В консоли появится сообщение Say something.
В этот момент произнесите фразу или предложение на английском языке, которое бот может распознать. Ваша речь передается боту через канал "Речь Direct Line", где она распознается и обрабатывается ботом. Ответ возвратится в качестве действия. Если ваш бот в качестве ответа возвращает речь, аудио воспроизводится с помощью класса `AudioPlayer`.

![Снимок экрана выходных данных консоли после успешного распознавания](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры, такие как чтение речи из аудиофайла, доступны на сайте GitHub.

> [!div class="nextstepaction"]
> [Создание и развертывание простого бота](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>См. также

- [About custom voice-first virtual assistants preview](voice-first-virtual-assistants.md) (Сведения о пользовательских виртуальных помощниках по обработке голоса (предварительная версия))
- [Получите ключ подписки для Служб речи бесплатно](get-started.md)
- [Create a custom wake word by using the Speech service](speech-devices-sdk-create-kws.md) (Создание пользовательских слов для активации с помощью службы "Речь")
- [Подключение бота к каналу Direct Line Speech (предварительная версия)](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Примеры для Java на сайте GitHub](https://aka.ms/csspeech/samples)
