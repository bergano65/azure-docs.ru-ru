---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: 70b983d0fc2b13957a3701c778dec074b328a770
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94482761"
---
Одной из основных функций службы "Речь" является распознавание и преобразование человеческой речи (часто это называется преобразованием речи в текст). Из этого краткого руководство вы узнаете, как использовать пакет SDK для службы "Речь" в приложениях и продуктах для выполнения высококачественного преобразования речи в текст.

## <a name="skip-to-samples-on-github"></a>Примеры на GitHub

Если вы хотите сразу перейти к примерам кода, см. [этот репозиторий](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre) на сайте GitHub.

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас есть учетная запись Azure и подписка на службу "Речь". Если у вас нет учетной записи и подписки, [попробуйте службу "Речь" бесплатно](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

Прежде чем выполнять какие-либо действия, необходимо установить пакет SDK для службы "Речь". В зависимости от используемой платформы выполните следующие действия:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Среда выполнения Java <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Создание конфигурации службы "Речь"

Чтобы вызвать службу "Речь" с помощью пакета SDK для службы "Речь", необходимо создать [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable). Этот класс содержит сведения о вашей подписке, такие как ключ и связанный регион, конечная точка, узел или маркер авторизации. Создайте [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) с помощью ключа и региона. Сведения о том, как найти пару "ключ-регион", см. в разделе [Поиск ключей и региона](../../../overview.md#find-keys-and-region).

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

Существует несколько других способов инициализации [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable):

* С помощью конечной точки: передайте конечную точку службы "Речь". Ключ или маркер авторизации являются необязательными.
* С помощью узла: передайте адрес узла. Ключ или маркер авторизации являются необязательными.
* С помощью маркера авторизации: передайте маркер авторизации и связанный регион.

> [!NOTE]
> Независимо от того, используете ли вы распознавание речи, синтез речи, перевод или распознавание намерения, вы всегда создаете конфигурацию.

## <a name="recognize-from-microphone"></a>Распознавание речи с микрофона

Чтобы распознать речь с микрофона устройства, создайте `AudioConfig` с помощью `fromDefaultMicrophoneInput()`. Затем инициализируйте [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable), передав `audioConfig` и `config`.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromMic(speechConfig);
    }

    public static void fromMic(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        System.out.println("Speak into your microphone.");
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

Если необходимо использовать *конкретное* входное аудиоустройство, необходимо указать код устройства в `AudioConfig`. Узнайте, [как получить код устройства](../../../how-to-select-audio-input-devices.md) для входного аудиоустройства.

## <a name="recognize-from-file"></a>Распознавание речи из файла

Чтобы распознавать речь из аудиофайла, а не с помощью микрофона, вам все равно нужно создать `AudioConfig`. Но при создании [`AudioConfig`](/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) вместо вызова `fromDefaultMicrophoneInput()` необходимо вызвать `fromWavFileInput()` и передать путь к файлу.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromFile(speechConfig);
    }

    public static void fromFile(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);
        
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

## <a name="error-handling"></a>Обработка ошибок

В предыдущих примерах мы просто получили распознанный текст с помощью `result.getText()`, но чтобы обработать ошибки и другие ответы, необходимо написать код для обработки результата. Следующий пример кода вычисляет значение [`result.getReason()`](/java/api/com.microsoft.cognitiveservices.speech.resultreason?view=azure-java-stable&preserve-view=true) и выполняет следующее:

* Выводит результат распознавания: `ResultReason.RecognizedSpeech`
* Если совпадений нет, сообщает об этом пользователю: `ResultReason.NoMatch`
* В случае ошибки выводит сообщение об ошибке: `ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

## <a name="continuous-recognition"></a>Непрерывное распознавание

В предыдущих примерах используется одноразовое распознавание, которое распознает один речевой фрагмент. Конец одного речевого фрагмента определяется путем прослушивания до тишины в конце, или пока не будет обработано максимум 15 секунд аудио.

Непрерывное распознавание функционирует иначе. Оно используется, если требуется **контролировать**, когда следует остановить распознавание. Для получения результатов распознавания необходимо подписаться на события `recognizing`, `recognized`и `canceled`. Чтобы отключить распознавание, необходимо вызвать [`stopContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync). Ниже приведен пример того, как выполняется непрерывное распознавание входного аудиофайла.

Начнем с определения входных данных и инициализации [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?preserve-view=true&view=azure-java-stable):

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Теперь создадим переменную для управления состоянием распознавания речи. Для начала мы объявляем `Semaphore` в области класса.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

Мы будем подписываться на события, отправленные из [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?preserve-view=true&view=azure-java-stable).

* [`recognizing`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing?preserve-view=true&view=azure-java-stable): Сигнал для событий, содержащих промежуточные результаты распознавания.
* [`recognized`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized?preserve-view=true&view=azure-java-stable): Сигнал для событий, содержащих окончательные результаты распознавания (указывает на успешность попытки распознавания).
* [`sessionStopped`](/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped?preserve-view=true&view=azure-java-stable): Сигнал для событий, указывающих на окончание распознавания (операция).
* [`canceled`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled?preserve-view=true&view=azure-java-stable): Сигнал для событий, содержащих отмененные результаты распознавания (показывающий попытку распознавания, которая была отменена в результате ошибки распознавания или при прямом запросе об отмене, в случае сбоя транспортировки или протоколирования).

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

Если все настроено, можно вызвать [`startContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.startcontinuousrecognitionasync).

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Режим диктовки

При использовании непрерывного распознавания вы можете включить обработку диктовки с помощью соответствующей функции "Включить диктовку". Этот режим приведет к тому, что экземпляр конфигурации речи будет интерпретировать словесные описания структуры предложения, например, пунктуацию. Например, речевой фрагмент "Живете ли вы в городе вопросительный знак" будет интерпретироваться как текст "Живете ли вы в городе?".

Чтобы включить режим диктовки, используйте метод [`enableDictation`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation?preserve-view=true&view=azure-java-stable) в [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-java-stable).

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Изменение исходного языка

Распространенной задачей распознавания речи является указание языка ввода (или исходного языка). Давайте посмотрим, как изменить язык ввода на французский. В коде найдите [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-java-stable), а затем добавьте следующую строку непосредственно под ней.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?preserve-view=true&view=azure-java-stable) — это параметр, принимающий строку в качестве аргумента. Вы можете указать любое значение в списке поддерживаемых [языковых стандартов/языков](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Улучшение точности распознавания

Существует несколько способов улучшить точность распознавания с помощью пакета SDK службы "Речь". Давайте взглянем на списки фраз. Списки фраз используются для определения известных фраз в звуковых данных, например имени пользователя или определенного местоположения. В список фраз можно добавить отдельные слова или полные фразы. Если в аудиофайл включено точное совпадение всей фразы, во время распознавания в списке фраз используется запись. Если точное совпадение с фразой не найдено, распознавание не происходит.

> [!IMPORTANT]
> Функция "Список фраз" доступна только на английском языке.

Чтобы использовать список фраз, сначала создайте объект [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-java-stable), а затем добавьте определенные слова и фразы с помощью [`AddPhrase`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-java-stable#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_).

Любые изменения [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-java-stable) вступают в силу при следующем распознавании или после повторного подключения к службе "Речь".

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Если необходимо очистить список фраз, выполните следующие действия. 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Другие опции для повышения точности распознавания

Списки фраз — это только один вариант для повышения точности распознавания. Кроме того, вы можете сделать следующее: 

* [Повышение точности с помощью пользовательского распознавания речи](../../../how-to-custom-speech.md)
* [Повышение точности с помощью моделей клиентов](../../../tutorial-tenant-model.md)