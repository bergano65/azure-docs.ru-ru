---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/04/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8e34b93abc5ef4f67f2bada7171a74a8b843131a
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89487901"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Служба "Конфигурация приложений" должна использовать управляемый клиентом ключ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |Ключи, управляемые клиентом, обеспечивают улучшенную защиту данных, позволяя вам управлять своими ключами шифрования. Это часто необходимо для удовлетворения требований по обеспечению соответствия. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json) |
|[Конфигурация приложений должна использовать приватный канал](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Подключения к частной конечной точке позволяют клиентам виртуальной сети безопасно получать доступ к службе "Конфигурация приложений Azure" через приватный канал. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
