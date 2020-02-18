---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 59a47ca7d3b3b42a5822e61c37ee90cb238c0778
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170052"
---
|Имя |Описание |Действие |Версия |
|---|---|---|---|
|[В учетных записях пакетной службы должны быть включены журналы диагностики](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |Аудит активации журналов диагностики. Это позволяет воссоздать следы действий для анализа инцидентов безопасности или случаев компрометации сети. |AuditIfNotExists, Disabled |2.0.0 |
|[В учетных записях пакетной службы должны быть настроены правила генерации оповещений по метрикам](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json) |Аудит настройки правил оповещений по метрикам в учетной записи пакетной службы для активации нужной метрики |AuditIfNotExists, Disabled |1.0.0 |
