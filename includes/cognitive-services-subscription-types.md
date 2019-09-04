---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 3b808f4eb853cf05eb08cd1acf08dedccabf71a6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274644"
---
## <a name="azure-cognitive-service-resource-types"></a>Типы ресурсов службы "служба Azure для себя"

<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
Вы можете получить доступ к Cognitive Services Azure с помощью двух разных ресурсов: Ресурс с несколькими службами или один сервис. Эти подписки позволяют подключаться к одной или нескольким перенаправленным службам одновременно.

### <a name="multi-service-resource"></a>Ресурс с несколькими службами

Подписка на ресурс Cognitive Services с несколькими службами:
* Позволяет использовать один ресурс Azure для большинства Cognitive Services Azure.
* Вы получаете один ключ, который можно использовать с несколькими Cognitive Services Azure.
* Консолидация счетов из используемых вами служб. Дополнительные сведения см. на странице [Цены на Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

>[!WARNING]
> Сейчас некоторые службы **не** поддерживают ключи для нескольких служб: QnA Maker, службы речи, Пользовательское визуальное распознавание и детектор аномалий.

### <a name="single-service-resource"></a>Ресурс с одной службой

Подписка на ресурс Cognitive Services с одной службой:
* Позволяет использовать указанную службу для создания ресурсов (например, Компьютерное зрение или службы речи).
* Вы получаете ключ, относящийся к службе, для которой создается ресурс.