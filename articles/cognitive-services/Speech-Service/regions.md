---
title: Регионы службы "Речь"
titlesuffix: Azure Cognitive Services
description: Справочник по регионам службы "Речь".
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: d302fc67225213ad55687797c97c7a2a8a17cc6d
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329513"
---
# <a name="speech-service-supported-regions"></a>Поддерживаемые регионы для службы "Речь"

Служба "Речь" позволяет приложению преобразовывать звук в текст, а также выполнять перевод речи и преобразование текста в речь. Служба доступна в нескольких регионах с использованием уникальных конечных точек для REST API и пакета SDK службы "Речь".

Вам нужно использовать конечную точку, которая соответствует региону для вашей подписки.

## <a name="speech-sdk"></a>Пакет SDK для распознавания речи

В [пакете SDK для службы "Речь"](speech-sdk.md) регионы указаны в виде строки (например, в качестве параметра `SpeechConfig.FromSubscription` в пакете SDK службы "Речь" для C#).

### <a name="speech-recognition-and-translation"></a>Распознавание и преобразование речи

Пакет SDK службы "Речь" доступен в следующих регионах для **распознавания речи** и **перевода**:

  Регион | Параметр пакета SDK для службы "Речь" | Портал настройки распознавания речи
 ------|-------|--------
 Запад США | `westus` | https://westus.cris.ai
 Западная часть США 2 | `westus2` | https://westus2.cris.ai
 Восточная часть США | `eastus` | https://eastus.cris.ai
 Восток США 2 | `eastus2` | https://eastus2.cris.ai
 Восточная Азия | `eastasia` | https://eastasia.cris.ai
 Юго-Восточная Азия | `southeastasia` | https://southeastasia.cris.ai
 Северная Европа | `northeurope` | https://northeurope.cris.ai
 Западная Европа | `westeurope` | https://westeurope.cris.ai


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

Дополнительные сведения о преобразовании речи в текст см. в документации по [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Преобразование текста в речь

Дополнительные сведения о преобразовании текста в речь см. в документации по [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
