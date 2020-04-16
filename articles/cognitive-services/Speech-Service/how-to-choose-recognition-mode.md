---
title: Выберите режим распознавания речи с помощью речи SDK
titleSuffix: Azure Cognitive Services
description: Узнайте, как выбрать оптимальный режим распознавания при использовании Speech SDK.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5fdca371e9188ef69068ddbcaa416cbb2b44054c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402157"
---
# <a name="choose-a-speech-recognition-mode"></a>Выберите режим распознавания речи

При рассмотрении операций распознавания речи к [тексту, Speech SDK](speech-sdk.md) предоставляет несколько режимов для обработки речи. Концептуально, иногда называют *режимраспознавания*. В этой статье сравниваются различные режимы распознавания.

## <a name="recognize-once"></a>Признать один раз

Если вы хотите обработать каждое высказывание по одному "приговору" за раз, используйте функцию "признать один раз". Этот метод будет обнаруживать распознаваемые высказывания от ввода, начиная с начала обнаруженной речи до следующей паузы. Обычно пауза означает окончание предложения или строки мысли.

В конце одного признанного высказывания служба прекращает обработку звука из этого запроса. Максимальным пределением распознавания является продолжительность предложения 20 секунд.

::: zone pivot="programming-language-csharp"

Для получения дополнительной `RecognizeOnceAsync` информации об [.NET Speech SDK docs](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync)использовании функции см.

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Для получения более `RecognizeOnceAsync` подробной информации об использовании функции см. [C++ Speech SDK docs](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync)

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Для получения дополнительной `recognizeOnceAsync` информации об [Java Speech SDK docs](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable)использовании функции см.

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Для получения дополнительной `recognize_once` информации об [Python Speech SDK docs](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult)использовании функции см.

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

Для дополнительных языков [Speech SDK reference docs](speech-to-text.md#speech-sdk-reference-docs)см.

::: zone-end

## <a name="continuous"></a>Непрерывные

Если вам требуется длительное распознавание, используйте стартовые и соответствующие стоп-функции для непрерывного распознавания. Функция запуска начнется и продолжит обработку всех высказываний до тех пор, пока вы не наймете функцию остановки, или пока не пройдет слишком много времени в тишине. При использовании непрерывного режима, не забудьте зарегистрироваться на различные события, которые будут стрелять при возникновении. Например, "признанное" событие сравнивает при распознавании речи. Для обработки распознавания необходимо иметь обработчик событий.

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
::: zone pivot="programming-language-more"

Для дополнительных языков [Speech SDK reference docs](speech-to-text.md#speech-sdk-reference-docs)см.

::: zone-end

## <a name="dictation"></a>Диктовка

При использовании непрерывного распознавания вы можете включить обработку диктовки с помощью соответствующей функции "Включить диктовку". Этот режим приведет к тому, что экземпляр конфигурации речи будет интерпретировать словесные описания структуры предложения, например, пунктуацию. Например, речевой фрагмент "Живете ли вы в городе вопросительный знак" будет интерпретироваться как текст "Живете ли вы в городе?".

::: zone pivot="programming-language-csharp"

Для получения дополнительной `EnableDictation` информации об [.NET Speech SDK docs](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation)использовании функции см.

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Для получения более `EnableDictation` подробной информации об использовании функции см. [C++ Speech SDK docs](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation)

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Для получения дополнительной `enableDictation` информации об [Java Speech SDK docs](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable)использовании функции см.

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Для получения дополнительной `enable_dictation` информации об [Python Speech SDK docs](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--)использовании функции см.

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

Для дополнительных языков [Speech SDK reference docs](speech-to-text.md#speech-sdk-reference-docs)см.

::: zone-end

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Изучите дополнительные образцы речи SDK на GitHub](https://aka.ms/csspeech/samples)
