---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/19/2020
ms.author: dacoulte
ms.openlocfilehash: 0f2050f42501b0ef55ef30ddc76d3d9c9bf23451
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485035"
---
|Имя |Описание |Действие |Версия |Источник |
|---|---|---|---|
|[Конфигурация приложений должна использовать управляемый клиентом ключ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |Эта политика выполняет аудит экземпляров Конфигурации приложений, не использующих управляемый клиентом ключ. |Audit, Disabled |1.0.0 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json)
|[Конфигурация приложений должна использовать приватный канал](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Эта политика выполняет аудит экземпляров Конфигурации приложений, не использующих приватный канал. |AuditIfNotExists, Disabled |1.0.0 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json)
