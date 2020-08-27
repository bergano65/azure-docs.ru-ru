---
title: Выбор режима распознавания речи с помощью речевого пакета SDK
titleSuffix: Azure Cognitive Services
description: Узнайте, как выбрать оптимальный режим распознавания при использовании речевого пакета SDK.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two-with-js
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 29dcd3ddc32ac92f7543e5d7648680e36f50dcfb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934605"
---
# <a name="choose-a-speech-recognition-mode"></a>Выбор режима распознавания речи

При рассмотрении операций распознавания речи в текст [пакет SDK](speech-sdk.md) для распознавания речи предоставляет несколько режимов обработки речи. По сути, иногда называется *режимом распознавания*. В этой статье сравниваются различные режимы распознавания.

## <a name="recognize-once"></a>Распознать один раз

Если вы хотите обрабатывать каждый utterance по одному "предложению", используйте функцию "распознать один раз". Этот метод обнаруживает распознанный utterance из входных данных, начиная с начала обнаруженного речи, до следующей паузы. Как правило, пауза обозначает конец предложения или линейного мышления.

В конце одного распознанного utterance служба останавливает обработку звука из этого запроса. Максимальный предел для распознавания — это длительность предложения в 20 секунд.

::: zone pivot="programming-language-csharp"

Дополнительные сведения об использовании `RecognizeOnceAsync` функции см. в документации по [пакету SDK для .NET для распознавания речи](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Дополнительные сведения об использовании `RecognizeOnceAsync` функции см. в документации по [C++ Speech SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync).

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Дополнительные сведения об использовании `recognizeOnceAsync` функции см. в документации по [Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable).

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Дополнительные сведения об использовании `recognize_once` функции см. в документации по [речевому SDK Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult).

```python
result = speech_recognizer.recognize_once()
```

::: zone-end

::: zone pivot="programming-language-javascript"

Дополнительные сведения об использовании `recognizeOnceAsync` функции см. в документации по [РЕЧЕВому пакету SDK для JavaScript](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-).

```JavaScript
recognizer.recognizeOnceAsync((result)=>{}, (error)=>{}));
```

::: zone-end

::: zone pivot="programming-language-more"

Дополнительные сведения о других языках см. в [справочных документах по пакету SDK для распознавания речи](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="continuous"></a>С задержкой

Если требуется долгосрочное распознавание, используйте функцию запуска и соответствующие функции для непрерывного распознавания. Функция start запустится и продолжит обработку всех фразы продолжительностью до тех пор, пока не будет вызвана функция завершения или пока не пройдет слишком много времени в тишине. При использовании непрерывного режима обязательно зарегистрируйтесь на различные события, которые будут срабатывать при возникновении. Например, при распознавании речи срабатывает событие «распознано». Для обработки распознавания необходимо наличие обработчика событий.

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync(()=>{}, (error)=>{});

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync(()=>{}, (error)=>{});
```

::: zone-end

::: zone pivot="programming-language-more"

Дополнительные сведения о других языках см. в [справочных документах по пакету SDK для распознавания речи](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="dictation"></a>Диктовка

При использовании непрерывного распознавания вы можете включить обработку диктовки с помощью соответствующей функции "Включить диктовку". Этот режим приведет к тому, что экземпляр конфигурации речи будет интерпретировать словесные описания структуры предложения, например, пунктуацию. Например, речевой фрагмент "Живете ли вы в городе вопросительный знак" будет интерпретироваться как текст "Живете ли вы в городе?".

::: zone pivot="programming-language-csharp"

Дополнительные сведения об использовании `EnableDictation` функции см. в документации по [пакету SDK для .NET для распознавания речи](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Дополнительные сведения об использовании `EnableDictation` функции см. в документации по [C++ Speech SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation).

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Дополнительные сведения об использовании `enableDictation` функции см. в документации по [Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable).

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Дополнительные сведения об использовании `enable_dictation` функции см. в документации по [речевому SDK Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--).

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end

::: zone pivot="programming-language-javascript"

Дополнительные сведения об использовании `enableDictation` функции см. в документации по [РЕЧЕВому пакету SDK для JavaScript](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--).

```JavaScript
// Enable diction
speechConfig.enableDictation();
```

::: zone-end

::: zone pivot="programming-language-more"

Дополнительные сведения о других языках см. в [справочных документах по пакету SDK для распознавания речи](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Ознакомьтесь с дополнительными примерами пакета SDK для речи на сайте GitHub](https://aka.ms/csspeech/samples)
