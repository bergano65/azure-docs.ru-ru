---
title: Краткое руководство. Настройка платформы для пакета SDK службы "Речь" для C++ (macOS) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как настроить платформу для использования C++ в macOS с пакетом SDK службы "Речь".
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 3ad8eb9564c4d8343a0763cc2f6f5061ee602b72
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188375"
---
Здесь также описано, как установить [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для C++ в macOS 10.13 и более новых версий.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Требования к системе

macOS версии 10.13 и более новых версий.

## <a name="install-speech-sdk"></a>Установка пакета SDK для службы "Речь"

1. Выберите каталог, в который должны быть извлечены файлы пакета SDK для службы "Речь", и задайте переменную среды `SPEECHSDK_ROOT`, чтобы она указывала на этот каталог. Эта переменная упрощает подключение к каталогу в будущих командах. Например, если вы хотите использовать каталог `speechsdk` в домашнем каталоге, используйте следующую команду.

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Если каталог не существует, создайте его.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Скачайте и извлеките архив `.zip` с платформой пакета SDK для службы "Речь".

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Проверьте содержимое каталога верхнего уровня извлеченного пакета.

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Список файлов в каталоге должен содержать уведомление третьих лиц и файлы лицензии, а также каталог `MicrosoftCognitiveServicesSpeech.framework`.

Теперь можно перейти к разделу [Дополнительная информация](#next-steps) ниже.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [windows](../quickstart-list.md)]
