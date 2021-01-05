---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: 786f9587ab223cf87a48cd791f366049b94af59b
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97866029"
---
Сначала загрузите файл модели ключевых слов с помощью `FromFile()` статической функции, которая возвращает `KeywordRecognitionModel` . Используйте путь к файлу, `.table` скачанному из Speech Studio. Кроме того, вы создаете `AudioConfig` с помощью микрофона по умолчанию, создаете новый экземпляр `KeywordRecognizer` с помощью конфигурации аудио.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Затем выполнение распознавания ключевых слов выполняется с помощью одного вызова `RecognizeOnceAsync()` , передавая объект модели. Запустится сеанс распознавания ключевых слов, который продолжается до тех пор, пока не будет распознано ключевое слово. Таким образом, этот шаблон разработки обычно используется в многопоточных приложениях или в случаях использования, когда вы можете ожидать пробуждения по неопределенному времени.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> В приведенном здесь примере используется распознавание локального ключевого слова, так как `SpeechConfig` для контекста проверки подлинности не требуется объект и не выполняется обращение к серверной части. Однако можно выполнить как распознавание ключевых слов, так и проверку, [используя прямое подключение к внутреннему интерфейсу](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword).

### <a name="continuous-recognition"></a>Непрерывное распознавание

Другие классы в пакете SDK для распознавания речи поддерживают непрерывное распознавание (для распознавания речи и намерений) с помощью распознавания ключевых слов. Это позволяет использовать тот же код, который обычно используется для непрерывного распознавания, с возможностью ссылаться на `.table` файл для модели ключевых слов.

Для преобразования речи в текст Используйте тот же шаблон проектирования, что и в [кратком руководстве](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started-speech-to-text?tabs=script%2Cbrowser%2Cwindowsinstall&pivots=programming-language-csharp#continuous-recognition) , чтобы настроить непрерывное распознавание. Затем замените вызов на `recognizer.StartContinuousRecognitionAsync()` `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` и передайте `KeywordRecognitionModel` объект. Чтобы предотвратить постоянное распознавание с помощью ключевых слов, используйте `recognizer.StopKeywordRecognitionAsync()` вместо `recognizer.StopContinuousRecognitionAsync()` .

При распознавании намерения используется идентичный шаблон [`StartKeywordRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) с [`StopKeywordRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) функциями и.