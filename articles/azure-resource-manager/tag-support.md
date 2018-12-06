---
title: Поддержка тегов Azure Resource Manager для ресурсов
description: В этой статье описываются типы ресурсов Azure, поддерживающие теги, а также приводятся сведения о всех службах Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/20/2018
ms.author: tomfitz
ms.openlocfilehash: a4bb423dc5eddde0fd2d2b9b4f263ab39dbd801f
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284988"
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
| services; | Нет  | 
| addsservices | Нет  | 
| Конфигурация | Нет  | 
| agents | Нет  | 
| aadsupportcases | Нет  | 
| reports | Нет  | 
| servicehealthmetrics | Нет  | 
| журналы | Нет  | 
| anonymousapiusers | Нет  | 

## <a name="analysis-services"></a>Analysis Services
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| servers | Yes | 

## <a name="api-hubs"></a>Концентраторы API
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| apiManagementAccounts | Нет  | 
| apiManagementAccounts/connectionProviders | Нет  | 
| apiManagementAccounts/connections | Нет  | 
| apiManagementAccounts/connectionAcls | Нет  | 
| apiManagementAccounts/connectionProviderAcls | Нет  | 
| apiManagementAccounts/apis | Нет  | 

## <a name="api-management"></a>Управление API
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| свойства | Yes | 

## <a name="automation"></a>Служба автоматизации
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| automationAccounts | Yes | 
| automationAccounts/runbooks | Yes | 
| automationAccounts/configurations | Yes | 
| automationAccounts/webhooks | Нет  | 
| automationAccounts/softwareUpdateConfigurations | Нет  | 
| automationAccounts/jobs | Нет  | 

## <a name="batch"></a>Пакетная служба Azure
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| batchAccounts | Yes | 

## <a name="batch-ai"></a>Искусственный интеллект пакетной службы
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| clusters | Yes | 
| jobs | Yes | 
| fileservers | Yes | 
| workspaces | Yes | 
| workspaces/clusters | Нет  | 
| workspaces/fileservers | Нет  | 
| workspaces/experiments | Нет  | 
| workspaces/experiments/jobs | Нет  | 

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
| профили | Yes | 
| profiles/endpoints | Yes | 
| profiles/endpoints/origins | Нет  | 
| profiles/endpoints/customdomains | Нет  | 
| validateProbe | Нет  | 
| edgenodes | Нет  | 

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
| virtualNetworks/virtualNetworkPeerings | Нет  | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Нет  | 

## <a name="classic-storage"></a>Классическая служба хранилища
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| storageAccounts/services | Нет  | 
| storageAccounts/services/diagnosticSettings | Нет  | 

## <a name="compute"></a>Службы вычислений
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| availabilitySets | Yes | 
| virtualMachines | Yes | 
| virtualMachines/extensions | Yes | 
| virtualMachineScaleSets | Yes | 
| virtualMachineScaleSets/extensions | Нет  | 
| virtualMachineScaleSets/virtualMachines | Нет  | 
| virtualMachineScaleSets/networkInterfaces | Нет  | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Нет  | 
| virtualMachineScaleSets/publicIPAddresses | Нет  | 
| restorePointCollections | Yes | 
| restorePointCollections/restorePoints | Нет  | 
| virtualMachines/diagnosticSettings | Нет  | 
| virtualMachines/metricDefinitions | Нет  | 
| sharedVMImages | Yes | 
| sharedVMImages/versions | Yes | 
| disks | Yes | 
| snapshots | Yes | 
| images | Yes | 

## <a name="container"></a>Контейнер
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| containerGroups | Yes | 

## <a name="container-instance"></a>Экземпляр контейнера
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| containerGroups | Yes | 
| serviceAssociationLinks | Нет  | 

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
| factories | Yes | 
| factories/integrationRuntimes | Нет  | 
| dataFactories/diagnosticSettings | Нет  | 
| dataFactories/metricDefinitions | Нет  | 
| dataFactorySchema | Нет  | 

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
| schedules | Yes | 
| labs/virtualMachines | Yes | 
| labs/serviceRunners | Yes | 

## <a name="dynamics-lcs"></a>Dynamics LCS.
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| lcsprojects | Нет  | 
| lcsprojects/connectors | Нет  | 
| lcsprojects/clouddeployments | Нет  | 

## <a name="event-grid"></a>Сетка событий Azure
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| eventSubscriptions | Нет  | 
| topics | Yes | 
| domains | Yes | 
| domains/topics | Нет  | 
| topicTypes | Нет  | 
| extensionTopics | Нет  | 

## <a name="event-hub"></a>Концентратор событий
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| namespaces | Yes | 
| clusters | Yes | 

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
| components | Yes | 
| components/query | Нет  | 
| components/metrics | Нет  | 
| components/events | Нет  | 
| webtests | Yes | 
| Запросы | Нет  | 
| scheduledqueryrules | Yes | 
| components/pricingPlans | Нет  | 
| migrateToNewPricingModel | Нет  | 
| rollbackToLegacyPricingModel | Нет  | 
| automatedExportSettings | Нет  | 
| workbooks | Yes | 
| myWorkbooks | Нет  | 
| журналы | Нет  | 

## <a name="key-vault"></a>Key Vault
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| vaults | Yes | 
| vaults/secrets | Нет  | 
| vaults/accessPolicies | Нет  | 
| deletedVaults | Нет  | 

## <a name="log-analytics"></a>Log Analytics
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| журналы | Нет  | 

## <a name="logic"></a>Логика
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| workflows | Yes | 
| integrationAccounts | Yes | 

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

## <a name="mariadb"></a>MariaDB
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| servers | Yes | 
| servers/recoverableServers | Нет  | 
| servers/virtualNetworkRules | Нет  | 

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
| mediaservices/assets | Нет  | 
| mediaservices/contentKeyPolicies | Нет  | 
| mediaservices/streamingLocators | Нет  | 
| mediaservices/streamingPolicies | Нет  | 
| mediaservices/eventGridFilters | Нет  | 
| mediaservices/transforms | Нет  | 
| mediaservices/transforms/jobs | Нет  | 
| mediaservices/streamingEndpoints | Yes | 
| mediaservices/liveEvents | Yes | 
| mediaservices/liveEvents/liveOutputs | Нет  | 
| mediaservices/streamingEndpointOperations | Нет  | 
| mediaservices/liveEventOperations | Нет  | 
| mediaservices/liveOutputOperations | Нет  | 
| mediaservices/assets/assetFilters | Нет  | 
| mediaservices/accountFilters | Нет  | 

## <a name="mysql"></a>MySQL
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| servers | Yes | 
| servers/recoverableServers | Нет  | 
| servers/virtualNetworkRules | Нет  | 

## <a name="network"></a>Сеть
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| virtualNetworks | Yes | 
| publicIPAddresses | Yes | 
| networkInterfaces | Yes | 
| interfaceEndpoints | Yes | 
| loadBalancers | Yes | 
| networkSecurityGroups | Yes | 
| applicationSecurityGroups | Yes | 
| serviceEndpointPolicies | Yes | 
| networkIntentPolicies | Yes | 
| routeTables | Yes | 
| publicIPPrefixes | Yes | 
| networkWatchers | Yes | 
| networkWatchers/connectionMonitors | Yes | 
| networkWatchers/lenses | Yes | 
| networkWatchers/pingMeshes | Yes | 
| virtualNetworkGateways | Yes | 
| localNetworkGateways | Yes | 
| connections | Yes | 
| applicationGateways | Yes | 
| expressRouteCircuits | Yes | 
| routeFilters | Yes | 
| virtualWans | Yes | 
| vpnSites | Yes | 
| virtualHubs | Yes | 
| vpnGateways | Yes | 
| azureFirewalls | Yes | 
| virtualNetworkTaps | Yes | 
| privateLinkServices | Yes | 
| ddosProtectionPlans | Yes | 
| networkProfiles | Yes | 
| frontdoors | Yes | 
| frontdoorWebApplicationFirewallPolicies | Yes | 
| webApplicationFirewallPolicies | Yes | 

## <a name="notification-hubs"></a>Центры уведомлений
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| namespaces | Yes | 
| namespaces/notificationHubs | Yes | 

## <a name="portal"></a>Microsoft Azure
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| dashboards | Yes | 

## <a name="portal-sdk"></a>Пакет средств разработки портала
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| rootResources | Yes | 

## <a name="postgresql"></a>PostgreSQL
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| servers | Yes | 
| servers/recoverableServers | Нет  | 
| servers/virtualNetworkRules | Нет  | 
| servers/topQueryStatistics | Нет  | 
| servers/queryTexts | Нет  | 
| servers/waitStatistics | Нет  | 
| servers/advisors | Нет  | 

## <a name="power-bi"></a>Power BI
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| workspaceCollections | Yes | 

## <a name="recovery-services"></a>Службы восстановления
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| vaults | Yes | 
| backupProtectedItems | Нет  | 

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
| searchServices | Yes | 
| resourceHealthMetadata | Нет  | 

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

## <a name="sql-virtual-machine"></a>Виртуальная машина SQL
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| DWVM | Yes | 

## <a name="storage"></a>Хранилище
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| storageAccounts | Yes | 
| storageAccounts/blobServices | Нет  | 
| storageAccounts/tableServices | Нет  | 
| storageAccounts/queueServices | Нет  | 
| storageAccounts/fileServices | Нет  | 
| storageAccounts/services | Нет  | 
| storageAccounts/services/metricDefinitions | Нет  | 

## <a name="storage-sync"></a>Синхронизация службы хранилища
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| storageSyncServices | Yes | 
| storageSyncServices/syncGroups | Нет  | 
| storageSyncServices/syncGroups/cloudEndpoints | Нет  | 
| storageSyncServices/syncGroups/serverEndpoints | Нет  | 
| storageSyncServices/registeredServers | Нет  | 
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
| account/project | Yes | 
| account/extension | Yes | 
| учетная запись | Yes | 
| account/project | Yes | 
| account/extension | Yes | 

## <a name="web"></a>Web
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| sites/instances | Нет  | 
| sites/slots/instances | Нет  | 
| sites/instances/extensions | Нет  | 
| sites/slots/instances/extensions | Нет  | 
| publishingUsers | Нет  | 
| validate | Нет  | 
| sourceControls | Нет  | 
| sites/hostNameBindings | Нет  | 
| sites/domainOwnershipIdentifiers | Нет  | 
| sites/slots/hostNameBindings | Нет  | 
| certificates | Yes | 
| serverFarms | Yes | 
| serverFarms/workers | Нет  | 
| sites | Yes | 
| sites/slots | Yes | 
| sites/metrics | Нет  | 
| sites/slots/metrics | Нет  | 
| sites/premieraddons | Yes | 
| hostingEnvironments | Yes | 
| hostingEnvironments/multiRolePools | Нет  | 
| hostingEnvironments/workerPools | Нет  | 
| hostingEnvironments/metrics | Нет  | 
| functions | Нет  | 
| deletedSites | Нет  | 
| apiManagementAccounts | Нет  | 
| apiManagementAccounts/connections | Нет  | 
| apiManagementAccounts/connectionAcls | Нет  | 
| apiManagementAccounts/apis/connections/connectionAcls | Нет  | 
| apiManagementAccounts/apis/connectionAcls | Нет  | 
| apiManagementAccounts/apiAcls | Нет  | 
| apiManagementAccounts/apis/apiAcls | Нет  | 
| apiManagementAccounts/apis | Нет  | 
| apiManagementAccounts/apis/localizedDefinitions | Нет  | 
| apiManagementAccounts/apis/connections | Нет  | 
| connections | Yes | 
| customApis | Yes | 
| connectionGateways | Yes | 
| billingMeters | Нет  | 
| verifyHostingEnvironmentVnet | Нет  | 

## <a name="xrm"></a>XRM
| Тип ресурса | Поддержка тегов |
| ------------- | ----------- |
| organizations | Нет  | 

## <a name="next-steps"></a>Дополнительная информация
Сведения о том, как применить теги к ресурсам, см. в статье, посвященной [использованию тегов для организации ресурсов Azure](resource-group-using-tags.md).
