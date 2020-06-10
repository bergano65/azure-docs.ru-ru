---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/29/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 504005d9d0a037640a959ca4861971d0ca90823c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234035"
---
|Имя |Описание |Политики |Версия |
|---|---|---|---|
|[Включение Azure Monitor для масштабируемых наборов виртуальных машин](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Включите Azure Monitor для масштабируемых наборов виртуальных машин в указанной области (группа управления, подписка или группа ресурсов). В качестве параметра принимается рабочая область Log Analytics. Примечание. Если параметру upgradePolicy масштабируемого набора присвоено значение Manual, необходимо применить расширение для всех виртуальных машин в наборе, вызвав их обновление. В интерфейсе командной строки для этого потребовалось бы ввести команду az vmss update-instances. |6 |1.0.1 |
|[Включение Azure Monitor для виртуальных машин](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Включение Azure Monitor для виртуальных машин (ВМ) в указанной области (группа управления, подписка или группа ресурсов). В качестве параметра принимается рабочая область Log Analytics. |10 |2.0.0 |
