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
ms.date: 09/24/2018
ms.author: mahilleb
ms.openlocfilehash: a5fce6f9547a96da3ce482ce388e5ba2093f2af4
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468132"
---
# <a name="regions-of-the-speech-service"></a>Регионы службы "Речь"

Служба "Речь" доступна в нескольких регионах.
При создании подписки можно выбрать доступный регион в соответствии с потребностями.

При использовании подписки необходима учетная запись для выбранного региона.

## <a name="rest-api"></a>REST API

Используйте REST API для выбора соответствующих конечных точек в конкретном регионе.
Дополнительные сведения приведены описании [интерфейсов REST API](rest-apis.md).

## <a name="speech-sdk"></a>Пакет SDK для распознавания речи

В [пакете SDK для службы "Речь"](speech-sdk.md) регионы указаны в виде строки (например, в качестве параметра `SpeechConfig.FromSubscription` в пакете SDK службы "Речь" для C#).

### <a name="regions-for-speech-recognition-and-translation"></a>Регионы для распознавания и перевода речи

В следующей таблице перечислены доступные регионы для служб **распознавания речи** и **перевода**.

  Регион | Параметр пакета SDK для службы "Речь" | Microsoft Azure
 ------|-------|--------
 Запад США | `westus` | https://westus.cris.ai
 Западная часть США 2 | `westus2` | https://westus2.cris.ai 
 Восточная часть США | `eastus` | https://eastus.cris.ai
 Восток США 2 | `eastus2` | https://eastus2.cris.ai
 Восточная Азия | `eastasia` | https://eastasia.cris.ai
 Юго-Восточная Азия | `southeastasia` | https://southeastasia.cris.ai
 Северная Европа | `northeurope` | https://northeurope.cris.ai
 Западная Европа | `westeurope` | https://westeurope.cris.ai


### <a name="regions-for-intent-recognition"></a>Регионы для распознавания намерений

Доступные регионы для **распознавания намерений** с помощью пакета SDK для службы "Речь" перечислены на [странице регионов службы "Распознавание речи"](/azure/cognitive-services/luis/luis-reference-regions).
Для каждого указанного региона публикации в пакете SDK для службы "Речь" определен соответствующий параметр, представляющий собой первую часть доменного имени конечной точки.
Например, используйте `westus`, чтобы указать регион публикации "Западная часть США".
