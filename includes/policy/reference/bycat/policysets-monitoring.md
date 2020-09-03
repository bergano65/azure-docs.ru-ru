---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 17eb8f6d5d2cf8b3ec53eb0fd940594645b9e28d
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298134"
---
|Имя |Описание |Политики |Версия |
|---|---|---|---|
|[Включение Azure Monitor для масштабируемых наборов виртуальных машин](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Включите Azure Monitor для масштабируемых наборов виртуальных машин в указанной области (группа управления, подписка или группа ресурсов). В качестве параметра принимается рабочая область Log Analytics. Примечание. Если параметру upgradePolicy масштабируемого набора присвоено значение Manual, необходимо применить расширение для всех виртуальных машин в наборе, вызвав их обновление. В интерфейсе командной строки для этого потребовалось бы ввести команду az vmss update-instances. |6 |1.0.1 |
|[Включение Azure Monitor для виртуальных машин](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Включение Azure Monitor для виртуальных машин (ВМ) в указанной области (группа управления, подписка или группа ресурсов). В качестве параметра принимается рабочая область Log Analytics. |10 |2.0.0 |
