---
title: Регионы - Речевая служба
titleSuffix: Azure Cognitive Services
description: Список доступных регионов и конечных точек для речевой службы, включая речевой текст, текст к речи и речевой перевод.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 560575ca7f51218e472abecb4319f4a3db69b1ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220471"
---
# <a name="speech-service-supported-regions"></a>Речевая служба поддерживает регионы

Служба "Речь" позволяет приложению преобразовывать звук в текст, а также выполнять перевод речи и преобразование текста в речь. Служба доступна в нескольких регионах с использованием уникальных конечных точек для REST API и пакета SDK службы "Речь".

Портал Речи для выполнения пользовательских конфигураций для вашего речевого опыта для всех регионов доступен здесь:https://speech.microsoft.com

Для вызовов службы Speech убедитесь, что вызов соответствует региону для вашей подписки.

## <a name="speech-sdk"></a>Пакет SDK для службы "Речь"

В [пакете SDK для службы "Речь"](speech-sdk.md) регионы указанны в виде строки (например, в качестве параметра `SpeechConfig.FromSubscription` в пакете SDK службы "Речь" для C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>От речи к тексту, тексту к речи и переводу

Портал речевой настройки доступен здесь:https://speech.microsoft.com

Служба речевой речи доступна в этих регионах для **распознавания речи,** **текст к речи**, и **перевод**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

При использовании [речи SDK](speech-sdk.md)регионы определяются **идентификатором** Региона `SpeechConfig.FromSubscription`(например, в качестве параметра). Убедитесь, что регион соответствует региону вашей подписки.

### <a name="intent-recognition"></a>Распознавание намерений

Регионы, доступные для **распознавания намерений** через пакет средств разработки службы "Речь", перечислены ниже:

| Глобальный регион | Регион           | Идентификатор региона |
| ------------- | ---------------- | -------------------- |
| Азия          | Восточная Азия        | `eastasia`           |
| Азия          | Юго-Восточная Азия   | `southeastasia`      |
| Австралия     | Восточная Австралия   | `australiaeast`      |
| Европа        | Северная Европа     | `northeurope`        |
| Европа        | Западная Европа      | `westeurope`         |
| Северная Америка | Восточная часть США          | `eastus`             |
| Северная Америка | восточная часть США 2        | `eastus2`            |
| Северная Америка | Центрально-южная часть США | `southcentralus`     |
| Северная Америка | центрально-западная часть США  | `westcentralus`      |
| Северная Америка | западная часть США          | `westus`             |
| Северная Америка | западная часть США 2        | `westus2`            |
| Южная Америка | Южная Бразилия     | `brazilsouth`        |

Это подмножество поддерживаемых регионов для публикации [службы распознавания речи (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Голосовые помощники

[Speech SDK](speech-sdk.md) поддерживает возможности **голосового помощника** в следующих регионах:

| Регион         | Идентификатор региона |
| -------------- | -------------------- |
| западная часть США        | `westus`             |
| западная часть США 2      | `westus2`            |
| Восточная часть США        | `eastus`             |
| восточная часть США 2      | `eastus2`            |
| Западная Европа    | `westeurope`         |
| Северная Европа   | `northeurope`        |
| Юго-Восточная Азия | `southeastasia`      |

## <a name="rest-apis"></a>Интерфейсы REST API

Служба "Речь" также предоставляет конечные точки REST для запросов распознавания речи в текст и преобразования текста в речь.

### <a name="speech-to-text"></a>Преобразование речи в текст

Для справочной документации от [Speech-to-text REST API](rest-speech-to-text.md)речи к тексту см.

Конечная точка для REST API имеет такой формат:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Замените `<REGION_IDENTIFIER>` идентификатор, соответствующий области подписки из этой таблицы:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> К URL-адресу нужно добавить параметр языка, чтобы не возникало сообщение об ошибке HTTP 4xx. Например, выбор английского языка (США) с использованием конечной точки "Западная часть США" будет выглядеть так: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="text-to-speech"></a>Преобразование текста в речь

Для справочной документации по [Text-to-speech REST API](rest-text-to-speech.md)тексту к речи см.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
