---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 4a6c0cad4a93d0d99573a348070823b909c75c7e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79299419"
---
|Имя |Описание |Действие |Версия |GitHub |
|---|---|---|---|---|
|[Необходимо включить Azure Backup для Виртуальных машин](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Эта политика помогает проводить аудит, если служба Azure Backup включена для всех виртуальных машин. Azure Backup — это экономичное решение для резервного копирования одним щелчком, которое упрощает восстановление данных и является более простым в использовании, чем другие облачные службы резервного копирования. |AuditIfNotExists, Disabled |1.0.0 |[Ссылка](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Настройка резервного копирования виртуальных машин в существующем в том же расположении центральном хранилище](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Эта политика настраивает защиту Azure Backup для виртуальных машин в указанном расположении с имеющимся центральным хранилищем в том же расположении. Она применяется только к тем виртуальным машинам, для которых еще не настроено резервное копирование. Рекомендуется, чтобы число виртуальных машин, которым назначается эта политика, не превышало 200. Если политика назначена для более чем 200 виртуальных машин, это может привести к запуску архивации на несколько часов позже заданного расписания. Эта политика будет улучшена для поддержки большего числа образов виртуальных машин. |deployIfNotExists, auditIfNotExists, disabled |1.0.0 |[Ссылка](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
