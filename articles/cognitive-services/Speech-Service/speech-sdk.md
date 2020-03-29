---
title: О речи SDK - Речевая служба
titleSuffix: Azure Cognitive Services
description: Пакет средств разработки программного обеспечения (SDK) для службы "Речь" предоставляет приложениям встроенный доступ к функциям службы "Речь", что облегчает разработку программного обеспечения. Эта статья содержит дополнительные сведения о пакете SDK для Windows, Linux и Android.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 984d2dfe07faa22756b4be167aa86a69806b1a84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331099"
---
# <a name="about-the-speech-sdk"></a>Сведения о пакете SDK службы "Речь"

Пакет средств разработки программного обеспечения (SDK) для службы "Речь" предоставляет приложениям доступ к функциям службы "Речь", что облегчает разработку программного обеспечения с поддержкой речи. В настоящее время, SDKs обеспечить доступ к **речи к тексту**, **текст к речи**, перевод **речи**, **признание намерений**, и **Бот Framework в Прямой линии речи канала**.

Вы можете легко захватить аудио с микрофона, читать из потока, или получить доступ к аудио файлы из хранилища с речью SDK. Speech SDK поддерживает WAV/PCM 16-битный, 16 кГц/8 кГц, одноканальный звук для распознавания речи. Дополнительные аудиоформаты поддерживаются с помощью [точки от речи к тексту REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) или [службы пакетной транскрипции.](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)

Общий обзор возможностей и поддерживаемых платформ можно найти на [странице ввода](https://aka.ms/csspeech)документации.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Получение пакета SDK

# <a name="windows"></a>[Windows](#tab/windows)

> [!WARNING]
> Speech SDK поддерживает Windows 10 или более поздние версии. Более ранние версии Windows **не поддерживаются.**

Для Windows поддерживаются следующие языки:

* C# (UWP и .NET), C++: можно ссылаться и использовать последнюю версию пакета средств разработки NuGet для распознавания речи. Пакет содержит 32-разрядные и 64-разрядные клиентские библиотеки и управляемые библиотеки (.NET). SDK может быть установлен в Visual Studio с помощью NuGet, [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech).

* Java: можно ссылаться и использовать последнюю версию пакета Speech SDK Maven, который поддерживает только 64-разрядные версии Windows. В проект Maven добавьте `https://csspeechstorage.blob.core.windows.net/maven/` в качестве дополнительного репозитория и ссылку на `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` в качестве зависимости.

# <a name="linux"></a>[Linux](#tab/linux)

> [!NOTE]
> В настоящее время мы поддерживаем только Ubuntu 16.04, Ubuntu 18.04, Debian 9, Red Hat Enterprise Linux (RHEL) 8 и CentOS 8 на следующих целевых архитектурах:
> - x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) и ARM64 (Debian/Ubuntu) для разработки КЗ
> - x64, ARM32 (Debian/Ubuntu) и ARM64 (Debian/Ubuntu) для Java
> - x64 для ядра и python .NET

Убедитесь, что у вас есть необходимые библиотеки, установленные, запустив следующие команды оболочки:

В Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Для Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

На RHEL/CentOS 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> На RHEL/CentOS 8 следуйте инструкциям о том, [как настроить OpenSSL для Linux.](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)

* C#: можно ссылаться и использовать последнюю версию пакета средств разработки NuGet для распознавания речи. Чтобы ссылаться на пакет SDK, добавьте следующую ссылку на пакет в проект:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: можно ссылаться и использовать последнюю версию пакета SDK Maven для распознавания речи. В проект Maven добавьте `https://csspeechstorage.blob.core.windows.net/maven/` в качестве дополнительного репозитория и ссылку на `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` в качестве зависимости.

* C++: скачайте пакет SDK в виде [пакета TAR](https://aka.ms/csspeech/linuxbinary) и распакуйте файлы в папку по своему усмотрению. В таблице ниже показана структура папок пакета SDK:

  |путь|Описание|
  |-|-|
  |`license.md`|Лицензия|
  |`ThirdPartyNotices.md`|Уведомления сторонних производителей|
  |`include`|Файлы заголовков для C и C++|
  |`lib/x64`|Собственная библиотека x64 для связывания с приложением|
  |`lib/x86`|Собственная библиотека x86 для связывания с приложением|

  Чтобы создать приложение, скопируйте или переместите необходимые двоичные файлы (и библиотеки) в среду разработки. Включите их как обязательные в процесс сборки.

# <a name="android"></a>[Android](#tab/android)

Пакет SDK Java для Android входит в состав [AAR (библиотека Android)](https://developer.android.com/studio/projects/android-library), которая содержит необходимые библиотеки и требуемые разрешения Android. Она размещена в репозитории Maven в `https://csspeechstorage.blob.core.windows.net/maven/` в виде пакета `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Чтобы использовать этот пакет из проекта Android Studio, внесите следующие изменения:

* В файл build.gradle уровня проекта добавьте следующий текст в раздел `repository`:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* В файл build.gradle уровня модуля добавьте следующий текст в раздел `dependencies`:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Пакет SDK для Java также входит в [пакет SDK для устройств распознавания речи](speech-devices-sdk.md).

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
* [Распознавание речи в C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
