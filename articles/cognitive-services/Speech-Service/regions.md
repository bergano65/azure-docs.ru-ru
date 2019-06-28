---
title: Регионы службы "Речь"
titlesuffix: Azure Cognitive Services
description: Справочник по регионам службы "Речь".
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 1ad618e9eb70fd75f433030584c0f6538532928f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466987"
---
# <a name="speech-service-supported-regions"></a>Поддерживаемые регионы для службы "Речь"

Служба "Речь" позволяет приложению преобразовывать звук в текст, а также выполнять перевод речи и преобразование текста в речь. Служба доступна в нескольких регионах с использованием уникальных конечных точек для REST API и пакета SDK службы "Речь".

Вам нужно использовать конечную точку, которая соответствует региону для вашей подписки.

## <a name="speech-sdk"></a>Пакет SDK для службы "Речь"

В [пакете SDK для службы "Речь"](speech-sdk.md) регионы указанны в виде строки (например, в качестве параметра `SpeechConfig.FromSubscription` в пакете SDK службы "Речь" для C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Речи в текст, текст в речь и перевод

Speech SDK доступна в этих регионах для **распознавания речи**, **преобразования текста в речь**, и **перевода**:

  Регион | Параметр пакета SDK для службы "Речь" | Портал настройки распознавания речи
 ------|-------|--------
 Запад США | `westus` | https://westus.cris.ai
 Западный регион США 2 | `westus2` | https://westus2.cris.ai
 Восточная часть США | `eastus` | https://eastus.cris.ai
 Восток США 2 | `eastus2` | https://eastus2.cris.ai
 Центральный регион США | `centralus` | https://centralus.cris.ai
 Центрально-северная часть США | `northcentralus` | https://northcentralus.cris.ai
 Центрально-южная часть США | `southcentralus` | https://southcentralus.cris.ai
 Центральная Индия | `centralindia` | https://centralindia.cris.ai
 Восточная Азия | `eastasia` | https://eastasia.cris.ai
 Юго-Восточная Азия | `southeastasia` | https://southeastasia.cris.ai
 Восточная часть Японии | `japaneast` | https://japaneast.cris.ai
 Центральная Корея | `koreacentral` | https://koreacentral.cris.ai
 Восточная часть Австралии | `australiaeast` | https://australiaeast.cris.ai
 Центральная Канада | `canadacentral` | https://canadacentral.cris.ai
 Северная Европа | `northeurope` | https://northeurope.cris.ai
 Западная Европа | `westeurope` | https://westeurope.cris.ai
 Южная часть Великобритании | `uksouth` | https://uksouth.cris.ai
 Центральная Франция | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Распознавание намерений

Регионы, доступные для **распознавания намерений** через пакет средств разработки службы "Речь", перечислены ниже:

 Глобальный регион | Регион | Параметр пакета SDK для службы "Речь"
 ------|-------|--------
 Азия | Восточная Азия | `eastasia`
 Азия | Юго-Восточная Азия | `southeastasia`
 Австралия | Восточная часть Австралии | `australiaeast`
 Европа | Северная Европа | `northeurope`
 Европа | Западная Европа | `westeurope`
 Северная Америка | Восточная часть США | `eastus`
 Северная Америка | Восток США 2 | `eastus2`
 Северная Америка | Центрально-южная часть США | `southcentralus`
 Северная Америка | Западно-центральная часть США | `westcentralus`
 Северная Америка | Запад США | `westus`
 Северная Америка | Западный регион США 2 | `westus2`
 Северная Америка | Южная часть Бразилии | `brazilsouth`

Это подмножество поддерживаемых регионов для публикации [службы распознавания речи (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-first-virtual-assistants"></a>Виртуальные помощники по обработке голоса

[Speech SDK](speech-sdk.md) поддерживает **виртуальные помощники voice-first** возможности в этих регионах:

Регион | Параметр пакета SDK для службы "Речь"
-------|---------------------
Запад США | `westus`
Западный регион США 2 | `westus2`
Восточная часть США | `eastus`
Восток США 2 | `eastus2`
Западная Европа | `westeurope`
Северная Европа | `northeurope`
Юго-Восточная Азия | `southeastasia`

## <a name="rest-apis"></a>Интерфейсы API REST

Служба "Речь" также предоставляет конечные точки REST для запросов распознавания речи в текст и преобразования текста в речь.

### <a name="speech-to-text"></a>Преобразование речи в текст

Для преобразования речи в текст справочную документацию см. в разделе [REST API распознавания речи в текст](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Преобразование текста в речь

Для преобразования текста в речь справочную документацию см. в разделе [REST API преобразования текста в речь](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]