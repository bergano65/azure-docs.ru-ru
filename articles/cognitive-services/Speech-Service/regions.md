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
ms.date: 12/06/2018
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: d41213d72d40555d8dc5aeab76040fc556dae774
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091651"
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

**Распознавание намерений** для пакета SDK службы "Речь" поддерживает те же регионы, что и LUIS. Полный список доступных регионов см. в руководстве по [регионам и конечным точкам публикации (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions).

Доступные регионы для **распознавания намерений** с помощью пакета SDK для службы "Речь" перечислены на [странице регионов службы "Распознавание речи"](/azure/cognitive-services/luis/luis-reference-regions).

Для каждого региона публикации используйте предоставленное **имя региона API**. Например, используйте `westus` для региона "Западная часть США".

## <a name="rest-apis"></a>Интерфейсы API REST

Служба "Речь" также предоставляет конечные точки REST для запросов распознавания речи в текст и преобразования текста в речь.

### <a name="speech-to-text"></a>Преобразование речи в текст.

Дополнительные сведения о преобразовании речи в текст см. в документации по [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Преобразование текста в речь

Дополнительные сведения о преобразовании текста в речь см. в документации по [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
