---
title: Типы ресурсов расширения
description: Списки типов ресурсов Azure используются для расширения возможностей других типов ресурсов.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c14cf6fec2da11534a7358599c79961a3258470b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76515371"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Типы ресурсов, расширяющие возможности других ресурсов

Ресурс расширения — это ресурс, который добавляет к возможностям другого ресурса. Например, блокировка ресурсов — это ресурс расширения. Блокировка ресурса на другом ресурсе, чтобы предотвратить его удаление или изменение. Самостоятельно создавать блокировку ресурсов не имеет смысла. Ресурс расширения всегда применяется к другому ресурсу.

## <a name="extension-resource-types"></a>Типы ресурсов расширения

- Microsoft.Advisor/конфигурации
- Microsoft.Advisor/рекомендации
- Microsoft.Advisor/подавления
- Microsoft.AlertsManagement/оповещения
- Microsoft.AlertsManagement/alertsРезюм
- Microsoft.Авторизация/чекДоступ
- Microsoft.Авторизация/отказназначения
- Microsoft.Авторизация/находкаОрторРоназначения
- Microsoft.Authorization/locks
- Microsoft.Authorization/permissions
- Microsoft.Авторизация/политикиНазначения
- Microsoft.Authorization/policyDefinitions
- Microsoft.Authorization/policySetDefinitions
- Microsoft.Authorization/roleAssignments
- Microsoft.Авторизация/рольНазначенияИспользованиеМетри
- Microsoft.Authorization/roleDefinitions
- Microsoft.Billing/billingPeriods
- Microsoft.Billing/billingPermissions
- Microsoft.Billing/billingRoleНазначения
- Microsoft.Billing/billingRoleDefinitions
- Microsoft.Billing/createBillingRoleAssignment
- Microsoft.Blueprint/BlueprintНазначенияs
- Microsoft.Blueprint/чертежи
- Microsoft.Потребление/АгрегированныйСчет
- Microsoft.Потребление/Балансы
- Microsoft.Потребление/Бюджеты
- Microsoft.Потребление/Сборы
- Microsoft.Потребление/CostTags
- Microsoft.Потребление/Прогнозы
- Microsoft.Потребление/Рынки
- Microsoft.Потребление/Результаты операции
- Microsoft.Потребление/ОперацияСтатус
- Microsoft.Потребление/Ценовые листы
- Microsoft.Потребление/ОговоркаПодробности
- Microsoft.Потребление /Рекомендации по бронированию
- Microsoft.Потребление/ReservationSummaries
- Microsoft.Потребление/БронированиеПритс
- Microsoft.Потребление/Теги
- Microsoft.Потребление/Сроки
- Microsoft.Потребление/ИспользованиеПодробности
- Microsoft.Потребление/кредиты
- Microsoft.Потребление/события
- Microsoft.Потребление/лоты
- Microsoft.Потребление/продукты
- Microsoft.Потребление/арендаторы
- Microsoft.ContainerInstance/serviceAssociationLinks
- Microsoft.CostManagement/Оповещения
- Microsoft.CostManagement/Бюджеты
- Microsoft.CostManagement/Dimensions
- Microsoft.CostManagement/Экспорт
- Microsoft.CostManagement/Внешняя подписка
- Microsoft.CostManagement/Forecast
- Microsoft.CostManagement/Запрос
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Отчеты
- Microsoft.CostManagement/Views
- Microsoft.CostManagement/showbackRules
- Microsoft.CustomProviders/associations
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- Microsoft.GuestConfiguration/configurationProfileНазначенияи
- Microsoft.GuestConfiguration/guestConfigurationНазначения
- Microsoft.GuestConfiguration/программное обеспечение
- Microsoft.GuestConfiguration/softwareUpdateProfile
- Microsoft.GuestConfiguration/softwareUpdates
- microsoft.insights/automatedExportSettings
- microsoft.insights/baseline
- microsoft.insights/calculatebaseline
- microsoft.insights/dataCollectionRuleAssociations
- microsoft.insights/diagnosticSettings
- microsoft.insights/diagnosticSettingsCategories
- microsoft.insights/eventtypes
- microsoft.insights/extendedDiagnosticSettings
- microsoft.insights/guestDiagnosticSettingsAssociation
- microsoft.insights/logDefinitions
- microsoft.insights/logs
- microsoft.insights/metricDefinitions
- microsoft.insights/metricNamespaces
- microsoft.insights/metricbaselines
- microsoft.insights/metrics
- microsoft.insights/myWorkbooks
- microsoft.insights/topology
- microsoft.insights/transactions
- microsoft.insights/vmInsightsOnboardingStatuses
- Microsoft.KubernetesConfiguration/источникControlConfigurations
- Microsoft.Maintenance/applyUpdates
- Microsoft.Maintenance/configurationAssignments
- Microsoft.Maintenance/обновления
- Microsoft.ManagedIdentity/Identities
- Microsoft.ManagedServices/registrationНазначения
- Microsoft.ManagedServices/регистрацияОпределения
- Microsoft.OperationalInsights/storageInsightConfigs
- Microsoft.OperationsManagement/управленческиеассоциации
- Microsoft.PolicyInsights/PolicyEvents
- Microsoft.ПолитикаInsights/политикигосударства
- Microsoft.PolicyInsights/policyTrackedРесурсы
- Microsoft.ПолитикаInsights/восстановительные работы
- Microsoft.RecoveryServices/backupProtectedItems
- Microsoft.RecoveryServices/репликацияРезультаты  
- Microsoft.ResourceHealth/availabilityStatuses
- Microsoft.ResourceHealth/childAvailabilityStatuses
- Microsoft.ResourceHealth/childResources
- Microsoft.ResourceHealth/события
- Microsoft.ResourceHealth/impactedРесурсы
- Microsoft.ResourceHealth/уведомления
- Microsoft.Ресурсы/ссылки
- Microsoft.Ресурсы/теги
- Microsoft.Security/Compliances
- Microsoft.Security/InformationProtectionПолитика
- Microsoft.Security/adaptiveNetworkHardenings
- Microsoft.Security/AdvancedThreatProtectionSettings
- Microsoft.Security/оценкаМетадата
- Microsoft.Security/оценки
- Microsoft.Security/complianceResults
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/dataCollectionРезультаты
- Microsoft.Security/deviceSecurityGroups
- Microsoft.Security/networkData
- Microsoft.Security/serverVulnerabilityAssessments
- Microsoft.SecurityInsights/агрегаты
- Microsoft.SecurityInsights/alertRuleTemplates
- Microsoft.SecurityInsights/alertRules
- Microsoft.SecurityInsights/bookmarks
- Microsoft.SecurityInsights/случаи
- Microsoft.SecurityInsights/dataConnectors
- Microsoft.SecurityInsights/организации
- Microsoft.SecurityInsights/entityЗапросы
- Microsoft.SecurityInsights/officeConsents
- Microsoft.SecurityInsights/настройки
- Microsoft.SoftwarePlan/hybridUseBenefits
- Microsoft.Подписка/СозданиеПодписка
- microsoft.support/createsupportticket
- microsoft.support/supporttickets
- Microsoft.WorkloadMonitor/компоненты
- Microsoft.WorkloadMonitor/monitorInstances
- Microsoft.WorkloadMonitor/мониторы
- Microsoft.WorkloadMonitor/уведомления

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы получить идентификатор ресурсов для ресурса расширения в шаблоне Azure Resource Manager, используйте [расширениеResourceId.](../templates/template-functions-resource.md#extensionresourceid)
- Например, создание ресурса расширения в шаблоне см. [Подписки event Event Event](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
