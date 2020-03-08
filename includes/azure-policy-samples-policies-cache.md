---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2020
ms.author: dacoulte
ms.openlocfilehash: 8e1d2fae03612c925949a4dcd9cba35b2f4e7f51
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669286"
---
|Имя |Описание |Действие |Версия |GitHub |
|---|---|---|---|---|
|[Должны быть включены только защищенные подключения к кэшу Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Аудит включения только безопасных подключений через SSL к Кэшу Redis. Использование безопасных подключений обеспечивает проверку подлинности между сервером и службой, а также защищает переносимые данные от атак сетевого уровня, таких как "злоумышленник в середине", прослушивание трафика и перехват сеанса. |Audit, Deny, Disabled |1.0.0 |[Ссылка](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json)
