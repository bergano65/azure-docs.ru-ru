---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f3520d90cdcbee77cf8c4989e5575dc0e15601ab
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83637374"
---
|Имя |Описание |Действие |Версия |GitHub |
|---|---|---|---|---|
|[Развертывание параметров диагностики учетной записи пакетной службы в концентраторе событий](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdb51110f-0865-4a6e-b274-e2e07a5b2cd7) |Развертывает параметры диагностики учетной записи пакетной службы для потоковой передачи в региональный концентратор событий при создании или изменении учетной записи пакетной службы, где эти параметры диагностики отсутствуют. |DeployIfNotExists, Disabled |2.0.0 |[Ссылка](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Batch_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Развертывание параметров диагностики учетной записи пакетной службы в рабочей области Log Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc84e5349-db6d-4769-805e-e14037dab9b5) |Развертывает параметры диагностики учетной записи пакетной службы для потоковой передачи в региональную рабочую область Log Analytics при создании или изменении учетной записи пакетной службы, где эти параметры диагностики отсутствуют. |DeployIfNotExists, Disabled |1.0.0 |[Ссылка](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Batch_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[В учетных записях пакетной службы должны быть включены журналы диагностики](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Аудит активации журналов диагностики. Это позволит воссоздать действия для расследования в случае инцидентов безопасности или компрометации сети. |AuditIfNotExists, Disabled |2.0.0 |[Ссылка](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[В учетных записях пакетной службы должны быть настроены правила генерации оповещений по метрикам](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26ee67a2-f81a-4ba8-b9ce-8550bd5ee1a7) |Аудит настройки правил оповещений по метрикам в учетной записи пакетной службы для активации нужной метрики |AuditIfNotExists, Disabled |1.0.0 |[Ссылка](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json) |
