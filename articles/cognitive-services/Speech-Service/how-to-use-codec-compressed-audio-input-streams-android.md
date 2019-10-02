---
title: Потоковая передача сжатого аудио-кодека с помощью речевого пакета SDK на Android — служба речевого ввода
titleSuffix: Azure Cognitive Services
description: Узнайте, как выполнять потоковую передачу сжатых аудио в службы речевого обучения Azure с помощью речевого пакета SDK на Android.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: df5eb123a2fd47a3eceea8153786442bf56a2718
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803836"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk-on-android"></a>Использование сжатых звуковых данных кодеков с пакетом SDK для распознавания речи в Android

Сжатый аудио-API-интерфейс пакета речевого **ввода** предоставляет способ потоковой передачи сжатого звука в службу распознавания речи с помощью Пуллстреам или пушстреам.

> [!IMPORTANT]
> В настоящее время поддерживается потоковая передача сжатых аудио-данных [ C++в, C#и Java в Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9)](how-to-use-codec-compressed-audio-input-streams.md). Она также поддерживается для Java в Android и [цели-C на платформе iOS](how-to-use-codec-compressed-audio-input-streams-ios.md) .
> Требуется пакет SDK для распознавания речи версии 1.7.0 или выше.

Сведения о WAV/PCM см. в документации по магистрали.  За пределами WAV/PCM поддерживаются следующие сжатые форматы входных данных кодеков:

- MP3
- ОПУС/OGG
- FLAC
- АЛАВ в контейнере WAV
- МУЛАВ в контейнере WAV

## <a name="prerequisites-to-using-codec-compressed-audio-input-on-android"></a>Предварительные требования для использования сжатого звукового ввода кодеков в Android

Сжатый аудиокодек реализован с помощью [гстреамер](https://gstreamer.freedesktop.org). В целях лицензирования двоичные файлы Гстреамер не компилируются вместе с пакетом SDK. Вам потребуется использовать предварительно созданные двоичные файлы для Android. Сведения о загрузке предварительно созданных библиотек см. в разделе [Установка для разработки Android](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c). 

требуется `libgstreamer_android.so`. Убедитесь, что подключаемые модули Гстреамер связаны в `libgstreamer_android.so`.

```make
GSTREAMER_PLUGINS := coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
```

Ниже приведен пример файла `Android.mk` и `Application.mk`. Чтобы создать общий объект гстреамер, выполните следующие действия: `libgstreamer_android.so`.

```make
# Android.mk
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := dummy
LOCAL_SHARED_LIBRARIES := gstreamer_android
LOCAL_LDLIBS := -llog
include $(BUILD_SHARED_LIBRARY)


ifndef GSTREAMER_ROOT_ANDROID
$(error GSTREAMER_ROOT_ANDROID is not defined!)
endif

ifndef APP_BUILD_SCRIPT
$(error APP_BUILD_SCRIPT is not defined!)
endif

ifndef TARGET_ARCH_ABI
$(error TARGET_ARCH_ABI is not defined!)
endif

ifeq ($(TARGET_ARCH_ABI),armeabi)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm
else ifeq ($(TARGET_ARCH_ABI),armeabi-v7a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/armv7
else ifeq ($(TARGET_ARCH_ABI),arm64-v8a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm64
else ifeq ($(TARGET_ARCH_ABI),x86)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86
else ifeq ($(TARGET_ARCH_ABI),x86_64)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86_64
else
$(error Target arch ABI not supported: $(TARGET_ARCH_ABI))
endif

GSTREAMER_NDK_BUILD_PATH  := $(GSTREAMER_ROOT)/share/gst-android/ndk-build/
include $(GSTREAMER_NDK_BUILD_PATH)/plugins.mk
GSTREAMER_PLUGINS         :=  coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
GSTREAMER_EXTRA_LIBS      := -liconv
include $(GSTREAMER_NDK_BUILD_PATH)/gstreamer-1.0.mk
```

```make
# Application.mk
APP_STL = c++_shared
APP_PLATFORM = android-21
APP_BUILD_SCRIPT = Android.mk
```

Вы можете создать `libgstreamer_android.so` с помощью следующей команды в Ubuntu 16,04 или 18,04. Следующие командные строки были протестированы только для [Гстреамер Android версии 1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2) с [Android NDK b16b.](https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip)

```sh
# assuming wget and unzip already installed on the system
mkdir buildLibGstreamer
cd buildLibGstreamer
wget https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip
unzip -q -o android-ndk-r16b-linux-x86_64.zip
export PATH=$PATH:$(pwd)/android-ndk-r16b
export NDK_PROJECT_PATH=$(pwd)/android-ndk-r16b
wget https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2
mkdir gstreamer_android
tar -xjf gstreamer-1.0-android-universal-1.14.4.tar.bz2 -C $(pwd)/gstreamer_android/
export GSTREAMER_ROOT_ANDROID=$(pwd)/gstreamer_android

mkdir gstreamer
# Copy the Application.mk and Android.mk from the documentation above and put it inside $(pwd)/gstreamer

# Enable only one of the following at one time to create the shared object for the targeted ABI
echo "building for armeabi-v7a. libgstreamer_android.so will be placed in $(pwd)/armeabi-v7a"
ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=armeabi-v7a NDK_LIBS_OUT=$(pwd)

#echo "building for arm64-v8a. libgstreamer_android.so will be placed in $(pwd)/arm64-v8a"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=arm64-v8a NDK_LIBS_OUT=$(pwd)

#echo "building for x86_64. libgstreamer_android.so will be placed in $(pwd)/x86_64"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86_64 NDK_LIBS_OUT=$(pwd)

#echo "building for x86. libgstreamer_android.so will be placed in $(pwd)/x86"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86 NDK_LIBS_OUT=$(pwd)
```

Когда общий объект (libgstreamer_android. so) будет создан разработчиком приложения, необходимо поместить общий объект в приложение Android, чтобы его можно было загрузить с помощью речевого пакета SDK.

## <a name="example-code-using-codec-compressed-audio-input"></a>Пример кода, использующий сжатые звуковые входные кодеки

Чтобы выполнить потоковую передачу в сжатом аудио формате в речевые службы, создайте `PullAudioInputStream` или `PushAudioInputStream`. Затем создайте `AudioConfig` из экземпляра класса Stream, указав формат сжатия потока.

Предположим, что у вас есть класс входного потока с именем `myPullStream` и опус/OGG. Код может выглядеть следующим образом:

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = AudioStreamFormat.getCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig = AudioConfig.fromStreamInput(myPullStream, audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get()

String text = result.getText();
```

## <a name="next-steps"></a>Следующие шаги

- [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
- [Распознавание речи в C#](quickstart-csharp-dotnet-windows.md)
