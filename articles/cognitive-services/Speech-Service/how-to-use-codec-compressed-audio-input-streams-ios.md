---
title: Потоковая передача сжатого аудио кодека с помощью пакета SDK для распознавания речи в iOS
titleSuffix: Azure Cognitive Services
description: Узнайте, как выполнять потоковую передачу сжатых аудио в службу речи с помощью речевого пакета SDK в iOS.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 2089f4191ddd57fa8dc19862bd195756c166f2d4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805864"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>Как использовать сжатый аудиокодек для речевого ввода с помощью пакета SDK для распознавания речи в iOS

Сжатый аудио-API-интерфейс пакета речевого **ввода** предоставляет способ потоковой передачи сжатого звука в службу распознавания речи с помощью потока опроса или Push-уведомления.

> [!IMPORTANT]
> Для потоковой передачи звука в iOS требуется пакет SDK для речи версии 1.7.0 или выше. Она также поддерживается для [ C++, C#и Java в Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9)](how-to-use-codec-compressed-audio-input-streams.md) и [Java в Android.](how-to-use-codec-compressed-audio-input-streams-android.md)

Сведения о WAV/PCM см. в документации по магистрали. За пределами WAV/PCM поддерживаются следующие сжатые форматы входных данных кодеков:

- MP3
- ОПУС/OGG
- FLAC
- АЛАВ в контейнере WAV
- МУЛАВ в контейнере WAV

## <a name="prerequisites"></a>Технические условия

Обработка сжатого аудио-сигнала реализуется с помощью [гстреамер](https://gstreamer.freedesktop.org). В целях лицензирования эти функции не могут поставляться вместе с пакетом SDK, но библиотека оболочек, содержащая эти функции, должна быть создана разработчиками приложений и поставляется с приложениями с помощью пакета SDK.

Чтобы создать эту библиотеку оболочки, сначала скачайте и установите [пакет SDK для гстреамер](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Затем скачайте проект Xcode для [библиотеки оболочек](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Откройте проект в Xcode и создайте его для **универсального целевого устройства iOS** . он не будет работать, чтобы создать его для определенного целевого объекта.

На этапе сборки создается динамический пакет платформы с динамической библиотекой для всех необходимых архитектур с именем `GStreamerWrapper.framework`.

Эта платформа должна быть включена во все приложения, использующие сжатые звуковые потоки с пакетом SDK для службы распознавания речи.

Для этого примените следующие параметры в проекте Xcode:

1. Скопируйте только что собранный `GStreamerWrapper.framework` и платформу Cognitive Services Speech SDK, которую можно скачать [отсюда](https://aka.ms/csspeech/iosbinary), в каталог, содержащий пример проекта.
1. Измените пути к платформам в _параметрах проекта_.
   1. На вкладке **Общие** в заголовке **внедренные двоичные файлы** добавьте библиотеку SDK в качестве платформы: **Добавить внедренные двоичные файлы** > **добавить другие...** > перейдите к выбранному каталогу и выберите обе платформы.
   1. Перейдите на вкладку **Build Settings** (Параметры сборки) и активируйте **Все** параметры.
1. Добавьте каталог `$(SRCROOT)/..` в _Пути поиска платформы_ под заголовком **Пути поиска**.

## <a name="example-code-using-codec-compressed-audio-input"></a>Пример кода, использующий сжатые звуковые входные кодеки

Чтобы выполнить потоковую передачу в сжатом аудио формате в службу распознавания речи, создайте `SPXPullAudioInputStream` или `SPXPushAudioInputStream`.

В следующем фрагменте кода показано, как создать `SPXAudioConfiguration` из экземпляра `SPXPushAudioInputStream`, указав MP3 в качестве формата сжатия потока.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

В следующем фрагменте кода показано, как можно считывать сжатые аудио-данные из файла и переносить их в `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>Дальнейшие действия

- [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
- [Узнайте, как распознать речь в Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
