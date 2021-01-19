---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8b8623d5fed525372c7335600dfa988cad91bd89
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047508"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Кэш Azure для Redis должен находиться в виртуальной сети](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Развертывание виртуальной сети Azure обеспечивает дополнительные возможности защиты и изоляции Кэша Azure для Redis. Такие развертывания позволяют определять подсети, настраивать политики контроля доступа, а также использовать другие функции ограничения доступа. Если экземпляр Кэша Azure для Redis настроен в виртуальной сети, он не является общедоступным, а доступен только для виртуальных машин и приложений в этой виртуальной сети. |Audit, Deny, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Использование только безопасных подключений к кэшу Azure для Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Аудит активации только подключений по протоколу SSL к кэшу Azure для Redis. Использование безопасных подключений обеспечивает проверку подлинности между сервером и службой, а также защищает переносимые данные от атак сетевого уровня, таких как "злоумышленник в середине", прослушивание трафика и перехват сеанса. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
