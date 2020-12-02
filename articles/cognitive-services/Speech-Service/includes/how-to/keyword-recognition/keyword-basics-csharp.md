---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: d71a7f6451cda5e2e50b5410140ac88361bf1735
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509456"
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
> В приведенном здесь примере используется распознавание локального ключевого слова, так как `SpeechConfig` для контекста проверки подлинности не требуется объект и не выполняется обращение к серверной части. Тем не менее можно выполнить как распознавание ключевых слов, так и проверку [с помощью непрерывного подключения к внутреннему серверной](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword)части.