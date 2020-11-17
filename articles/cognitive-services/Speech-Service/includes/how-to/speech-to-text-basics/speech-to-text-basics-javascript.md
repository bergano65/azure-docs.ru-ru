---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 52ed0af9d678f93c5b91dbee9a44b754725c2120
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94482799"
---
Одной из основных функций службы "Речь" является распознавание и преобразование человеческой речи (часто это называется преобразованием речи в текст). Из этого краткого руководство вы узнаете, как использовать пакет SDK для службы "Речь" в приложениях и продуктах для выполнения высококачественного преобразования речи в текст.

## <a name="skip-to-samples-on-github"></a>Примеры на GitHub

Если вы хотите сразу перейти к примерам кода, см. [этот репозиторий](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) на сайте GitHub.

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас есть учетная запись Azure и подписка на службу "Речь". Если у вас нет учетной записи и подписки, [попробуйте службу "Речь" бесплатно](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

Сначала необходимо установить <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">пакет SDK службы "Речь" для JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a>. В зависимости от используемой платформы выполните следующие действия:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Веб-браузер <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Кроме того, в зависимости от целевой среды используйте один из следующих параметров:

# <a name="script"></a>[script](#tab/script)

Скачайте <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">пакет SDK службы "Речь" для JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a>, извлеките из него файл *microsoft.cognitiveservices.speech.sdk.bundle.js* и поместите его в папку, доступную для HTML-файла.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Если в качестве платформы вы используете веб-браузер и применяете тег `<script>`, при ссылке на классы использовать префикс `sdk` не нужно. Префикс `sdk` является псевдонимом для присвоения имени модулю `require`.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Дополнительные сведения о функции `require` см. <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">здесь <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="create-a-speech-configuration"></a>Создание конфигурации службы "Речь"

Чтобы вызвать службу "Речь" с помощью пакета SDK для службы "Речь", необходимо создать [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest). Этот класс содержит сведения о вашей подписке, такие как ключ и связанный регион, конечная точка, узел или маркер авторизации. Создайте [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) с помощью ключа и региона. Сведения о том, как найти пару "ключ-регион", см. в разделе [Поиск ключей и региона](../../../overview.md#find-keys-and-region).

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Существует несколько других способов инициализации [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest):

* С помощью конечной точки: передайте конечную точку службы "Речь". Ключ или маркер авторизации являются необязательными.
* С помощью узла: передайте адрес узла. Ключ или маркер авторизации являются необязательными.
* С помощью маркера авторизации: передайте маркер авторизации и связанный регион.

> [!NOTE]
> Независимо от того, используете ли вы распознавание речи, синтез речи, перевод или распознавание намерения, вы всегда создаете конфигурацию.

## <a name="recognize-from-microphone-browser-only"></a>Распознавание речи с микрофона (только с помощью браузера)

Чтобы распознать речь с микрофона устройства, создайте `AudioConfig` с помощью `fromDefaultMicrophoneInput()`. Затем инициализируйте [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest), передав `speechConfig` и `audioConfig`.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromMic() {
    let audioConfig = sdk.AudioConfig.fromDefaultMicrophoneInput();
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    console.log('Speak into your microphone.');
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromMic();
```

Если необходимо использовать *конкретное* входное аудиоустройство, необходимо указать код устройства в `AudioConfig`. Узнайте, [как получить код устройства](../../../how-to-select-audio-input-devices.md) для входного аудиоустройства.

## <a name="recognize-from-file"></a>Распознавание речи из файла 

# <a name="browser"></a>[Браузер](#tab/browser)

Чтобы распознать речь из звукового файла в среде JavaScript на основе браузера, создайте [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest) с помощью функции `fromWavFileInput()`. Для функции `fromWavFileInput()` в качестве параметра необходим объект [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) JavaScript.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    // wavByteContent should be a byte array of the raw wav content
    let file = new File([wavByteContent]);
    let audioConfig = sdk.AudioConfig.fromWavFileInput(file);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromFile();
```

# <a name="nodejs"></a>[Node.js](#tab/node)

Чтобы распознать речь из звукового файла в Node.js, необходимо использовать альтернативный конструктивный шаблон, применяющий поток push-передачи, так как объект [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) JavaScript не может использоваться в среде выполнения Node.js. В приведенном ниже коде

* создается поток push-передачи с помощью `createPushStream()`;
* открывается файл `.wav`, создавая поток чтения, и осуществляется его запись в поток push-передачи;
* создается аудио-конфигурация с помощью потока push-передачи.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

При использовании потока push-передачи в качестве входных данных предполагается, что звуковые данные являются необработанным PCM (пропуская все заголовки).
API будет по-прежнему работать в некоторых случаях, если заголовок не пропущен, но для получения наилучших результатов рекомендуется реализовать логику для чтения заголовков, чтобы `fs` начинался с *начала звуковых данных*.

---

## <a name="error-handling"></a>Обработка ошибок

В предыдущих примерах мы просто получили распознанный текст из `result.text`, но чтобы обработать ошибки и другие ответы, необходимо написать код для обработки результата. Следующий код вычисляет свойство [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?preserve-view=true&view=azure-node-latest#reason) и:

* Выводит результат распознавания: `ResultReason.RecognizedSpeech`
* Если совпадений нет, сообщает об этом пользователю: `ResultReason.NoMatch`
* В случае ошибки выводит сообщение об ошибке: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>Непрерывное распознавание

В предыдущих примерах используется одноразовое распознавание, которое распознает один речевой фрагмент. Конец одного речевого фрагмента определяется путем прослушивания до тишины в конце, или пока не будет обработано максимум 15 секунд аудио.

Непрерывное распознавание функционирует иначе. Оно используется, если требуется **контролировать**, когда следует остановить распознавание. Для получения результатов распознавания необходимо подписаться на события `Recognizing`, `Recognized`и `Canceled`. Чтобы отключить распознавание, необходимо вызвать [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#stopcontinuousrecognitionasync). Ниже приведен пример того, как выполняется непрерывное распознавание входного аудиофайла.

Начните с определения входных данных и инициализации [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest):

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig);
```

Теперь подпишитесь на события, отправленные из [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest).

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#recognizing): Сигнал для событий, содержащих промежуточные результаты распознавания.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#recognized): Сигнал для событий, содержащих окончательные результаты распознавания (указывает на успешность попытки распознавания).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#sessionstopped): Сигнал для событий, указывающих на окончание распознавания (операция).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#canceled): Сигнал для событий, содержащих отмененные результаты распознавания (показывающий попытку распознавания, которая была отменена в результате ошибки распознавания или при прямом запросе об отмене, в случае сбоя транспортировки или протоколирования).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Завершив настройку, вызовите [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#startcontinuousrecognitionasync), чтобы начать распознавание.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Режим диктовки

При использовании непрерывного распознавания вы можете включить обработку диктовки с помощью соответствующей функции "Включить диктовку". Этот режим приведет к тому, что экземпляр конфигурации речи будет интерпретировать словесные описания структуры предложения, например, пунктуацию. Например, речевой фрагмент "Живете ли вы в городе вопросительный знак" будет интерпретироваться как текст "Живете ли вы в городе?".

Чтобы включить режим диктовки, используйте метод [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest#enabledictation--) в [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest).

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Изменение исходного языка

Распространенной задачей распознавания речи является указание языка ввода (или исходного языка). Давайте посмотрим, как изменить язык ввода на итальянский. В коде найдите [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest), а затем добавьте следующую строку непосредственно под ней.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

Свойство [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest#speechrecognitionlanguage) принимает строку формата языкового стандарта. Вы можете указать любое значение в столбце **Языковой стандарт** в списке поддерживаемых [языковых стандартов/языков](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Улучшение точности распознавания

Существует несколько способов улучшить точность распознавания с помощью службы "Речь". Давайте взглянем на списки фраз. Списки фраз используются для определения известных фраз в звуковых данных, например имени пользователя или определенного местоположения. В список фраз можно добавить отдельные слова или полные фразы. Если в аудиофайл включено точное совпадение всей фразы, во время распознавания в списке фраз используется запись. Если точное совпадение с фразой не найдено, распознавание не происходит.

> [!IMPORTANT]
> Функция "Список фраз" доступна только на английском языке.

Чтобы использовать список фраз, сначала создайте объект [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest), а затем добавьте определенные слова и фразы с помощью [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest#addphrase-string-).

Любые изменения [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest) вступают в силу при следующем распознавании или после повторного подключения к службе "Речь".

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Если необходимо очистить список фраз, выполните следующие действия.

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Другие опции для повышения точности распознавания

Списки фраз — это только один вариант для повышения точности распознавания. Кроме того, вы можете сделать следующее: 

* [Повышение точности с помощью пользовательского распознавания речи](../../../how-to-custom-speech.md)
* [Повышение точности с помощью моделей клиентов](../../../tutorial-tenant-model.md)