---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: aedfe8783beacfe2e6679848ef4c2defa24d2da0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557708"
---
<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](../articles/governance/policy/overview.md#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
Вы можете получить доступ к Azure Cognitive Services с помощью двух разных ресурсов: Ресурс с одной или несколькими службами.

* Ресурс с несколькими службами:
    * доступ к нескольким службам Azure Cognitive Services с помощью одного ключа и конечной точки;
    * объединение счетов из используемых служб.
* Ресурс с одной службой:
    * доступ к одной службе Azure Cognitive Service с уникальным ключом и конечной точкой для каждой созданной службы; 
    * использование уровня "Бесплатный" для работы со службой.