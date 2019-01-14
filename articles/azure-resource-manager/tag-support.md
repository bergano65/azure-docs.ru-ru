---
title: Поддержка тегов Azure Resource Manager для ресурсов
description: В этой статье описываются типы ресурсов Azure, поддерживающие теги, а также приводятся сведения о всех службах Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/02/2019
ms.author: tomfitz
ms.openlocfilehash: 50ea7a2446b5560bd208b2da128fa877068ce452
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000297"
---
# <a name="tag-support-for-azure-resources"></a>Поддержка тегов для ресурсов Azure
В этой статье приводятся сведения о поддержке типом ресурса функции [добавления тегов](resource-group-using-tags.md).

## <a name="aad-domain-services"></a>Доменные службы AAD
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| domains | Нет  | 

## <a name="ad-hybrid-health-service"></a>служба работоспособности гибридного AD;
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| addsservices | Нет  |
| aadsupportcases | Нет  | 
| agents | Нет  | 
| anonymousapiusers | Нет  | 
| Конфигурация | Нет  | 
| журналы | Нет  | 
| reports | Нет  | 
| services; | Нет  | 
| servicehealthmetrics | Нет  | 

## <a name="aks"></a>AKS
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| managedClusters | Yes | 

## <a name="analysis-services"></a>Analysis Services
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| servers | Yes | 

## <a name="api-hubs"></a>Концентраторы API
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| apiManagementAccounts | Нет  | 
| apiManagementAccounts/apis | Нет  | 
| apiManagementAccounts/connectionAcls | Нет  | 
| apiManagementAccounts/connectionProviders | Нет  | 
| apiManagementAccounts/connectionProviderAcls | Нет  | 
| apiManagementAccounts/connections | Нет  | 

## <a name="api-management"></a>Управление API
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| свойства | Yes | 

## <a name="automation"></a>Служба автоматизации
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| automationAccounts | Yes | 
| automationAccounts/configurations | Yes | 
| automationAccounts/jobs | Нет  | 
| automationAccounts/runbooks | Yes | 
| automationAccounts/softwareUpdateConfigurations | Нет  | 
| automationAccounts/webhooks | Нет  | 

## <a name="azure-database-for-mariadb"></a>База данных Azure для MariaDB
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| servers | Yes | 
| servers/configurations | Нет  |
| servers/databases | Нет  |
| servers/firewallRules | Нет  |
| servers/recoverableServers | Нет  | 
| servers/securityAlertPolicies | Нет  |
| servers/virtualNetworkRules | Нет  | 

## <a name="azure-database-for-mysql"></a>База данных Azure для MySQL
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| servers | Yes | 
| servers/configurations | Нет  |
| servers/databases | Нет  |
| servers/firewallRules | Нет  |
| servers/recoverableServers | Нет  | 
| servers/securityAlertPolicies | Нет  |
| servers/virtualNetworkRules | Нет  | 

## <a name="azure-database-for-postgresql"></a>База данных Azure для PostgreSQL
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| servers | Yes | 
| servers/advisors | Нет  | 
| servers/configurations | Нет  |
| servers/databases | Нет  |
| servers/firewallRules | Нет  |
| servers/queryTexts | Нет  | 
| servers/recoverableServers | Нет  | 
| servers/securityAlertPolicies | Нет  |
| servers/topQueryStatistics | Нет  | 
| servers/virtualNetworkRules | Нет  | 
| servers/waitStatistics | Нет  | 

## <a name="batch"></a>Пакетная служба Azure
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| batchAccounts | Yes | 

## <a name="bing-maps"></a>Карты Bing
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| mapApis | Yes | 

## <a name="biztalk-services"></a>Службы BizTalk
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| BizTalk | Yes | 

## <a name="cache"></a>Кэш
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| Redis | Yes | 

## <a name="cdn"></a>CDN
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| edgenodes | Нет  | 
| профили | Yes | 
| profiles/endpoints | Yes | 
| profiles/endpoints/customdomains | Нет  | 
| profiles/endpoints/origins | Нет  | 
| validateProbe | Нет  | 

## <a name="classic-compute"></a>Классические вычислительные ресурсы
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| domainNames | Нет  | 
| domainNames/slots | Нет  | 
| domainNames/slots/roles | Нет  | 
| virtualMachines | Нет  | 
| virtualMachines/diagnosticSettings | Нет  | 
| virtualMachines/metricDefinitions | Нет  | 
| virtualMachines/metrics | Нет  | 

## <a name="classic-infrastructure-migrate"></a>Миграция классической инфраструктуры
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| classicInfrastructureResources | Нет  | 

## <a name="classic-network"></a>Классическая сеть
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| virtualNetworks | Нет  | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Нет  | 
| virtualNetworks/virtualNetworkPeerings | Нет  | 

## <a name="classic-storage"></a>Классическая служба хранилища
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| storageAccounts/services | Нет  | 
| storageAccounts/services/diagnosticSettings | Нет  | 

## <a name="compute"></a>Службы вычислений
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| availabilitySets | Yes | 
| disks | Yes | 
| images | Yes | 
| restorePointCollections | Yes | 
| restorePointCollections/restorePoints | Нет  | 
| sharedVMImages | Yes | 
| sharedVMImages/versions | Yes | 
| snapshots | Yes | 
| virtualMachines | Yes | 
| virtualMachines/diagnosticSettings | Нет  | 
| virtualMachines/extensions | Yes | 
| virtualMachines/metricDefinitions | Нет  | 
| virtualMachineScaleSets | Yes | 
| virtualMachineScaleSets/extensions | Нет  | 
| virtualMachineScaleSets/networkInterfaces | Нет  | 
| virtualMachineScaleSets/publicIPAddresses | Нет  | 
| virtualMachineScaleSets/virtualMachines | Нет  | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Нет  | 

## <a name="container"></a>Контейнер
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| containerGroups | Yes | 

## <a name="container-instance"></a>Экземпляр контейнера
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| containerGroups | Yes | 
| serviceAssociationLinks | Нет  | 

## <a name="container-registry"></a>Реестр контейнеров
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| registries | Yes | 
| registries/replications | Yes |
| registries/tasks | Yes |
| registries/webhooks | Yes |

## <a name="container-service"></a>Служба контейнеров
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| containerServices | Yes | 

## <a name="cortana-analytics"></a>Cortana Analytics
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="cosmos-db"></a>База данных Cosmos
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| databaseAccounts | Yes | 
| databaseAccountNames | Нет  | 

## <a name="cost-management"></a>Управление затратами
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| Соединители | Yes | 

## <a name="data-box"></a>Data Box
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| jobs | Yes | 

## <a name="data-box-edge"></a>Data Box Edge
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| DataBoxEdgeDevices | Yes | 

## <a name="data-catalog"></a>Каталог данных
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| catalogs | Yes | 

## <a name="data-connect"></a>Подключение к данным
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| connectionManagers | Yes | 

## <a name="data-factory"></a>Фабрика данных
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| dataFactories | Yes | 
| dataFactories/diagnosticSettings | Нет  | 
| dataFactories/metricDefinitions | Нет  | 
| dataFactorySchema | Нет  | 
| factories | Yes | 
| factories/integrationRuntimes | Нет  | 

## <a name="devices"></a>Устройства
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| IotHubs | Yes | 
| IotHubs/eventGridFilters | Нет  | 
| ProvisioningServices | Yes | 

## <a name="devspaces"></a>Devspaces
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| controllers | Yes | 

## <a name="devtest-lab"></a>DevTest Lab
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| labs | Yes | 
| labs/artifactsources | Yes |
| labs/costs | Yes |
| labs/customimages | Yes |
| labs/formulas | Yes |
| labs/notificationchannels | Yes |
| labs/policysets/policies | Yes |
| labs/schedules | Yes |
| labs/serviceRunners | Yes | 
| labs/users | Yes |
| labs/users/disks | Yes |
| labs/users/environments | Yes |
| labs/users/secrets | Yes |
| labs/users/servicefabrics | Yes |
| labs/users/servicefabrics/schedules | Yes |
| labs/virtualMachines | Yes | 
| labs/virtualmachines/schedules | Yes |
| labs/virtualnetworks | Yes |
| schedules | Yes | 

## <a name="dynamics-lcs"></a>Dynamics LCS.
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| lcsprojects | Нет  | 
| lcsprojects/connectors | Нет  | 
| lcsprojects/clouddeployments | Нет  | 

## <a name="event-grid"></a>Сетка событий Azure
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| domains | Yes | 
| domains/topics | Нет  | 
| eventSubscriptions | Нет  | 
| extensionTopics | Нет  | 
| topics | Yes | 
| topicTypes | Нет  | 

## <a name="event-hub"></a>Концентратор событий
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| clusters | Yes | 
| namespaces | Yes | 
| namespaces/AuthorizationRules | Нет  |
| namespaces/disasterRecoveryConfigs | Нет  |
| namespaces/eventhubs | Нет  |
| namespaces/eventhubs/authorizationRules | Нет  |
| namespaces/eventhubs/consumergroups | Нет  |

## <a name="hana-on-azure"></a>HANA в Azure
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| hanaInstances | Yes | 

## <a name="hdinsight"></a>HDInsight
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| clusters | Yes | 
| clusters/applications | Нет  | 

## <a name="import-export"></a>Импорт и экспорт
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| jobs | Yes | 

## <a name="insights"></a>Аналитика
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| actionGroups | Yes |
| activityLogAlerts | Yes |
| alertrules | Yes |
| automatedExportSettings | Нет  | 
| components | Yes | 
| components/events | Нет  | 
| components/metrics | Нет  | 
| components/pricingPlans | Нет  | 
| components/query | Нет  | 
| журналы | Нет  | 
| metricAlerts | Yes |
| migrateToNewPricingModel | Нет  | 
| myWorkbooks | Нет  | 
| Запросы | Нет  | 
| rollbackToLegacyPricingModel | Нет  | 
| scheduledqueryrules | Yes | 
| webtests | Yes | 
| workbooks | Yes | 

## <a name="key-vault"></a>Key Vault
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| deletedVaults | Нет  | 
| vaults | Yes | 
| vaults/accessPolicies | Нет  | 
| vaults/secrets | Нет  | 

## <a name="log-analytics"></a>Log Analytics
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| журналы | Нет  | 

## <a name="logic"></a>Логика
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| integrationAccounts | Yes | 
| workflows | Yes | 

## <a name="machine-learning-services"></a>Службы машинного обучения
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| workspaces | Yes | 
| workspaces/computes | Нет  | 

## <a name="managed-identity"></a>Управляемое удостоверение
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| Identities | Нет  | 
| userAssignedIdentities | Yes | 

## <a name="marketplace-apps"></a>Приложения Microsoft Azure Marketplace
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| classicDevServices | Yes | 

## <a name="marketplace-ordering"></a>Упорядочение Microsoft Azure Marketplace
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| agreements | Нет  | 
| offertypes | Нет  | 

## <a name="media"></a>Служба мультимедиа
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| mediaservices | Yes | 
| mediaservices/accountFilters | Нет  | 
| mediaservices/assets | Нет  | 
| mediaservices/assets/assetFilters | Нет  | 
| mediaservices/contentKeyPolicies | Нет  | 
| mediaservices/eventGridFilters | Нет  | 
| mediaservices/liveEventOperations | Нет  | 
| mediaservices/liveEvents | Yes | 
| mediaservices/liveEvents/liveOutputs | Нет  | 
| mediaservices/liveOutputOperations | Нет  | 
| mediaservices/streamingEndpoints | Yes | 
| mediaservices/streamingEndpointOperations | Нет  | 
| mediaservices/streamingLocators | Нет  | 
| mediaservices/streamingPolicies | Нет  | 
| mediaservices/transforms | Нет  | 
| mediaservices/transforms/jobs | Нет  | 

## <a name="network"></a>Сеть
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| applicationGateways | Yes | 
| applicationSecurityGroups | Yes | 
| azureFirewalls | Yes | 
| connections | Yes | 
| ddosProtectionPlans | Yes | 
| expressRouteCircuits | Yes | 
| frontdoors | Yes | 
| frontdoorWebApplicationFirewallPolicies | Yes | 
| interfaceEndpoints | Yes | 
| loadBalancers | Yes | 
| localNetworkGateways | Yes | 
| networkIntentPolicies | Yes | 
| networkInterfaces | Yes | 
| networkProfiles | Yes | 
| networkSecurityGroups | Yes | 
| networkWatchers | Yes | 
| networkWatchers/connectionMonitors | Yes | 
| networkWatchers/lenses | Yes | 
| networkWatchers/pingMeshes | Yes | 
| privateLinkServices | Yes | 
| publicIPAddresses | Yes | 
| publicIPPrefixes | Yes | 
| routeFilters | Yes | 
| routeTables | Yes | 
| serviceEndpointPolicies | Yes | 
| virtualHubs | Yes | 
| virtualNetworks | Yes | 
| virtualNetworkGateways | Yes | 
| virtualNetworkTaps | Yes | 
| virtualWans | Yes | 
| vpnGateways | Yes | 
| vpnSites | Yes | 
| webApplicationFirewallPolicies | Yes | 

## <a name="notification-hubs"></a>Центры уведомлений
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| namespaces | Yes | 
| namespaces/notificationHubs | Yes | 

## <a name="operational-insights"></a>Operational Insights;
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| workspaces | Yes |
| workspaces/dataSources | Yes |
| workspaces/linkedServices | Yes |
| workspaces/savedSearches | Нет  |
| workspaces/storageInsightConfigs | Yes |

## <a name="operations-management"></a>Пакет Operations Management
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| solutions | Нет  |

## <a name="portal"></a>Microsoft Azure
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| dashboards | Yes | 

## <a name="portal-sdk"></a>Пакет средств разработки портала
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| rootResources | Yes | 

## <a name="power-bi"></a>Power BI
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| workspaceCollections | Yes | 

## <a name="recovery-services"></a>Службы восстановления
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| backupProtectedItems | Нет  | 
| vaults | Yes | 

## <a name="relay"></a>Передача
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| namespaces | Yes | 

## <a name="resources"></a>Ресурсы
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| resourceGroups | Yes | 
| subscriptions/resourceGroups | Yes | 

## <a name="scheduler"></a>Планировщик
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| jobcollections | Yes | 
| flows | Yes | 

## <a name="search"></a>поиска
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| resourceHealthMetadata | Нет  | 
| searchServices | Yes | 

## <a name="security"></a>Безопасность
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| dataCollectionAgents | Нет  | 

## <a name="service-bus"></a>Служебная шина Azure
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| namespaces | Yes | 
| namespaces/eventgridfilters | Нет  | 

## <a name="service-fabric"></a>Service Fabric
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| clusters | Yes | 
| clusters/applications | Нет  | 

## <a name="service-fabric-mesh"></a>Служба Microsoft Azure Service Fabric
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| веб-масштабированием; | Yes | 
| networks | Yes | 
| volumes. | Yes | 

## <a name="signalr-service"></a>Служба SignalR
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| SignalR | Yes | 

## <a name="site-recovery"></a>Site Recovery
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| SiteRecoveryVault | Yes | 

## <a name="solutions"></a>Решения
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| веб-масштабированием; | Yes | 
| applicationDefinitions | Yes | 
| jitRequests | Yes | 

## <a name="sql"></a>SQL
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| locations/instanceFailoverGroups | Нет  |
| managedInstances | Yes |
| managedInstances/databases | Yes |
| managedInstances/databases/backupShortTermRetentionPolicies | Нет  |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Нет  |
| managedInstances/databases/vulnerabilityAssessments | Нет  |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Нет  |
| managedInstances/encryptionProtector | Нет  |
| managedInstances/keys | Нет  |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Нет  |
| managedInstances/vulnerabilityAssessments | Нет  |
| servers | Yes |
| servers/administrators | Нет  |
| servers/advisors | Нет  |
| servers/auditingSettings | Нет  |
| servers/backupLongTermRetentionVaults | Нет  |
| servers/communicationLinks | Нет  |
| servers/connectionPolicies | Нет  |
| servers/databases | Yes |
| servers/databases/advisors | Нет  |
| servers/databases/auditingSettings | Нет  |
| servers/databases/backupLongTermRetentionPolicies | Нет  |
| servers/databases/backupShortTermRetentionPolicies | Нет  |
| servers/databases/connectionPolicies | Нет  |
| servers/databases/dataMaskingPolicies | Нет  |
| servers/databases/dataMaskingPolicies/rules | Нет  |
| servers/databases/extendedAuditingSettings | Нет  |
| servers/databases/extensions | Нет  |
| servers/databases/geoBackupPolicies | Нет  |
| servers/databases/schemas/tables/columns/sensitivityLabels | Нет  |
| servers/databases/securityAlertPolicies | Нет  |
| servers/databases/syncGroups | Нет  |
| servers/databases/syncGroups/syncMembers | Нет  |
| servers/databases/transparentDataEncryption | Нет  |
| servers/databases/vulnerabilityAssessments | Нет  |
| servers/databases/vulnerabilityAssessments/rules/baselines | Нет  |
| servers/disasterRecoveryConfiguration | Нет  |
| servers/dnsAliases | Нет  |
| servers/elasticPools | Yes |
| servers/encryptionProtector | Нет  |
| servers/extendedAuditingSettings | Нет  |
| servers/failoverGroups | Yes |
| servers/firewallRules | Нет  |
| servers/jobAgents | Yes |
| servers/jobAgents/credentials | Нет  |
| servers/jobAgents/jobs | Нет  |
| servers/jobAgents/jobs/executions | Нет  |
| servers/jobAgents/jobs/steps | Нет  |
| servers/jobAgents/targetGroups | Нет  |
| servers/keys | Нет  |
| servers/securityAlertPolicies | Нет  |
| servers/syncAgents | Нет  |
| servers/virtualNetworkRules | Нет  |
| servers/vulnerabilityAssessments | Нет  |

## <a name="sql-virtual-machine"></a>Виртуальная машина SQL
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| DWVM | Yes | 

## <a name="storage"></a>Хранилище
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| storageAccounts | Yes | 
| storageAccounts/blobServices | Нет  | 
| storageAccounts/fileServices | Нет  | 
| storageAccounts/queueServices | Нет  | 
| storageAccounts/services | Нет  | 
| storageAccounts/services/metricDefinitions | Нет  | 
| storageAccounts/tableServices | Нет  | 

## <a name="storage-sync"></a>Синхронизация службы хранилища
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| storageSyncServices | Yes | 
| storageSyncServices/registeredServers | Нет  | 
| storageSyncServices/syncGroups | Нет  | 
| storageSyncServices/syncGroups/cloudEndpoints | Нет  | 
| storageSyncServices/syncGroups/serverEndpoints | Нет  | 
| storageSyncServices/workflows | Нет  | 

## <a name="storsimple"></a>StorSimple
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| managers | Yes | 

## <a name="stream-analytics"></a>Stream Analytics
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| streamingjobs | Yes | 
| streamingjobs/diagnosticSettings | Нет  | 
| streamingjobs/metricDefinitions | Нет  | 

## <a name="subscription"></a>Подписка
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| SubscriptionDefinitions | Нет  | 
| SubscriptionOperations | Нет  | 

## <a name="support"></a>Поддержка
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| supporttickets | Нет  | 

## <a name="visual-studio"></a>Visual Studio
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| учетная запись | Yes | 
| account/extension | Yes | 
| account/project | Yes | 

## <a name="web"></a>Web
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| apiManagementAccounts | Нет  | 
| apiManagementAccounts/apiAcls | Нет  | 
| apiManagementAccounts/apis | Нет  | 
| apiManagementAccounts/apis/apiAcls | Нет  | 
| apiManagementAccounts/apis/connectionAcls | Нет  | 
| apiManagementAccounts/apis/connections | Нет  | 
| apiManagementAccounts/apis/connections/connectionAcls | Нет  | 
| apiManagementAccounts/apis/localizedDefinitions | Нет  | 
| apiManagementAccounts/connectionAcls | Нет  | 
| apiManagementAccounts/connections | Нет  | 
| billingMeters | Нет  | 
| certificates | Yes | 
| connectionGateways | Yes | 
| connections | Yes | 
| customApis | Yes | 
| deletedSites | Нет  | 
| functions | Нет  | 
| hostingEnvironments | Yes | 
| hostingEnvironments/metrics | Нет  | 
| hostingEnvironments/multiRolePools | Нет  | 
| hostingEnvironments/workerPools | Нет  | 
| publishingUsers | Нет  | 
| serverFarms | Yes | 
| serverFarms/workers | Нет  | 
| sites | Yes | 
| sites/domainOwnershipIdentifiers | Нет  | 
| sites/hostNameBindings | Нет  | 
| sites/instances | Нет  | 
| sites/instances/extensions | Нет  | 
| sites/metrics | Нет  | 
| sites/premieraddons | Yes | 
| sites/slots | Yes | 
| sites/slots/hostNameBindings | Нет  | 
| sites/slots/instances | Нет  | 
| sites/slots/instances/extensions | Нет  | 
| sites/slots/metrics | Нет  | 
| sourceControls | Нет  | 
| validate | Нет  | 
| verifyHostingEnvironmentVnet | Нет  | 

## <a name="xrm"></a>XRM
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| organizations | Нет  | 

## <a name="next-steps"></a>Дополнительная информация
Сведения о том, как применить теги к ресурсам, см. в статье, посвященной [использованию тегов для организации ресурсов Azure](resource-group-using-tags.md).
