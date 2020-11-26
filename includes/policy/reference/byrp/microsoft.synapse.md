---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fb5f6dbb410a15bf88fccc1ed3f9c3ca0f830c70
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "96296099"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Для шифрования неактивных данных рабочие области Azure синапсе должны использовать ключи, управляемые клиентом.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |Используйте управляемые клиентом ключи для управления шифрованием неактивных данных, хранящихся в рабочих областях Azure синапсе. Ключи, управляемые клиентом, обеспечивают двойное шифрование, добавляя второй уровень шифрования поверх шифрования по умолчанию с ключами, управляемыми службой. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[Правила брандмауэра IP-адресов в рабочих областях Azure синапсе должны быть удалены](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56fd377d-098c-4f02-8406-81eb055902b8) |Удаление всех правил брандмауэра IP-адресов повышает безопасность, обеспечивая доступ к рабочей области Azure синапсе только из частной конечной точки. Эта конфигурация выполняет аудит создания правил брандмауэра, которые разрешают доступ к общедоступной сети в рабочей области. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceFirewallRules_Audit.json) |
|[Необходимо включить управляемую виртуальную сеть рабочей области в рабочих областях Azure синапсе](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d9dbfa3-927b-4cf0-9d0f-08747f971650) |Включение управляемой виртуальной сети рабочей области гарантирует, что ваша рабочая область будет изолирована от других рабочих областей. Интеграция данных и ресурсы Spark, развернутые в этой виртуальной сети, также обеспечивают изоляцию на уровне пользователей для действий Spark. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceManagedVnet_Audit.json) |
|[Необходимо включить подключения к частным конечным точкам в рабочих областях Azure синапсе](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Частные конечные точки можно настроить для частного подключения к рабочей области Azure синапсе. Это используется для принудительного применения безопасного канала связи с рабочей областью Azure синапсе. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Управляемые частные конечные точки синапсе должны подключаться только к ресурсам в утвержденных Azure Active Directory клиентах](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a003702-13d2-4679-941b-937e58c443f0) |Защитите рабочую область синапсе, разрешая подключение к ресурсам только утвержденных клиентов Azure Active Directory (Azure AD). Утвержденные клиенты Azure AD можно определить во время назначения политики. |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/Workspace_DataExfiltrationPrevention_Deny.json) |
