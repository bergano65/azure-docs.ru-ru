---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 645494688ed1fb2a29efa083d50e9456f6d2b1b4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170072"
---
|Имя |Описание |Действие |Версия |
|---|---|---|---|
|[В Azure Data Lake Store должны быть включены журналы диагностики](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |Аудит активации журналов диагностики. Это позволяет воссоздать следы действий для анализа инцидентов безопасности или случаев компрометации сети. |AuditIfNotExists, Disabled |2.0.0 |
|[В Data Lake Analytics должны быть включены журналы диагностики](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |Аудит активации журналов диагностики. Это позволяет воссоздать следы действий для анализа инцидентов безопасности или случаев компрометации сети. |AuditIfNotExists, Disabled |2.0.0 |
|[Требовать шифрование учетных записей Data Lake Store](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |Эта политика активирует шифрование во всех учетных записях Data Lake Storage |deny |1.0.0 |
