---
title: Краткое руководство. Пользовательский голосовой помощник, Java (Android) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как создать приложение голосового помощника в Java на Android с помощью пакета SDK службы "Речь"
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: travisw
ms.openlocfilehash: 70031513d6f120e539e80d6befad4bf9320fb9c4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975964"
---
# <a name="quickstart-create-a-voice-assistant-in-java-on-android-by-using-the-speech-sdk"></a>Краткое руководство. Создание голосового помощника в Java на Android с помощью пакета SDK службы "Речь"

Кроме того, доступны краткие руководства по [преобразованию речи в текст](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android) и [преобразованию текста в речь](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=android).

В этой статье показано, как создать голосовой помощник на языке Java для Android с помощью [пакета SDK службы "Речь"](speech-sdk.md). Это приложение подключится к боту, который уже создан и настроен с помощью [канала службы "Речь Direct Line"](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech). Затем оно отправит голосовой запрос боту и представит действие голосового ответа.

Приложение создается с помощью пакета SDK Maven версии 3.3 для службы "Речь", а также Android Studio 3.3. Пакет SDK службы "Речь" сейчас совместим с устройствами Android, в которых установлены 32- или 64-разрядные процессоры ARM и процессоры Intel, совместимые с набором команд x86-x64.

> [!NOTE]
> Ознакомьтесь с пакетом SDK службы "Речь" и устройством Roobo в разделе [Сведения о пакете SDK для речевых устройств](speech-devices-sdk.md).

## <a name="prerequisites"></a>Предварительные требования

- Ключ подписки Azure для службы "Речь". [Получите бесплатно](get-started.md) или создайте его на [портале Azure](https://portal.azure.com).
- Ранее созданный бот, настроенный с помощью[канала "Речь Direct Line"](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).
- [Android Studio](https://developer.android.com/studio/) версии 3.3 или более поздней.

    > [!NOTE]
    > Ознакомьтесь со [списком поддерживаемых регионов для голосовых помощников](regions.md#voice-assistants) и убедитесь, что ваши ресурсы развернуты в одном из этих регионов.

## <a name="create-and-configure-a-project"></a>Создание и настройка проекта

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Создание пользовательского интерфейса

В этом разделе мы создадим базовый пользовательский интерфейс для приложения. Начнем с открытия основного действия: `activity_main.xml`. В базовом шаблоне должны отображаться имя приложения в заголовке окна и элемент `TextView` с сообщением "Hello world!".

Затем замените содержимое файла `activity_main.xml` на код, приведенный ниже.

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

Этот XML-файл определяет простой пользовательский интерфейс для взаимодействия с ботом.

- Элемент `button` инициирует взаимодействие и вызывает метод `onBotButtonClicked` при щелчке.
- Элемент `recoText` будет отображать результаты преобразования речи в текст при разговоре с ботом.
- Элемент `activityText` будет отображать полезные данные JSON для последнего действия Bot Framework от бота.

Теперь текстовая и графическая версии пользовательского интерфейса должны выглядеть следующим образом:

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл `MainActivity.java` и замените его содержимое кодом, приведенным ниже.

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from speech subscription information
            BotFrameworkConfig config = BotFrameworkConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * Метод `onCreate` содержит код, который запрашивает разрешения на использование микрофона и подключение к Интернету.

   * Метод `onBotButtonClicked`, как упоминалось ранее, является обработчиком нажатия кнопки. Нажатие кнопки активирует одно взаимодействие ("включение") с ботом.

   * Метод `registerEventListeners` показывает события, используемые `DialogServiceConnector`, и базовую обработку входящих действий.

1. В том же файле замените строки конфигурации в соответствии с ресурсами:

    * Замените `YourChannelSecret` секретом канала службы "Речь Direct Line" для вашего бота.

    * Замените `YourSpeechSubscriptionKey` ключом своей подписки.

    * Замените `YourServiceRegion` [регионом](regions.md), связанным с вашей подпиской. Канал Direct Line Speech сейчас поддерживается только в ряде регионов службы "Речь". Дополнительные сведения см. в разделе [Регионы](regions.md#voice-assistants).

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Подключите устройство Android к компьютеру разработки. Убедитесь, что вы включили на устройстве [режим разработки и отладку по USB](https://developer.android.com/studio/debug/dev-options).

1. Чтобы выполнить сборку приложения, нажмите комбинацию клавиш Ctrl + F9 или выберите в строке меню **Сборка** > **Make Project** (Создать проект).

1. Чтобы запустить приложение, нажмите комбинацию клавиш Shift+F10 или выберите**Запуск** > **Run 'app'** (Запустить "приложение").

1. В отобразившемся окне целей развертывания выберите свое устройство Android.

   ![Снимок экрана окна Select Deployment Target (Выбор цели развертывания)](media/sdk/qs-java-android-12-deploy.png)

После запуска приложения и его активности нажмите кнопку, чтобы начать разговор c ботом. Расшифрованный текст будет отображаться при разговоре, а последнее действие, полученное от вашего бота, будет отображаться после его получения. Если для вашего бота настроено предоставление речевых ответов, преобразование речи в текст будет воспроизводиться автоматически.

![Снимок экрана приложения Android](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание и развертывание простого бота](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>См. также
- [Сведения о голосовых помощниках](voice-assistants.md)
- [Try the Speech service for free](get-started.md) (Бесплатное использование службы "Речь")
- [Пользовательские ключевые слова](speech-devices-sdk-create-kws.md)
- [Подключение бота к каналу Direct Line Speech (предварительная версия)](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Примеры для Java на сайте GitHub](https://aka.ms/csspeech/samples)
