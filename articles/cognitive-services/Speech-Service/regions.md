---
title: Регионы — служба речи
titleSuffix: Azure Cognitive Services
description: Список доступных регионов и конечных точек для службы речи, включая преобразование речи в текст, озвучивание текста в речь и перевод речи.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220471"
---
# <a name="speech-service-supported-regions"></a>Регионы, поддерживаемые речевыми службами

Служба "Речь" позволяет приложению преобразовывать звук в текст, а также выполнять перевод речи и преобразование текста в речь. Служба доступна в нескольких регионах с использованием уникальных конечных точек для REST API и пакета SDK службы "Речь".

Портал речевого ввода для выполнения пользовательских конфигураций в речевом интерфейсе для всех регионов доступен здесь:https://speech.microsoft.com

Для вызовов службы речи убедитесь, что вызов соответствует региону для вашей подписки.

## <a name="speech-sdk"></a>Пакет SDK для службы "Речь"

В [пакете SDK для службы "Речь"](speech-sdk.md) регионы указанны в виде строки (например, в качестве параметра `SpeechConfig.FromSubscription` в пакете SDK службы "Речь" для C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Преобразование речи в текст, преобразования текста в речь и перевода

Портал настройки речи доступен здесь:https://speech.microsoft.com

Служба распознавания речи доступна в этих регионах для **распознавания речи**, преобразования **текста в речь**и **перевода**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Если используется [речевой пакет SDK](speech-sdk.md), регионы задаются с помощью **идентификатора региона** (например, в качестве параметра `SpeechConfig.FromSubscription`). Убедитесь, что регион соответствует региону подписки.

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

[Пакет SDK для распознавания речи](speech-sdk.md) поддерживает возможности **голосового помощника** в следующих регионах:

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

Справочную документацию по голосовым документам см. [в разделе REST API преобразования речи в текст](rest-speech-to-text.md).

Конечная точка для REST API имеет следующий формат:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Замените `<REGION_IDENTIFIER>` идентификатором, соответствующим региону подписки, из следующей таблицы:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> К URL-адресу нужно добавить параметр языка, чтобы не возникало сообщение об ошибке HTTP 4xx. Например, выбор английского языка (США) с использованием конечной точки "Западная часть США" будет выглядеть так: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="text-to-speech"></a>Преобразование текста в речь

Справочную документацию по тексту в речь см. [в разделе REST API преобразования текста в речь](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
