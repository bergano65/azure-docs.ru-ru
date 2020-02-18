---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 53494bc9642a3df91492d2353a6aa3c6875c1fff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170132"
---
|Имя |Описание |Действие |Версия |
|---|---|---|---|
|[Развернуть параметры диагностики для Key Vault в концентраторе событий](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |Развертывает параметры диагностики для Key Vault для потоковой передачи в региональный концентратор событий при создании или изменении любого ресурса Key Vault, где эти параметры диагностики отсутствуют. |deployIfNotExists |1.0.0 |
|[Журналы диагностики в Key Vault должны быть включены](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |Аудит активации журналов диагностики. Это позволит воссоздать действия для расследования в случае инцидентов безопасности или компрометации сети. |AuditIfNotExists, Disabled |1.0.0 |
|[Объекты Key Vault должны быть восстанавливаемыми](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |Эта политика позволяет проверить наличие невосстанавливаемых объектов Key Vault. Функция обратимого удаления позволяет в течение определенного периода (90 дней) эффективно сохранять ресурсы даже после операции удаления. Ресурсы при этом как будто удалены. Если защита от очистки включена, хранилище или удаленный из него объект нельзя удалить без возможности восстановления в течение 90 дней после первоначального удаления. Эти хранилища и объекты по-прежнему можно восстановить, гарантируя клиентам, что политика хранения будет соблюдена. |Audit, Disabled |1.0.0 |
