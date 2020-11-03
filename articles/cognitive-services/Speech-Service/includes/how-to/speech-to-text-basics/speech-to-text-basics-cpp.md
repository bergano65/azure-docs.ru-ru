---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: trbye
ms.openlocfilehash: 7575e174f1f47d55c507fdbf0386fbd578649839
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92499102"
---
Одной из основных функций службы "Речь" является распознавание и преобразование человеческой речи (часто это называется преобразованием речи в текст). Из этого краткого руководство вы узнаете, как использовать пакет SDK для службы "Речь" в приложениях и продуктах для выполнения высококачественного преобразования речи в текст.

## <a name="skip-to-samples-on-github"></a>Примеры на GitHub

Если вы хотите сразу перейти к примерам кода, см. [этот репозиторий](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) на сайте GitHub.

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас есть учетная запись Azure и подписка на службу "Речь". Если у вас нет учетной записи и подписки, [попробуйте службу "Речь" бесплатно](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

Прежде чем выполнять какие-либо действия, необходимо установить пакет SDK для службы "Речь". В зависимости от используемой платформы выполните следующие действия:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Создание конфигурации службы "Речь"

Чтобы вызвать службу "Речь" с помощью пакета SDK для службы "Речь", необходимо создать [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig). Этот класс содержит сведения о вашей подписке, такие как ключ и связанный регион, конечная точка, узел или маркер авторизации. Создайте [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) с помощью ключа и региона. Идентификатор региона можно узнать в разделе о [поддержке регионов](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk).

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;

auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Существует несколько других способов инициализации [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig):

* С помощью конечной точки: передайте конечную точку службы "Речь". Ключ или маркер авторизации являются необязательными.
* С помощью узла: передайте адрес узла. Ключ или маркер авторизации являются необязательными.
* С помощью маркера авторизации: передайте маркер авторизации и связанный регион.

> [!NOTE]
> Независимо от того, используете ли вы распознавание речи, синтез речи, перевод или распознавание намерения, вы всегда создаете конфигурацию.

## <a name="recognize-from-microphone"></a>Распознавание речи с микрофона

Чтобы распознать речь с микрофона устройства, создайте `AudioConfig` с помощью `FromDefaultMicrophoneInput()`. Затем инициализируйте [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer), передав `audioConfig` и `config`.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);

cout << "Speak into your microphone." << std::endl;
auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

Если необходимо использовать *конкретное* входное аудиоустройство, необходимо указать код устройства в `AudioConfig`. Узнайте, [как получить код устройства](../../../how-to-select-audio-input-devices.md) для входного аудиоустройства.

## <a name="recognize-from-file"></a>Распознавание речи из файла

Чтобы распознавать речь из аудиофайла, а не с помощью микрофона, вам все равно нужно создать `AudioConfig`. Но при создании [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) вместо вызова `FromDefaultMicrophoneInput()` необходимо вызвать `FromWavFileInput()` и передать путь к файлу.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);

auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

## <a name="recognize-speech"></a>Распознавание речи

[Класс распознавателя](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) для пакета SDK службы "Речь" для C++ предоставляет несколько методов, которые можно использовать для распознавания речи.

### <a name="single-shot-recognition"></a>Одноразовое распознавание

Под одноразовым распознаванием подразумевается асинхронное распознавание отдельного речевого фрагмента. Конец одного речевого фрагмента определяется путем прослушивания до тишины в конце, или пока не будет обработано максимум 15 секунд аудио. Ниже приведен пример асинхронного одноразового распознавания с помощью [`RecognizeOnceAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync):

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

Вам понадобиться написать код, чтобы вычислить результат. В этом примере вычисляется [`result->Reason`](https://docs.microsoft.com/cpp/cognitive-services/speech/recognitionresult#reason):

* Выводит результат распознавания: `ResultReason::RecognizedSpeech`
* Если совпадений нет, сообщает об этом пользователю: `ResultReason::NoMatch`
* В случае ошибки выводит сообщение об ошибке: `ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>Непрерывное распознавание

Непрерывное распознавание является более сложным, чем одноразовое. Для получения результатов распознавания необходимо подписаться на события `Recognizing`, `Recognized`и `Canceled`. Чтобы отключить распознавание, необходимо вызвать [StopContinuousRecognitionAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync). Ниже приведен пример того, как выполняется непрерывное распознавание входного аудиофайла.

Начнем с определения входных данных и инициализации [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer):

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Теперь создадим переменную для управления состоянием распознавания речи. Для начала мы объявим `promise<void>`, так как в начале распознавания можно смело предположить, что оно не закончено.

```cpp
promise<void> recognitionEnd;
```

Мы будем подписываться на события, отправленные из [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer).

* [`Recognizing`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizing): Сигнал для событий, содержащих промежуточные результаты распознавания.
* [`Recognized`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognized): Сигнал для событий, содержащих окончательные результаты распознавания (указывает на успешность попытки распознавания).
* [`SessionStopped`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): Сигнал для событий, указывающих на окончание распознавания (операция).
* [`Canceled`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#canceled): Сигнал для событий, содержащих отмененные результаты распознавания (показывающий попытку распознавания, которая была отменена в результате ошибки распознавания или при прямом запросе об отмене, в случае сбоя транспортировки или протоколирования).

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

Если все настроено, можно вызвать [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync).

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Режим диктовки

При использовании непрерывного распознавания вы можете включить обработку диктовки с помощью соответствующей функции "Включить диктовку". Этот режим приведет к тому, что экземпляр конфигурации речи будет интерпретировать словесные описания структуры предложения, например, пунктуацию. Например, речевой фрагмент "Живете ли вы в городе вопросительный знак" будет интерпретироваться как текст "Живете ли вы в городе?".

Чтобы включить режим диктовки, используйте метод [`EnableDictation`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation) в [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Изменение исходного языка

Распространенной задачей распознавания речи является указание языка ввода (или исходного языка). Давайте посмотрим, как изменить язык ввода на немецкий. В коде найдите [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig), а затем добавьте следующую строку непосредственно под ней.

```cpp
config->SetSpeechRecognitionLanguage("de-DE");
```

[`SetSpeechRecognitionLanguage`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage) — это параметр, принимающий строку в качестве аргумента. Вы можете указать любое значение в списке поддерживаемых [языковых стандартов/языков](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Улучшение точности распознавания

Существует несколько способов улучшить точность распознавания с помощью пакета SDK службы "Речь". Давайте взглянем на списки фраз. Списки фраз используются для определения известных фраз в звуковых данных, например имени пользователя или определенного местоположения. В список фраз можно добавить отдельные слова или полные фразы. Если в аудиофайл включено точное совпадение всей фразы, во время распознавания в списке фраз используется запись. Если точное совпадение с фразой не найдено, распознавание не происходит.

> [!IMPORTANT]
> Функция "Список фраз" доступна только на английском языке.

Чтобы использовать список фраз, сначала создайте объект [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar), а затем добавьте определенные слова и фразы с помощью [`AddPhrase`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar#addphrase).

Любые изменения [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) вступают в силу при следующем распознавании или после повторного подключения к службе "Речь".

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Если необходимо очистить список фраз, выполните следующие действия. 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Другие опции для повышения точности распознавания

Списки фраз — это только один вариант для повышения точности распознавания. Кроме того, вы можете сделать следующее: 

* [Повышение точности с помощью пользовательского распознавания речи](../../../how-to-custom-speech.md)
* [Повышение точности с помощью моделей клиентов](../../../tutorial-tenant-model.md)
