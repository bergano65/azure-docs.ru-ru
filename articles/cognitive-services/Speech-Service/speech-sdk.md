---
title: Сведения о пакете SDK службы "Речь" — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Пакет средств разработки программного обеспечения (SDK) для службы "Речь" предоставляет приложениям встроенный доступ к функциям службы "Речь", что облегчает разработку программного обеспечения. Эта статья содержит дополнительные сведения о пакете SDK для Windows, Linux и Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: wolfma
ms.openlocfilehash: 2bfe111cbe1e352c16c9cbdd6d2ecc999bbe444f
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003013"
---
# <a name="about-the-speech-sdk"></a>Сведения о пакете SDK службы "Речь"

Речи Software Development Kit (SDK) предоставляет приложениям получать доступ к функциям служб речи, облегчая процесс разработки программного обеспечения с поддержкой речевых функций. В настоящее время пакеты SDK предоставляют доступ к **речи в текст**, **преобразования текста в речь**, **перевода речи**, и **распознавание сути высказывания**. Общие сведения о возможностях и поддерживаемых платформ можно найти в документации по [страницы записи](https://aka.ms/csspeech).

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Получение пакета SDK

### <a name="windows"></a> Windows

Для Windows поддерживаются следующие языки:

* C# (UWP и .NET), C++: можно ссылаться и использовать последнюю версию пакета средств разработки NuGet для распознавания речи. Пакет содержит 32-разрядные и 64-разрядные клиентские библиотеки и управляемые библиотеки (.NET). Пакет SDK можно установить в Visual Studio с помощью NuGet. Выполните поиск по **Microsoft.CognitiveServices.Speech**.

* Java: можно ссылаться и использовать последнюю версию пакета Speech SDK Maven, который поддерживает только 64-разрядные версии Windows. В проект Maven добавьте `https://csspeechstorage.blob.core.windows.net/maven/` в качестве дополнительного репозитория и ссылку на `com.microsoft.cognitiveservices.speech:client-sdk:1.5.1` в качестве зависимости.

### <a name="linux"></a>Linux

> [!NOTE]
> Сейчас поддерживается только Ubuntu 16.04, Ubuntu 18.04 и Debian 9 на ПК (x86 или x64 для C++ разработки и x64 для .NET Core, Java и Python).

Убедитесь, что у вас есть необходимые библиотеки, установку, выполнив следующие команды оболочки:

Для Ubuntu.

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Для Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

* C#: можно ссылаться и использовать последнюю версию пакета средств разработки NuGet для распознавания речи. Чтобы ссылаться на пакет SDK, добавьте следующую ссылку на пакет в проект:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.5.1" />
  ```

* Java: можно ссылаться и использовать последнюю версию пакета SDK Maven для распознавания речи. В проект Maven добавьте `https://csspeechstorage.blob.core.windows.net/maven/` в качестве дополнительного репозитория и ссылку на `com.microsoft.cognitiveservices.speech:client-sdk:1.5.1` в качестве зависимости.

* C++: скачайте пакет SDK в виде [пакета TAR](https://aka.ms/csspeech/linuxbinary) и распакуйте файлы в папку по своему усмотрению. В таблице ниже показана структура папок пакета SDK:

  |`Path`|Описание|
  |-|-|
  |`license.md`|Лицензия|
  |`ThirdPartyNotices.md`|Уведомления сторонних производителей|
  |`include`|Файлы заголовков для C и C++|
  |`lib/x64`|Собственная библиотека x64 для связывания с приложением|
  |`lib/x86`|Собственная библиотека x86 для связывания с приложением|

  Чтобы создать приложение, скопируйте или переместите необходимые двоичные файлы (и библиотеки) в среду разработки. Включите их как обязательные в процесс сборки.

### <a name="android"></a>Android

Пакет SDK Java для Android входит в состав [AAR (библиотека Android)](https://developer.android.com/studio/projects/android-library), которая содержит необходимые библиотеки и требуемые разрешения Android. Она размещена в репозитории Maven в `https://csspeechstorage.blob.core.windows.net/maven/` в виде пакета `com.microsoft.cognitiveservices.speech:client-sdk:1.5.1`.

Чтобы использовать этот пакет из проекта Android Studio, внесите следующие изменения:

* В файл build.gradle уровня проекта добавьте следующий текст в раздел `repository`:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* В файл build.gradle уровня модуля добавьте следующий текст в раздел `dependencies`:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.5.1'
  ```

Пакет SDK для Java также входит в [пакет SDK для устройств распознавания речи](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
* [Распознавание речи в C#](quickstart-csharp-dotnet-windows.md)
