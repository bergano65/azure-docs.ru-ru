---
title: Потоковая передача сжатого аудио-кодека с помощью речевого пакета SDK — служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как выполнять потоковую передачу сжатых аудио в службы речевого обучения Azure с помощью речевого пакета SDK. Доступно для C++, C#и Java для Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: b29b42dea9522526d49c1bda017a522855946def
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559549"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Использование сжатых звуковых данных кодеков с помощью пакета SDK для распознавания речи

Сжатый аудио-API-интерфейс пакета речевого **ввода** предоставляет способ потоковой передачи сжатого звука в службу распознавания речи с помощью Пуллстреам или пушстреам.

> [!IMPORTANT]
> Потоковая передача сжатого звука поддерживается C++только C#для, и Java в Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9).
> Требуется пакет SDK для распознавания речи версии 1.4.0 или выше.

Сведения о WAV/PCM см. в документации по магистрали.  За пределами WAV/PCM поддерживаются следующие сжатые форматы входных данных кодеков:

- MP3
- ОПУС/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>Предварительные требования для использования сжатых звуковых входов кодеков

Установите эти дополнительные зависимости, чтобы использовать сжатый речевой ввод с помощью пакета SDK для для Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Пример кода, использующий сжатые звуковые входные кодеки

Чтобы выполнить потоковую передачу в сжатом аудио формате в службы речи `PullAudioInputStream` , `PushAudioInputStream`Создайте или. Затем создайте объект `AudioConfig` из экземпляра класса Stream, указав формат сжатия потока.

Предположим, что у вас есть класс входного потока с `myPushStream` именем и используется опус/OGG. Код может выглядеть следующим образом:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>Следующие шаги

- [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
- [Распознавание речи в C#](quickstart-csharp-dotnet-windows.md)
