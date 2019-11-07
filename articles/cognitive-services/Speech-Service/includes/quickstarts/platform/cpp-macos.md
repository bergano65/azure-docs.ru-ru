---
title: Краткое руководство. Настройка платформы для пакета SDK службы "Речь" для C++ (macOS) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как настроить платформу для использования C++ в macOS с пакетом SDK для службы "Речь".
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 2c6a186eef631372f08b876083dacede31cf1077
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504470"
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

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [windows](../quickstart-list.md)]
