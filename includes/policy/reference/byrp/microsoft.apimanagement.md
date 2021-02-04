---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f0952c2c2cb75c25d06714cae669c905ae9366c5
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556720"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Службы управления API должны использовать виртуальную сеть](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Развертывание виртуальной сети Azure обеспечивает повышенную безопасность, изоляцию и позволяет разместить службу управления API в сети, не поддерживающей маршрутизацию через Интернет, которая управляет доступом к. Затем эти сети могут быть подключены к локальным сетям с помощью различных технологий VPN, что обеспечивает доступ к внутренним службам внутри сети и (или) локальной среды. Портал разработчика и шлюз API можно настроить для предоставления доступа как из Интернета, так и только в пределах виртуальной сети. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
