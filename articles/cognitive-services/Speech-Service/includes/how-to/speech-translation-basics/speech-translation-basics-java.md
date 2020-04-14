---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 9f1fdca94edc6d3938c137185e037ba0c57b7a0f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266617"
---
## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас есть учетная запись Azure и подписка на услуги speech. Если у вас нет учетной записи и подписки, [попробуйте услугу Speech бесплатно.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

Прежде чем вы можете сделать что-нибудь, вам нужно установить речь SDK. В зависимости от вашей платформы, следуйте инструкциям в разделе <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Получить речь <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> статьи речи SDK.

## <a name="import-dependencies"></a>Импорт зависимостей

Чтобы выполнить примеры в этой `import` статье, включите следующие утверждения в верхней части*q . *Файл кода Java.

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>Чувствительные данные и переменные среды

Пример исходного кода в этой статье зависит от переменных среды для хранения конфиденциальных данных, таких как ключ подписки ресурса Speech и регион. Файл кода Java `static final String` содержит два значения, которые присваиваются `SPEECH__SUBSCRIPTION__KEY` переменным среды хоста, а именно: и `SPEECH__SERVICE__REGION`. Оба этих поля находятся в области класса, что делает их доступными в телах метода класса. Для получения дополнительной информации [environment variables and application configuration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)о переменных среды см.

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
}
```

## <a name="create-a-speech-translation-configuration"></a>Создание конфигурации речевого перевода

Чтобы вызвать службу speech с помощью Speech [`SpeechTranslationConfig`][config]SDK, необходимо создать . Этот класс включает информацию о вашей подписке, например, ваш ключ и связанный с ним регион, конечную точку, узла или токен авторизации.

> [!TIP]
> Независимо от того, выполняете ли вы распознавание речи, синтез речи, перевод или распознавание намерений, вы всегда будете создавать конфигурацию.

Есть несколько способов, которые [`SpeechTranslationConfig`][config]можно инициализировать:

* С подпиской: пройти в ключе и связанном с ним регионе.
* С конечней точкой: пройти в конечную точку службы речи. Токен ключа или авторизации не является обязательным.
* С хостом: пройдите по адресу хозяина. Токен ключа или авторизации не является обязательным.
* С токеном авторизации: пройти в токенавторизации и связанном с ним регионе.

Давайте взглянем на [`SpeechTranslationConfig`][config] то, как создается создается с помощью ключа и региона. Просмотрите страницу [поддержки региона,](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) чтобы найти идентификатор региона.

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Изменение языка исходного кода

Одной из распространенных задач речевого перевода является определение языка ввода (или источника). Давайте взглянем на то, как вы бы изменить язык ввода на итальянский. В коде взаимодействуйте с [`SpeechTranslationConfig`][config] `setSpeechRecognitionLanguage` экземпляром, вызывая метод.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

Функция [`setSpeechRecognitionLanguage`][recognitionlang] ожидает строку формата языкового локализации. Вы можете предоставить любое значение в столбце **Locale** в списке поддерживаемых [языков/языков.](../../../language-support.md)

## <a name="add-translation-language"></a>Добавление языка перевода

Еще одна общая задача речевого перевода заключается в указании целевых языков перевода, по крайней мере один требуется, но кратные поддерживаются. В следующем фрагменте кода, как французский, так и немецкий язык в качестве языка перевода цели.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

С каждым [`addTargetLanguage`][addlang]вызовом на, новый язык целевого перевода указан. Другими словами, когда речь распознается из исходного языка, каждый целевой перевод доступен как часть полученной операции перевода.

## <a name="initialize-a-translation-recognizer"></a>Инициализация распознавания перевода

После создания [`SpeechTranslationConfig`][config], следующим шагом является инициализация [`TranslationRecognizer`][recognizer]. Когда вы [`TranslationRecognizer`][recognizer]инициализировать, вам нужно `translationConfig`будет передать его ваш . Объект конфигурации предоставляет учетные данные, необходимые службе речи для проверки запроса.

Если вы распознаете речь с помощью микрофона по [`TranslationRecognizer`][recognizer] умолчанию устройства, вот как должно выглядеть:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

Если вы хотите указать устройство ввода звука, то [`AudioConfig`][audioconfig] вам нужно `audioConfig` создать и [`TranslationRecognizer`][recognizer]обеспечить параметр при инициализации вашего .

> [!TIP]
> [Узнайте, как получить идентификатор устройства для ввода звука.](../../../how-to-select-audio-input-devices.md)

Во-первых, вы `AudioConfig` будете ссылаться на объект следующим образом:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

Если вы хотите предоставить аудио файл вместо использования микрофона, вам `audioConfig`все равно нужно будет предоставить . Однако, когда [`AudioConfig`][audioconfig]вы создаете, вместо вызова, `fromDefaultMicrophoneInput`вы будете звонить `fromWavFileInput` и передавать `filename` параметр.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>Преобразование текста

Для перевода речи, речь SDK опирается на микрофон или аудио ввод файла. Распознавание речи происходит перед переводом речи. После того, как все объекты были инициализированы, позвоните функции распознавания и получите результат.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

Для получения дополнительной информации о речи к тексту, см [основы распознавания речи](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Синтез переводов

После успешного распознавания речи и перевода результат содержит все переводы в словаре. Функция [`getTranslations`][translations] возвращает словарь с ключом в качестве языка целевого перевода, а значение — переводным текстом. Признанная речь может быть переведена, а затем синтезирована на другом языке (речь к речи).

### <a name="event-based-synthesis"></a>Синтез на основе событий

Объект `TranslationRecognizer` разоблачает `synthesizing` событие. Событие запускается несколько раз и обеспечивает механизм для извлечения синтезированного звука из результата распознавания перевода. Если вы переводите на несколько языков, [см.](#manual-synthesis) Укажите голос синтеза, [`setVoiceName`][voicename] назначив обработчик `synthesizing` а также предоставив обработчик ановийку событий, получите звук. Следующий пример сохраняет переведенный звук как файл *.wav.*

> [!IMPORTANT]
> Синтез на основе событий работает только с одним переводом, **не** добавляя несколько языков целевого перевода. Кроме того, [`setVoiceName`][voicename] например, язык должен быть таким же, как и на языке целевого перевода; `"de"` может накарте `"de-DE-Hedda"`.

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>Ручной синтез

Функция [`getTranslations`][translations] возвращает словарь, который может быть использован для синтеза аудио из текста перевода. Итерировать каждый перевод и синтезировать перевод. При создании `SpeechSynthesizer` экземпляра `SpeechConfig` объект должен [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename] иметь свойство, настроенное на нужный голос. Следующий пример переводится на пять языков, и каждый перевод затем синтезируется в аудио файл на соответствующем нейронном языке.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

Для получения дополнительной информации о синтезе речи, см [основы синтеза речи](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig?view=azure-java-stable
[audioconfig]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig?view=azure-java-stable
[recognizer]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer?view=azure-java-stable
[recognitionlang]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable
[addlang]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage?view=azure-java-stable
[translations]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations?view=azure-java-stable
[voicename]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename?view=azure-java-stable
[speechsynthesisvoicename]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename?view=azure-java-stable
