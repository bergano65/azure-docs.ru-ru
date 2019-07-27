---
title: Регионы — служба речи
titleSuffix: Azure Cognitive Services
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
ms.openlocfilehash: c0414277b4851891911908ba4f42e92abedc86e4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553199"
---
# <a name="speech-service-supported-regions"></a>Поддерживаемые регионы для службы "Речь"

Служба "Речь" позволяет приложению преобразовывать звук в текст, а также выполнять перевод речи и преобразование текста в речь. Служба доступна в нескольких регионах с использованием уникальных конечных точек для REST API и пакета SDK службы "Речь".

Вам нужно использовать конечную точку, которая соответствует региону для вашей подписки.

## <a name="speech-sdk"></a>Пакет SDK для службы "Речь"

В [пакете SDK для службы "Речь"](speech-sdk.md) регионы указанны в виде строки (например, в качестве параметра `SpeechConfig.FromSubscription` в пакете SDK службы "Речь" для C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Преобразование речи в текст, преобразования текста в речь и перевода

Речевой пакет SDK доступен в этих регионах для **распознавания речи**, преобразования **текста в речь**и **перевода**:

  Район | Параметр пакета SDK для службы "Речь" | Портал настройки распознавания речи
 ------|-------|--------
 Западная часть США | `westus` | https://westus.cris.ai
 Западная часть США 2 | `westus2` | https://westus2.cris.ai
 East US | `eastus` | https://eastus.cris.ai
 Восточная часть США 2 | `eastus2` | https://eastus2.cris.ai
 Центральная часть США | `centralus` | https://centralus.cris.ai
 Центрально-северная часть США | `northcentralus` | https://northcentralus.cris.ai
 Центрально-южная часть США | `southcentralus` | https://southcentralus.cris.ai
 Центральная Индия | `centralindia` | https://centralindia.cris.ai
 Восточная Азия | `eastasia` | https://eastasia.cris.ai
 Юго-Восточная Азия | `southeastasia` | https://southeastasia.cris.ai
 Восточная Япония | `japaneast` | https://japaneast.cris.ai
 Центральная Корея | `koreacentral` | https://koreacentral.cris.ai
 Восточная Австралия | `australiaeast` | https://australiaeast.cris.ai
 Центральная Канада | `canadacentral` | https://canadacentral.cris.ai
 Северная Европа | `northeurope` | https://northeurope.cris.ai
 Западная Европа | `westeurope` | https://westeurope.cris.ai
 Южная часть Соединенного Королевства | `uksouth` | https://uksouth.cris.ai
 Центральная Франция | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Распознавание намерений

Регионы, доступные для **распознавания намерений** через пакет средств разработки службы "Речь", перечислены ниже:

 Глобальный регион | Район | Параметр пакета SDK для службы "Речь"
 ------|-------|--------
 Азия | Восточная Азия | `eastasia`
 Азия | Юго-Восточная Азия | `southeastasia`
 Австралия | Восточная Австралия | `australiaeast`
 Европа | Северная Европа | `northeurope`
 Европа | Западная Европа | `westeurope`
 Северная Америка | East US | `eastus`
 Северная Америка | Восточная часть США 2 | `eastus2`
 Северная Америка | Центрально-южная часть США | `southcentralus`
 Северная Америка | Центрально-западная часть США | `westcentralus`
 Северная Америка | Западная часть США | `westus`
 Северная Америка | Западная часть США 2 | `westus2`
 Южная Америка | Южная Бразилия | `brazilsouth`

Это подмножество поддерживаемых регионов для публикации [службы распознавания речи (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-first-virtual-assistants"></a>Виртуальные помощники по обработке голоса

[Пакет SDK для распознавания речи](speech-sdk.md) поддерживает возможности **виртуального помощника** по голосовым функциям в следующих регионах:

Район | Параметр пакета SDK для службы "Речь"
-------|---------------------
Западная часть США | `westus`
Западная часть США 2 | `westus2`
East US | `eastus`
Восточная часть США 2 | `eastus2`
Западная Европа | `westeurope`
Северная Европа | `northeurope`
Юго-Восточная Азия | `southeastasia`

## <a name="rest-apis"></a>REST API

Служба "Речь" также предоставляет конечные точки REST для запросов распознавания речи в текст и преобразования текста в речь.

### <a name="speech-to-text"></a>Преобразование речи в текст

Справочную документацию по голосовым документам см. [в разделе REST API преобразования речи в текст](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Преобразование текста в речь

Справочную документацию по тексту в речь см. [в разделе REST API преобразования текста в речь](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]