---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ab9ca05db0e6def896ec242df74ae531d33293bf
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90022133"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Необходимо включить Azure Backup для Виртуальных машин](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Обеспечьте защиту виртуальных машин Azure, включив Azure Backup. Azure Backup — это безопасное и экономичное решение для защиты данных в Azure. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Настройка резервного копирования виртуальных машин в существующем в том же расположении центральном хранилище](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Эта политика настраивает защиту Azure Backup для виртуальных машин в указанном расположении с имеющимся центральным хранилищем в том же расположении. Она применяется только к тем виртуальным машинам, для которых еще не настроено резервное копирование. Рекомендуется, чтобы число виртуальных машин, которым назначается эта политика, не превышало 200. Если политика назначена для более чем 200 виртуальных машин, это может привести к запуску архивации на несколько часов позже заданного расписания. Эта политика будет улучшена для поддержки большего числа образов виртуальных машин. |deployIfNotExists, auditIfNotExists, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[Развертывание параметров диагностики для потоковой передачи данных из хранилища Служб восстановления в рабочую область Log Analytics по определенным категориям ресурсов.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |Развертывание параметров диагностики для потоковой передачи данных из хранилища Служб восстановления в рабочую область Log Analytics по определенным категориям ресурсов. Если какая-то из категорий не включена, создается новый параметр диагностики. |deployIfNotExists |[1.0.1 (предварительная версия)](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
