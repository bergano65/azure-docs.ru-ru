---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: aa9320ab4a2ff28c185ecef0f525376ceab4d875
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170202"
---
|Имя |Описание |Действие |Версия |
|---|---|---|---|
|[Из пространства имен Центра событий должны быть удалены все правила авторизации за исключением RootManageSharedAccessKey](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json) |Клиентам концентратора событий не следует использовать политику доступа на уровне пространства имен, которая предоставляет доступ ко всем очередям и разделам в пространстве имен. Для соответствия модели безопасности с наименьшими правами создавайте политики доступа к очередям и разделам на уровне сущности, чтобы предоставлять доступ только к конкретной сущности. |Audit, Deny, Disabled |1.0.1 |
|[Необходимо определить правила авторизации в экземпляре концентратора событий](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json) |Аудит наличия правил авторизации для предоставления доступа с наименьшими правами в сущностях концентратора событий |AuditIfNotExists, Disabled |1.0.0 |
|[Необходимо включить журналы диагностики в концентраторе событий](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |Аудит активации журналов диагностики. Это позволяет воссоздать следы действий для анализа инцидентов безопасности или случаев компрометации сети. |AuditIfNotExists, Disabled |2.0.0 |
