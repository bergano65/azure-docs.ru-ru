---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: ad1527a5e7f1cb2ff1beb9ddace5460f41bb8a87
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461510"
---
## <a name="azure-cognitive-service-subscription-types"></a>Типы подписки Azure Cognitive Service

> [!NOTE]
> Владелец подписки может отключить создание учетной записи Cognitive Services для группы ресурсов или подписки с помощью [политики Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), присвоив определение политики "Недопустимые типы ресурсов" и указав **Microsoft.CognitiveServices/accounts** в качестве целевого типа ресурсов.

Azure Cognitive Services можно обращаться из двух разных подписках: Подписка нескольких служб или одной службой один. Эти подписки позволяют подключаться к одной службы или несколько служб, за один раз.

### <a name="multi-service-subscription"></a>Подписка на несколько служб

>[!WARNING]
> Сейчас некоторые службы **не** поддерживают ключи для нескольких служб: QnA Maker, речью, пользовательской службе визуального распознавания и обнаружения аномалий.

Нескольких служб подписки для Azure Cognitive Services позволяет использовать одной подписки и ресурсов Azure для большинства служб Azure Cognitive Services и объединяет выставления счетов от служб, которые можно использовать. Дополнительные сведения см. на странице [Цены на Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

### <a name="single-service-subscription"></a>Подписка на одну службу

Подписка на одну службу, например компьютерного или служб речи. Подписка на одну службу распространяется только на соответствующий ресурс. 
