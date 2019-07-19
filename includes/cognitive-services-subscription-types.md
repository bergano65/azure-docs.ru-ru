---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 674dd30ff3e493ec4c4036f032f82624a6ca5749
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334258"
---
## <a name="azure-cognitive-service-resource-types"></a>Типы ресурсов службы "служба Azure для себя"

> [!NOTE]
> Владельцы подписки могут отключить создание Cognitive Services ресурсов для групп ресурсов и подписок, применив [политику Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), назначив определение политики "запрещенные типы ресурсов" и указав  **Microsoft. CognitiveServices/Accounts** как тип целевого ресурса.

Вы можете получить доступ к Cognitive Services Azure с помощью двух разных ресурсов: Ресурс с несколькими службами или один сервис. Эти подписки позволяют одновременно подключиться к одной службе или нескольким службам.

### <a name="multi-service-resource"></a>Ресурс с несколькими службами

>[!WARNING]
> Сейчас некоторые службы **не** поддерживают ключи для нескольких служб: QnA Maker, службы речи, Пользовательское визуальное распознавание и детектор аномалий.

Подписка на ресурс Cognitive Services с несколькими службами:
* Позволяет использовать один ресурс Azure для большинства Cognitive Services Azure.
* Консолидация счетов из используемых вами служб. Дополнительные сведения см. на странице [Цены на Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

### <a name="single-service-resource"></a>Ресурс с одной службой

Ресурсы одной службы (например, Компьютерное зрение или службы речи) ограничены указанной службой.