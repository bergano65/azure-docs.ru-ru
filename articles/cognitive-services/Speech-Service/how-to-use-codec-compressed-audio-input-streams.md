---
title: Потоковая передача сжатого аудио-кодека с помощью речевого пакета SDK — служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как выполнять потоковую передачу сжатых аудио в службы речевого обучения Azure с помощью речевого пакета SDK. Доступно для C++, C#и Java для Linux, Java в Android и цели-C в iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 668964c597b8d748220cbeec68e0ba68300cb406
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464347"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Использование сжатых звуковых данных кодеков с помощью пакета SDK для распознавания речи

Сжатый аудио-API-интерфейс пакета речевого **ввода** предоставляет способ потоковой передачи сжатого звука в службу распознавания речи с помощью Пуллстреам или пушстреам.

> [!IMPORTANT]
> В настоящее время поддерживается потоковая передача сжатых аудио-данных в C++, C#и Java в Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9). Она также поддерживается для [Java в Android](how-to-use-codec-compressed-audio-input-streams-android.md) и [цели-C на платформе iOS](how-to-use-codec-compressed-audio-input-streams-ios.md) .
> Требуется пакет SDK для распознавания речи версии 1.7.0 или выше.

Сведения о WAV/PCM см. в документации по магистрали.  За пределами WAV/PCM поддерживаются следующие сжатые форматы входных данных кодеков:

- MP3
- ОПУС/OGG
- FLAC
- АЛАВ в контейнере WAV
- МУЛАВ в контейнере WAV

## <a name="prerequisites"></a>Технические условия

Обработка сжатого аудио-сигнала реализуется с помощью [гстреамер](https://gstreamer.freedesktop.org). Для причины лицензирования двоичные файлы Гстреамер не компилируются и не связываются с голосовыми пакетами SDK. Поэтому для использования сжатого звукового звука разработчику приложения необходимо установить следующие данные: 18,04, 16,04 и Debian 9.

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Пример кода, использующий сжатые звуковые входные кодеки

Чтобы выполнить потоковую передачу в сжатом аудио формате в речевые службы, создайте `PullAudioInputStream` или `PushAudioInputStream`. Затем создайте `AudioConfig` из экземпляра класса Stream, указав формат сжатия потока.

Предположим, что у вас есть класс входного потока с именем `myPushStream` и опус/OGG. Код может выглядеть следующим образом:

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

- [Получение пробной подписки на службу "Речь"](https://azure.microsoft.com/try/cognitive-services/)
* [Узнайте, как распознать речь в Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
