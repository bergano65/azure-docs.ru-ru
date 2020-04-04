---
title: Поток кодек сжатого звука с речью SDK - Речь службы
titleSuffix: Azure Cognitive Services
description: Узнайте, как передавать сжатый звук в службу speech с помощью Speech SDK. Доступно для СЗ, СЗ и Java для Linux, Java в Android и Objective-C в iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: bd6b500a823bde654da4442704b75451806d6a46
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637301"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Используйте кодек сжатого звука ввода с речью SDK

Служба речевого ввода SDK **Compressed Audio Input Stream** API предоставляет способ `PullStream` `PushStream`потоковой передачи сжатого звука в службу speech с помощью a или .

Потоковая передача сжатого вхостабля в настоящее время поддерживается для C, C, Java на Windows (приложения UWP не поддерживаются) и Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). Он также поддерживается для Java в Android и Objective-C в платформе iOS.
* Речь SDK версия 1.10.0 или позже требуется для RHEL 8 и CentOS 8
* Версия SDK 1.11.0 или позже требуется для Windows.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Предварительные требования

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Пример кода с использованием кодека сжатого ввода звука

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Узнайте, как распознать речь](quickstarts/speech-to-text-from-microphone.md)
