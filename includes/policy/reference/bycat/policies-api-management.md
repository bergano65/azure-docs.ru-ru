---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 611c2792162a152292c19333000465b1ebe9fbbc
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100420"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Службы Управления API должны использовать виртуальную сеть](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Развертывание виртуальной сети Azure обеспечивает повышенную безопасность, изоляцию и позволяет разместить службу "Управление API" в сети без маршрутизации через Интернет, доступом к которой вы полностью управляете. Затем эти сети можно подключить к локальным сетям с помощью различных технологий VPN, что обеспечивает доступ к внутренним службам в сети или локальной среде. Портал разработчика и шлюз API можно настроить для предоставления доступа как из Интернета, так и только в пределах виртуальной сети. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
