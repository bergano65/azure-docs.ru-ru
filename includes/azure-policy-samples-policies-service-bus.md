---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: b3647f02274bdf0efa4cd8079c55be330f28abc9
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170282"
---
|Имя |Описание |Действие |Версия |
|---|---|---|---|
|[Из пространства имен служебной шины должны быть удалены все правила авторизации за исключением RootManageSharedAccessKey](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |Клиентам служебной шины не следует использовать политику доступа на уровне пространства имен, которая предоставляет доступ ко всем очередям и разделам в пространстве имен. Для соответствия модели безопасности с наименьшими правами создавайте политики доступа к очередям и разделам на уровне сущности, чтобы предоставлять доступ только к конкретной сущности. |Audit, Deny, Disabled |1.0.1 |
|[В служебной шине должны быть включены журналы диагностики](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |Аудит активации журналов диагностики. Это позволяет воссоздать следы действий для анализа инцидентов безопасности или случаев компрометации сети. |AuditIfNotExists, Disabled |2.0.0 |
