---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.custom: devx-track-js
ms.openlocfilehash: c63b55905ea16c0597e1f8f1179153229018ed32
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470991"
---
Одной из основных функций речевой службы является возможность распознавания речи и перевода их на другие языки. В этом кратком руководстве вы узнаете, как использовать речевой пакет SDK в приложениях и продуктах для выполнения высококачественного перевода речи. В этом кратком руководстве рассматриваются такие темы, как:

* Преобразование речи в текст
* Преобразование речи на несколько целевых языков
* Выполнение прямого перевода речи в речь

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
> Если в качестве платформы вы используете веб-браузер и применяете тег `<script>`, использовать префикс `sdk` не нужно. Префикс `sdk` является псевдонимом для присвоения имени модулю `require`.

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Дополнительные сведения о директиве `import` см. в статье об <a href="https://javascript.info/import-export" target="_blank">экспорте и импорте <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Дополнительные сведения о функции `require` см. <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">здесь <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="create-a-translation-configuration"></a>Создание конфигурации перевода

Чтобы вызвать службу перевода с помощью речевого пакета SDK, необходимо создать [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true) . Этот класс содержит сведения о вашей подписке, такие как ключ и связанный регион, конечная точка, узел или маркер авторизации.

> [!NOTE]
> Независимо от того, используете ли вы распознавание речи, синтез речи, перевод или распознавание намерения, вы всегда создаете конфигурацию.
Существует несколько способов инициализации [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true).

* С помощью подписки: передайте ключ и связанный с ним регион.
* С помощью конечной точки: передайте конечную точку службы "Речь". Ключ или маркер авторизации являются необязательными.
* С помощью узла: передайте адрес узла. Ключ или маркер авторизации являются необязательными.
* С помощью маркера авторизации: передайте маркер авторизации и связанный регион.

Давайте посмотрим, как создается [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true) с помощью ключа и региона. Получите эти учетные данные, выполнив действия, описанные в разделе [пробная служба распознавания речи бесплатно](../../../overview.md#try-the-speech-service-for-free).

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>Инициализация транслятора

После создания [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true), следующим шагом является инициализация [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true). При инициализации [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true)ему необходимо передать `speechTranslationConfig`. Это предоставляет учетные данные, необходимые службе перевода для проверки запроса.

Если речь идет о переводе речи, предоставленных через микрофон по умолчанию для вашего устройства, это [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true) должно быть так:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Если вы хотите указать входное аудиоустройство, необходимо создать [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true) и указать параметр `audioConfig` при инициализации [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true).

> [!TIP]
> [Узнайте, как получить идентификатор устройства для входного аудиоустройства](../../../how-to-select-audio-input-devices.md).
Создайте ссылку на объект `AudioConfig` следующим образом:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

Если вы хотите предоставить звуковой файл, а не использовать микрофон, вам по-прежнему потребуется предоставить `audioConfig`. Но это можно сделать только при разработке для **Node.js** и при создании [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true). Вместо вызова `fromDefaultMicrophoneInput` необходимо вызвать `fromWavFileOutput` и передать параметр `filename`.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>Преобразование текста

[Класс транслатионрекогнизер](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true) для РЕЧЕВОГО пакета SDK для JavaScript предоставляет несколько методов, которые можно использовать для перевода речи.

* Преобразование с одним снимком (Async) — выполняет перевод в неблокирующем (асинхронном) режиме. Это приведет к преобразованию одного utterance. Конец одного речевого фрагмента определяется путем прослушивания до тишины в конце, или пока не будет обработано максимум 15 секунд аудио.
* Непрерывный перевод (асинхронный) — асинхронно инициирует операцию непрерывного перевода. Пользователь регистрируется в событиях и обрабатывает различные состояния приложения. Чтобы отключить асинхронный непрерывный перевод, вызовите [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#stopcontinuousrecognitionasync) .

> [!NOTE]
> Узнайте больше о том, как [выбрать режим распознавания речи](../../../how-to-choose-recognition-mode.md).
### <a name="specify-a-target-language"></a>Укажите целевой язык

Для преобразования необходимо указать как исходный язык, так и хотя бы один целевой язык.
Вы можете выбрать исходный язык, используя языковой стандарт, указанный в [таблице преобразования речи](../../../language-support.md#speech-translation). Найдите параметры для переведенного языка по той же ссылке. Параметры для целевых языков различаются, если вы хотите просмотреть текст или хотите услышать синтезированный переведенный голос. Для перевода с английского на немецкий измените объект конфигурации перевода:

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>Одноразовое распознавание

Ниже приведен пример асинхронного преобразования с одним снимком с помощью [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync) :

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Вам понадобиться написать код, чтобы вычислить результат. В этом примере вычисляется [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult?view=azure-node-latest&preserve-view=true) для перевода на немецкий язык:

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

Код также может обрабатывать обновления, предоставляемые во время обработки перевода.
Эти обновления можно использовать для предоставления визуальной обратной связи о ходе перевода.
Пример кода, в котором показаны обновления, предоставляемые в процессе перевода, см. в [этом примере JavaScript Node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js) . В следующем коде также отображаются сведения, созданные в процессе перевода.

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>Непрерывный перевод

Непрерывный перевод является более сложным, чем распознавание с одним снимком. Для получения результатов распознавания необходимо подписаться на события `recognizing`, `recognized`и `canceled`. Чтобы прерывать перевод, необходимо вызвать [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#stopcontinuousrecognitionasync) . Ниже приведен пример того, как непрерывный перевод выполняется для входного звукового файла.

Начнем с определения входных данных и инициализации [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true):

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Мы будем подписываться на события, отправленные из [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true).

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#recognizing): Сигнал для событий, содержащих промежуточные результаты перевода.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#recognized): Сигнал для событий, содержащих окончательные результаты перевода (указывает на успешную попыток перевода).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#sessionstopped): Сигнал для событий, указывающих на окончание сеанса трансляции (операция).
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#canceled): Сигнал для событий, содержащих отмененные результаты перевода (показывающий попытку перевода, которая была отменена в результате запроса или прямой отмены, или, в случае сбоя транспорта или протокола).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
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

Если все настроено, можно вызвать [`startContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#startcontinuousrecognitionasync).

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## <a name="choose-a-source-language"></a>Выбор исходного языка

Распространенной задачей для перевода речи является указание языка ввода (или исходного кода). Давайте посмотрим, как изменить язык ввода на итальянский. Найдите в коде [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true) , а затем добавьте следующую строку непосредственно под ней.

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

Свойство [`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true#speechrecognitionlanguage) принимает строку формата языкового стандарта. Вы можете указать любое значение в столбце **Языковой стандарт** в списке поддерживаемых [языковых стандартов/языков](../../../language-support.md).

## <a name="choose-one-or-more-target-languages"></a>Выберите один или несколько целевых языков

Пакет SDK для распознавания речи может одновременно преобразовываться на несколько целевых языков. Доступные целевые языки немного отличаются от списка язык исходного языка, и вы указываете целевые языки с помощью кода языка, а не языкового стандарта.
См. список кодов языков для текстовых целей в [таблице перевод речи на странице Поддержка языков](../../../language-support.md#speech-translation). Здесь также можно найти сведения о переводе на синтезированные языки.

Следующий код добавляет немецкий в качестве целевого языка:

```javascript
translationConfig.addTargetLanguage("de");
```

Поскольку можно использовать несколько целевых языковых переводов, код должен указать целевой язык при проверке результата. Следующий код возвращает результаты перевода для немецкого языка.

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```
