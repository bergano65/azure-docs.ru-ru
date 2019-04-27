---
title: Stream сжатые аудио с Speech SDK - службы распознавания речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как выполнять потоковую передачу сжатые аудио для служб Azure речи с помощью пакета SDK для распознавания речи. Для C++, C#и Java для Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 2066dc3e20ab9fc92b23fd071728ea6a920d3324
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60697687"
---
# <a name="stream-compressed-audio-with-the-speech-sdk"></a>Stream сжатые аудио с пакетом SDK для распознавания речи

Пакет SDK для распознавания речи **сжатые аудио входных данных Stream** API позволяет выполнять потоковую передачу сжатые аудио, чтобы служба распознавания речи с помощью PullStream или PushStream.

> [!IMPORTANT]
> Потоковая передача сжатые аудио поддерживается только для C++, C#и Java в Linux (Ubuntu 16.04 или Ubuntu 18.04).
> Поддержка ограничена MP3 и ТРУДЕ/OGG.

## <a name="prerequisites"></a>Технические условия

Необходимо установить эти зависимости, чтобы использовать сжатые аудио вход с помощью Speech SDK для Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="streaming-compressed-audio"></a>Сжатые аудио

Для потоковой передачи в сжатом формате аудио в службы распознавания речи, создания `PullAudioInputStream` или `PushAudioInputStream`. Затем создайте `AudioConfig` из экземпляра класса stream, задав формат сжатия потока.

Предположим, что у вас есть класс входного потока с именем `myPushStream` и при использовании ТРУДЕ/OGG. Это код может выглядеть так: 

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

## <a name="next-steps"></a>Дальнейшие действия

* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
* [Распознавание речи в C#](quickstart-csharp-dotnet-windows.md)
