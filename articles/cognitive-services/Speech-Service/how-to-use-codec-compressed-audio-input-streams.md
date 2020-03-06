---
title: Потоковая передача сжатого аудио-кодека с помощью речевого пакета SDK — служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как выполнять потоковую передачу сжатых аудио в службу речи с помощью речевого пакета SDK. Доступно для C++, C#и Java для Linux, Java в Android и цели-C в iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 2b530da06b02091ce66ff7c116f3e17ddcc22497
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331116"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Использование сжатых звуковых данных кодеков с помощью пакета SDK для распознавания речи

Сжатый аудио-API-интерфейс пакета речевого **ввода** предоставляет способ потоковой передачи сжатого звука в службу распознавания речи с помощью Пуллстреам или пушстреам.

> [!IMPORTANT]
> В настоящее время поддерживается потоковая передача сжатых аудио-данных для C++, C#и Java в Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). Она также поддерживается для [Java в Android](how-to-use-codec-compressed-audio-input-streams-android.md) и [цели-C на платформе iOS](how-to-use-codec-compressed-audio-input-streams-ios.md) .
> Требуется пакет SDK для распознавания речи версии 1.7.0 или выше (версия 1.10.0 или более поздняя для RHEL 8, CentOS 8).

Сведения о WAV/PCM см. в документации по магистрали.  За пределами WAV/PCM поддерживаются следующие сжатые форматы входных данных кодеков:

- MP3
- ОПУС/OGG
- FLAC
- АЛАВ в контейнере WAV
- МУЛАВ в контейнере WAV

## <a name="prerequisites"></a>предварительные требования

Обработка сжатого аудио-сигнала реализуется с помощью [гстреамер](https://gstreamer.freedesktop.org). Для причины лицензирования двоичные файлы Гстреамер не компилируются и не связываются с голосовыми пакетами SDK. Поэтому для использования сжатого звукового звука разработчику приложения необходимо установить следующие данные: 18,04, 16,04 и Debian 9.

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

В RHEL/CentOS 8:

```sh
sudo yum install gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

> [!NOTE]
> В RHEL/CentOS 8 следуйте инструкциям по [настройке OpenSSL для Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="example-code-using-codec-compressed-audio-input"></a>Пример кода, использующий сжатые звуковые входные кодеки

Чтобы выполнить потоковую передачу в сжатом аудио формате в службу распознавания речи, создайте `PullAudioInputStream` или `PushAudioInputStream`. Затем создайте `AudioConfig` из экземпляра класса Stream, указав формат сжатия потока.

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

- [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
* [Узнайте, как распознать речь в Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
