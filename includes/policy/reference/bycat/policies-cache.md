---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/04/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 430a0ebaa65bb96af575ab216bc6a413329c4a1f
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89487932"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Кэш Azure для Redis должен находиться в виртуальной сети](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Кэш Azure для Redis может находиться в виртуальной сети, что позволяет ресурсу иметь не являющуюся общедоступной конечную точку, контролируемую и управляемую пользователем. |Audit, Deny, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Использование только безопасных подключений к кэшу Azure для Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Аудит активации только подключений по протоколу SSL к кэшу Azure для Redis. Использование безопасных подключений обеспечивает проверку подлинности между сервером и службой, а также защищает переносимые данные от атак сетевого уровня, таких как "злоумышленник в середине", прослушивание трафика и перехват сеанса. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
