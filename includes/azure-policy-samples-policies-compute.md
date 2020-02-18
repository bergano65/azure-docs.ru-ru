---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 667d017e8febcf1abcc1cfe21ecfaa43ae75ea6d
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170102"
---
|Имя |Описание |Действие |Версия |
|---|---|---|---|
|[Разрешенные номера SKU виртуальных машин](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json) |Эта политика позволяет задать набор SKU виртуальных машин, которые может развертывать ваша организация. |Запрет |1.0.0 |
|[Аудит виртуальных машин без аварийного восстановления](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |Аудит виртуальных машин, на которых не настроено аварийное восстановление. Дополнительные сведения об аварийном восстановлении см. здесь: https://aka.ms/asr-doc. |auditIfNotExists |1.0.0 |
|[Аудит виртуальных машин, которые не используют управляемые диски](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |Эта политика выполняет аудит виртуальных машин, которые не используют управляемые диски. |аудит |1.0.0 |
|[Развертывание расширения IaaSAntimalware (Майкрософт) по умолчанию для Windows Server](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |Эта политика развертывает расширение IaaSAntimalware (Майкрософт) с конфигурацией по умолчанию, если для виртуальной машины не настроено расширение защиты от вредоносных программ. |deployIfNotExists |1.0.0 |
|[В масштабируемых наборах виртуальных машин должны быть включены журналы диагностики](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |Мы рекомендуем включить журналы для воссоздания следов действий и проведения исследования в случае инцидента или компрометации. |AuditIfNotExists, Disabled |1.0.0 |
|[В Microsoft Antimalware для Azure должно быть настроено автоматическое обновление подписей защиты](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |Эта политика выполняет аудит всех виртуальных машин Windows, на которых не настроено автоматическое обновление подписей защиты Microsoft Antimalware. |AuditIfNotExists, Disabled |1.0.0 |
|[Расширение Microsoft IaaSAntimalware должно быть развернуто на серверах Windows](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |Эта политика выполняет аудит всех виртуальных машин Windows Server, на которых не развернуто расширение IaaSAntimalware (Майкрософт). |AuditIfNotExists, Disabled |1.0.0 |
|[Должны быть установлены только утвержденные расширения виртуальных машин](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |Эта политика управляет неутвержденными расширениями виртуальных машин. |Audit, Deny, Disabled |1.0.0 |
|[Требовать автоматическое исправление образов ОС в масштабируемых наборах виртуальных машин](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json) |Эта политика обеспечивает принудительное автоматическое применение исправлений к образам ОС в масштабируемых наборах виртуальных машин. Это позволяет поддерживать защиту виртуальных машин за счет ежемесячного применения последних обновлений для системы безопасности. |deny |1.0.0 |
|[Неподключенные диски должны быть зашифрованы](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |Эта политика выполняет аудит всех неподключенных дисков, на которых не активировано шифрование. |Audit, Disabled |1.0.0 |
|[Виртуальные машины должны быть перенесены на новые ресурсы Azure Resource Manager](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |Используйте для своих виртуальных машин новую версию Azure Resource Manager с улучшенными функциями безопасности: более строгим контролем доступа (RBAC), улучшенным аудитом, развертыванием и управлением на основе ARM, доступом к управляемым удостоверениям, доступом к хранилищу ключей для секретов, проверкой подлинности на основе Azure AD, а также поддержкой тегов и групп ресурсов, которые упрощают управление защитой. |Audit, Deny, Disabled |1.0.0 |
