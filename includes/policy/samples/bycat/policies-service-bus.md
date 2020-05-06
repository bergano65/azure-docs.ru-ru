---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 59d8090d1b0557aadc8d7a5af61bb3166075b51b
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838373"
---
|Имя |Description |Действие |Версия |GitHub |
|---|---|---|---|---|
|[Из пространства имен служебной шины должны быть удалены все правила авторизации за исключением RootManageSharedAccessKey](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Клиентам служебной шины не следует использовать политику доступа на уровне пространства имен, которая предоставляет доступ ко всем очередям и разделам в пространстве имен. Для соответствия модели безопасности с наименьшими правами создавайте политики доступа к очередям и разделам на уровне сущности, чтобы предоставлять доступ только к конкретной сущности. |Audit, Deny, Disabled |1.0.1 |[Ссылка](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |
|[В служебной шине должны быть включены журналы диагностики](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Аудит активации журналов диагностики. Это позволит воссоздать действия для расследования в случае инцидентов безопасности или компрометации сети. |AuditIfNotExists, Disabled |2.0.0 |[Ссылка](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
