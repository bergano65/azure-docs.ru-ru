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
ms.openlocfilehash: 1be51c4f5aede58a975dc9441b3bec7a83f7becd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57900919"
---
# <a name="speech-service-supported-regions"></a>Поддерживаемые регионы для службы "Речь"

Служба "Речь" позволяет приложению преобразовывать звук в текст, а также выполнять перевод речи и преобразование текста в речь. Служба доступна в нескольких регионах с использованием уникальных конечных точек для REST API и пакета SDK службы "Речь".

Вам нужно использовать конечную точку, которая соответствует региону для вашей подписки.

## <a name="speech-sdk"></a>Пакет SDK для распознавания речи

В [пакете SDK для службы "Речь"](speech-sdk.md) регионы указанны в виде строки (например, в качестве параметра `SpeechConfig.FromSubscription` в пакете SDK службы "Речь" для C#).

### <a name="speech-recognition-and-translation"></a>Распознавание и преобразование речи

Пакет SDK службы "Речь" доступен в следующих регионах для **распознавания речи** и **перевода**:

  Регион | Параметр пакета SDK для службы "Речь" | Портал настройки распознавания речи
 ------|-------|--------
 Запад США | `westus` | https://westus.cris.ai
 Западная часть США 2 | `westus2` | https://westus2.cris.ai
 Восточная часть США | `eastus` | https://eastus.cris.ai
 Восток США 2 | `eastus2` | https://eastus2.cris.ai
 Центральный регион США | «centralus» | https://centralus.cris.ai
 Центрально-северная часть США | «northcentralus» | https://northcentralus.cris.ai
 Центрально-южная часть США | «southcentralus» | https://southcentralus.cris.ai
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

## <a name="rest-apis"></a>Интерфейсы API REST

Служба "Речь" также предоставляет конечные точки REST для запросов распознавания речи в текст и преобразования текста в речь.

### <a name="speech-to-text"></a>Преобразование речи в текст.

Дополнительные сведения о преобразовании речи в текст см. в документации по [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Преобразование текста в речь

Дополнительные сведения о преобразовании текста в речь см. в документации по [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
