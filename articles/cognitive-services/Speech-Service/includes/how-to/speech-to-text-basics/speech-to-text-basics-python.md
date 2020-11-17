---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 44980977f366bc40ceff9c7b5751d5657c79ccc2
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94482608"
---
Одной из основных функций службы "Речь" является распознавание и преобразование человеческой речи (часто это называется преобразованием речи в текст). Из этого краткого руководство вы узнаете, как использовать пакет SDK для службы "Речь" в приложениях и продуктах для выполнения высококачественного преобразования речи в текст.

## <a name="skip-to-samples-on-github"></a>Примеры на GitHub

Если вы хотите сразу перейти к примерам кода, см. [этот репозиторий](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python) на сайте GitHub.

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что:

* У вас есть учетная запись Azure и подписка на службу "Речь". Если у вас нет учетной записи и подписки — [попробуйте службу "Речь" бесплатно](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-and-import-the-speech-sdk"></a>Установка и импорт пакета SDK для службы "Речь"

Прежде чем выполнять какие-либо действия, необходимо установить пакет SDK для службы "Речь".

```Python
pip install azure-cognitiveservices-speech
```

Если вы работаете в macOS и сталкиваетесь с проблемами при установке, вам может понадобиться сначала запустить эту команду.

```Python
python3 -m pip install --upgrade pip
```

После установки пакета SDK для службы "Речь" импортируйте его в свой проект Python.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>Создание конфигурации службы "Речь"

Чтобы вызвать службу "Речь" с помощью пакета SDK для службы "Речь", необходимо создать [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python). Этот класс содержит сведения о вашей подписке, такие как ключ и связанный регион, конечная точка, узел или маркер авторизации. Создайте [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) с помощью ключа и региона. Сведения о том, как найти пару "ключ-регион", см. в разделе [Поиск ключей и региона](../../../overview.md#find-keys-and-region).

```Python
speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
```

Существует несколько других способов инициализации [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python):

* С помощью конечной точки: передайте конечную точку службы "Речь". Ключ или маркер авторизации являются необязательными.
* С помощью узла: передайте адрес узла. Ключ или маркер авторизации являются необязательными.
* С помощью маркера авторизации: передайте маркер авторизации и связанный регион.

> [!NOTE]
> Независимо от того, используете ли вы распознавание речи, синтез речи, перевод или распознавание намерения, вы всегда создаете конфигурацию.

## <a name="recognize-from-microphone"></a>Распознавание речи с микрофона

Чтобы распознать речь с микрофона устройства, просто создайте `SpeechRecognizer`, не передавая `AudioConfig`, и передайте свой `speech_config`.

```Python
import azure.cognitiveservices.speech as speechsdk

def from_mic():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
    
    print("Speak into your microphone.")
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_mic()
```

Если необходимо использовать *конкретное* входное аудиоустройство, необходимо указать код устройства в `AudioConfig` и передать его в параметр `audio_config` конструктора `SpeechRecognizer`. Узнайте, [как получить код устройства](../../../how-to-select-audio-input-devices.md) для входного аудиоустройства.

## <a name="recognize-from-file"></a>Распознавание речи из файла

Чтобы распознавать речь из аудиофайла, а не с помощью микрофона, создайте [`AudioConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?preserve-view=true&view=azure-python) и используйте параметр `filename`.

```Python
import azure.cognitiveservices.speech as speechsdk

def from_file():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
    audio_input = speechsdk.AudioConfig(filename="your_file_name.wav")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
    
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_file()
```

## <a name="error-handling"></a>Обработка ошибок

В предыдущих примерах мы просто получили распознанный текст из `result.text`, но чтобы обработать ошибки и другие ответы, необходимо написать код для обработки результата. Следующий код вычисляет свойство [`result.reason`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python) и:

* Выводит результат распознавания: `speechsdk.ResultReason.RecognizedSpeech`
* Если совпадений нет, сообщает об этом пользователю: `speechsdk.ResultReason.NoMatch `
* В случае ошибки выводит сообщение об ошибке: `speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

## <a name="continuous-recognition"></a>Непрерывное распознавание

В предыдущих примерах используется одноразовое распознавание, которое распознает один речевой фрагмент. Конец одного речевого фрагмента определяется путем прослушивания до тишины в конце, или пока не будет обработано максимум 15 секунд аудио.

Непрерывное распознавание функционирует иначе. Оно используется, если требуется **контролировать**, когда следует остановить распознавание. Для получения результатов распознавания необходимо подключиться к `EventSignal`, а для остановки распознавания необходимо вызвать [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) или [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--). Ниже приведен пример того, как выполняется непрерывное распознавание входного аудиофайла.

Начнем с определения входных данных и инициализации [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?preserve-view=true&view=azure-python):

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Теперь создадим переменную для управления состоянием распознавания речи. Для начала мы зададим это значение `False`, так как в начале распознавания мы можем смело предположить, что он не завершен.

```Python
done = False
```

Теперь создадим обратный вызов, чтобы предотвратить непрерывное распознавание при получении `evt`. Необходимо помнить о некоторых вещах.

* При получении `evt` выводится сообщение `evt`.
* После получения `evt` для отмены распознавания вызывается [stop_continuous_recognition ()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--).
* Состояние распознавания изменится на `True`.

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

В этом примере кода показано, как соединять обратные вызовы с событиями, отправленными из [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--).

* [`recognizing`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): Сигнал для событий, содержащих промежуточные результаты распознавания.
* [`recognized`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): Сигнал для событий, содержащих окончательные результаты распознавания (указывает на успешность попытки распознавания).
* [`session_started`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): Сигнал для событий, указывающих на начало распознавания (операция).
* [`session_stopped`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): Сигнал для событий, указывающих на окончание распознавания (операция).
* [`canceled`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): Сигнал для событий, содержащих отмененные результаты распознавания (показывающий попытку распознавания, которая была отменена в результате ошибки распознавания или при прямом запросе об отмене, в случае сбоя транспортировки или протоколирования).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Если все настроено, можно вызвать [start_continuous_recognition ()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped).

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Режим диктовки

При использовании непрерывного распознавания вы можете включить обработку диктовки с помощью соответствующей функции "Включить диктовку". Этот режим приведет к тому, что экземпляр конфигурации речи будет интерпретировать словесные описания структуры предложения, например, пунктуацию. Например, речевой фрагмент "Живете ли вы в городе вопросительный знак" будет интерпретироваться как текст "Живете ли вы в городе?".

Чтобы включить режим диктовки, используйте метод [`enable_dictation()`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) в [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python).

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Изменение исходного языка

Распространенной задачей распознавания речи является указание языка ввода (или исходного языка). Давайте посмотрим, как изменить язык ввода на немецкий. В коде найдите SpeechConfig, а затем добавьте следующую строку непосредственно под ней.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language) — это параметр, принимающий строку в качестве аргумента. Вы можете указать любое значение в списке поддерживаемых [языковых стандартов/языков](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Улучшение точности распознавания

Существует несколько способов улучшить точность распознавания с помощью пакета SDK службы "Речь". Давайте взглянем на списки фраз. Списки фраз используются для определения известных фраз в звуковых данных, например имени пользователя или определенного местоположения. В список фраз можно добавить отдельные слова или полные фразы. Если в аудиофайл включено точное совпадение всей фразы, во время распознавания в списке фраз используется запись. Если точное совпадение с фразой не найдено, распознавание не происходит.

> [!IMPORTANT]
> Функция "Список фраз" доступна только на английском языке.

Чтобы использовать список фраз, сначала создайте объект [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-python), а затем добавьте определенные слова и фразы с помощью [`addPhrase`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-).

Любые изменения [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-python) вступают в силу при следующем распознавании или после повторного подключения к службе "Речь".

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

Если необходимо очистить список фраз, выполните следующие действия. 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Другие опции для повышения точности распознавания

Списки фраз — это только один вариант для повышения точности распознавания. Кроме того, вы можете сделать следующее: 

* [Повышение точности с помощью пользовательского распознавания речи](../../../how-to-custom-speech.md)
* [Повышение точности с помощью моделей клиентов](../../../tutorial-tenant-model.md)