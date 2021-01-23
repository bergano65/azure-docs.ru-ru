---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 722c02dc47dfea1376f216e7f5335051aea85af6
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701332"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Рабочие области Azure Synapse должны использовать ключи, управляемые клиентом, для шифрования неактивных данных](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |Используйте управляемые клиентом ключи для управления шифрованием неактивных данных, хранящихся в рабочих областях Azure Synapse. Кроме того, ключи, управляемые клиентом, обеспечивают двойное шифрование, добавляя второй уровень шифрования поверх заданного по умолчанию способа (с помощью ключей, управляемых службой). |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[Правила брандмауэра для IP-адресов в рабочих областях Azure Synapse должны быть удалены](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56fd377d-098c-4f02-8406-81eb055902b8) |Удаление всех правил брандмауэра для IP-адресов повышает уровень безопасности, обеспечивая доступ к рабочей области Azure Synapse только из частной конечной точки. Эта конфигурация выполняет аудит создания правил брандмауэра, которые разрешают доступ к общедоступной сети в рабочей области. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceFirewallRules_Audit.json) |
|[Управляемая виртуальная сеть рабочей области в рабочих областях Azure Synapse должна быть включена](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d9dbfa3-927b-4cf0-9d0f-08747f971650) |Включение управляемой виртуальной сети рабочей области гарантирует, что ваша рабочая область будет изолирована от других областей. Интеграции данных и ресурсы Spark, развернутые в этой виртуальной сети, также обеспечивают изоляцию на уровне пользователей для действий Spark. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceManagedVnet_Audit.json) |
|[Подключения к частным конечным точкам в рабочих областях Azure Synapse должны быть включены](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Частные конечные точки можно настроить для частного подключения к рабочей области Azure Synapse. Это необходимо для принудительного применения безопасного коммуникационного канала к рабочей области Azure Synapse. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Управляемые частные конечные точки Synapse должны подключаться только к ресурсам в утвержденных клиентах Azure Active Directory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a003702-13d2-4679-941b-937e58c443f0) |Защитите рабочую область Synapse, разрешив подключения только к ресурсам утвержденных клиентов Azure Active Directory. Утвержденные клиенты Azure AD можно определить во время назначения политики. |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/Workspace_DataExfiltrationPrevention_Deny.json) |
