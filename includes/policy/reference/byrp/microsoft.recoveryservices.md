---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1b06c20e4c8cad1421bc328ff40570b3fa4a7572
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310717"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Необходимо включить Azure Backup для Виртуальных машин](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Обеспечьте защиту виртуальных машин Azure, включив Azure Backup. Azure Backup — это безопасное и экономичное решение для защиты данных в Azure. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Настройка резервного копирования виртуальных машин в существующем в том же расположении центральном хранилище](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Эта политика настраивает защиту Azure Backup для виртуальных машин в указанном расположении с имеющимся центральным хранилищем в том же расположении. Она применяется только к тем виртуальным машинам, для которых еще не настроено резервное копирование. Рекомендуется, чтобы число виртуальных машин, которым назначается эта политика, не превышало 200. Если политика назначена для более чем 200 виртуальных машин, это может привести к запуску архивации на несколько часов позже заданного расписания. Эта политика будет улучшена для поддержки большего числа образов виртуальных машин. |deployIfNotExists, auditIfNotExists, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[Развертывание параметров диагностики для потоковой передачи данных из хранилища Служб восстановления в рабочую область Log Analytics по определенным категориям ресурсов.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |Развертывание параметров диагностики для потоковой передачи данных из хранилища Служб восстановления в рабочую область Log Analytics по определенным категориям ресурсов. Если какая-то из категорий не включена, создается новый параметр диагностики. |deployIfNotExists |[1.0.1 (предварительная версия)](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
