---
title: О пакете SDK для распознавания речи — речевая служба
titleSuffix: Azure Cognitive Services
description: Пакет средств разработки программного обеспечения (SDK) для службы "Речь" предоставляет приложениям встроенный доступ к функциям службы "Речь", что облегчает разработку программного обеспечения. Эта статья содержит дополнительные сведения о пакете SDK для Windows, Linux и Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: ee505cc78c16d85640c7e13541272f1bafe30061
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71800008"
---
# <a name="about-the-speech-sdk"></a>Сведения о пакете SDK службы "Речь"

Пакет средств разработки речевых программ (SDK) предоставляет приложениям доступ к функциям речевых служб, что упрощает разработку программ с поддержкой речи. В настоящее время пакеты SDK предоставляют доступ к **речевым** **трансляциям, преобразованию** **текста в речь**, **распознаванию**речи и **каналу голосовой речи прямой линии**. Общий обзор возможностей и поддерживаемых платформ можно найти на [странице записи](https://aka.ms/csspeech)документации.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Получение пакета SDK

### <a name="windows"></a>Windows

Для Windows поддерживаются следующие языки:

* C# (UWP и .NET), C++: можно ссылаться и использовать последнюю версию пакета средств разработки NuGet для распознавания речи. Пакет содержит 32-разрядные и 64-разрядные клиентские библиотеки и управляемые библиотеки (.NET). Пакет SDK можно установить в Visual Studio с помощью NuGet. Выполните поиск по **Microsoft.CognitiveServices.Speech**.

* Java: можно ссылаться и использовать последнюю версию пакета Speech SDK Maven, который поддерживает только 64-разрядные версии Windows. В проект Maven добавьте `https://csspeechstorage.blob.core.windows.net/maven/` в качестве дополнительного репозитория и ссылку на `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` в качестве зависимости.

### <a name="linux"></a>Linux

> [!NOTE]
> Сейчас в следующих целевых архитектурах поддерживаются только Ubuntu 16,04, Ubuntu 18,04 и Debian 9.
> - x86, x64 и ARM64 для C++ разработки
> - x64 и ARM64 для Java
> - x64 для .NET Core и Python

Убедитесь, что установлены необходимые библиотеки, выполнив следующие команды оболочки:

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
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.7.0" />
  ```

* Java: можно ссылаться и использовать последнюю версию пакета SDK Maven для распознавания речи. В проект Maven добавьте `https://csspeechstorage.blob.core.windows.net/maven/` в качестве дополнительного репозитория и ссылку на `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` в качестве зависимости.

* C++: скачайте пакет SDK в виде [пакета TAR](https://aka.ms/csspeech/linuxbinary) и распакуйте файлы в папку по своему усмотрению. В таблице ниже показана структура папок пакета SDK:

  |Path|Описание|
  |-|-|
  |`license.md`|Лицензия|
  |`ThirdPartyNotices.md`|Уведомления сторонних производителей|
  |`include`|Файлы заголовков для C и C++|
  |`lib/x64`|Собственная библиотека x64 для связывания с приложением|
  |`lib/x86`|Собственная библиотека x86 для связывания с приложением|

  Чтобы создать приложение, скопируйте или переместите необходимые двоичные файлы (и библиотеки) в среду разработки. Включите их как обязательные в процесс сборки.

### <a name="android"></a>Android

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

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Следующие шаги

* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
* [Распознавание речи в C#](quickstart-csharp-dotnet-windows.md)
