---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b38f33947ceaca40546febc7d73dbf514ffe0c40
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99213859"
---
|Имя |Описание |Политики |Версия |
|---|---|---|---|
|[Развертывание. Настройка необходимых компонентов для включения Azure Monitor и агентов безопасности Azure на виртуальных машинах](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Настройка компьютеров для автоматической установки Azure Monitor и агентов безопасности Azure. Центр безопасности собирает события, поступающие от агентов, и предоставляет на их основе оповещения системы безопасности и специализированные задачи для усиления защиты (рекомендации). Создайте группу ресурсов и рабочую область Log Analytics в том же регионе, где находится компьютер с записями аудита. Эта политика применяется только к виртуальным машинам в нескольких регионах. |5 |1.0.0 (предварительная версия) |
|[Включение Azure Monitor для масштабируемых наборов виртуальных машин](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Включите Azure Monitor для масштабируемых наборов виртуальных машин в указанной области (группа управления, подписка или группа ресурсов). В качестве параметра принимается рабочая область Log Analytics. Примечание. Если параметру upgradePolicy масштабируемого набора присвоено значение Manual, необходимо применить расширение для всех виртуальных машин в наборе, вызвав их обновление. В интерфейсе командной строки для этого потребовалось бы ввести команду az vmss update-instances. |6 |1.0.1 |
|[Включение Azure Monitor для виртуальных машин](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Включение Azure Monitor для виртуальных машин (ВМ) в указанной области (группа управления, подписка или группа ресурсов). В качестве параметра принимается рабочая область Log Analytics. |10 |2.0.0 |
