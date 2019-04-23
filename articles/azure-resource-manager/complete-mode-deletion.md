---
title: Удаление ресурсов в полном режиме в Azure Resource Manager по типу ресурса
description: Здесь показано, как происходит удаление ресурсов в полном режиме в шаблонах Azure Resource Manager по типу ресурса.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 02/13/2019
ms.author: tomfitz
ms.openlocfilehash: fded37fee844a01f4d51518f2ca56dcf575704b2
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60150155"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Удаление ресурсов Azure для развертываний в полном режиме
В этой статье описывается, как процесс удаления будет обрабатываться различными типами, когда он не происходит в шаблоне, развернутом в завершенном режиме.

Типы ресурсов, помеченные как `Yes`, будут удалены, если их тип отсутствует в шаблоне, развернутом в завершенном режиме. 

Типы ресурсов, помеченные как `No`, не будут удалены автоматически, если они отсутствуют в шаблоне, однако они будут удалены после удаления родительского ресурса. Чтобы получить полное описание поведения см. статью [Режимы развертывания в Azure Resource Manager](deployment-modes.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| DomainServices | Yes | 
| DomainServices/oucontainer | Нет  | 

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| diagnosticSettings | Нет  | 
| diagnosticSettingsCategories | Нет  | 

## <a name="microsoftaddons"></a>Microsoft.Addons
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| supportProviders | Нет  | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| aadsupportcases | Нет  | 
| addsservices | Нет  | 
| agents | Нет  | 
| anonymousapiusers | Нет  | 
| Конфигурация | Нет  | 
| журналы | Нет  | 
| reports | Нет  | 
| services; | Нет  | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| конфигурации | Нет  | 
| generateRecommendations | Нет  | 
| к просмотру фильмов | Нет  | 
| suppressions | Нет  | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| actionRules | Нет  | 
| оповещения | Нет  | 
| alertsList | Нет  | 
| alertsSummary | Нет  | 
| alertsSummaryList | Нет  | 
| smartDetectorAlertRules | Нет  | 
| smartDetectorRuntimeEnvironments | Нет  | 
| smartGroups | Нет  | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| servers | Yes | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| reportFeedback | Нет  | 
| свойства | Yes | 
| validateServiceName | Нет  | 

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| attestationProviders | Нет  | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| classicAdministrators | Нет  | 
| denyAssignments | Нет  | 
| elevateAccess | Нет  | 
| locks | Нет  | 
| permissions | Нет  | 
| policyAssignments | Нет  | 
| policyDefinitions | Нет  | 
| policySetDefinitions | Нет  | 
| providerOperations | Нет  | 
| roleAssignments | Нет  | 
| roleDefinitions | Нет  | 

## <a name="microsoftautomation"></a>Microsoft.Automation
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| automationAccounts | Yes | 
| automationAccounts/configurations | Yes | 
| automationAccounts/jobs | Нет  | 
| automationAccounts/runbooks | Yes | 
| automationAccounts/softwareUpdateConfigurations | Нет  | 
| automationAccounts/webhooks | Нет  | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| environments | Нет  | 
| environments/accounts | Нет  | 
| environments/accounts/namespaces | Нет  | 
| environments/accounts/namespaces/configurations | Нет  | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| b2cDirectories | Yes | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| registrations | Yes | 
| registrations/customerSubscriptions | Нет  | 
| registrations/products | Нет  | 

## <a name="microsoftbatch"></a>Microsoft.Batch
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| batchAccounts | Yes | 

## <a name="microsoftbilling"></a>Microsoft.Billing
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| billingAccounts | Нет  | 
| billingAccounts/billingProfiles | Нет  | 
| billingAccounts/billingProfiles/billingSubscriptions | Нет  | 
| billingAccounts/billingProfiles/invoices | Нет  | 
| billingAccounts/billingProfiles/invoices/pricesheet | Нет  | 
| billingAccounts/billingProfiles/operationStatus | Нет  | 
| billingAccounts/billingProfiles/paymentMethods | Нет  | 
| billingAccounts/billingProfiles/policies | Нет  | 
| billingAccounts/billingProfiles/pricesheet | Нет  | 
| billingAccounts/billingProfiles/products | Нет  | 
| billingAccounts/billingProfiles/transactions | Нет  | 
| billingAccounts/billingSubscriptions | Нет  | 
| billingAccounts/departments | Нет  | 
| billingAccounts/eligibleOffers | Нет  | 
| billingAccounts/enrollmentAccounts | Нет  | 
| billingAccounts/invoices | Нет  | 
| billingAccounts/invoiceSections | Нет  | 
| billingAccounts/invoiceSections/billingSubscriptions | Нет  | 
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Нет  | 
| billingAccounts/invoiceSections/importRequests | Нет  | 
| billingAccounts/invoiceSections/initiateImportRequest | Нет  | 
| billingAccounts/invoiceSections/initiateTransfer | Нет  | 
| billingAccounts/invoiceSections/operationStatus | Нет  | 
| billingAccounts/invoiceSections/products | Нет  | 
| billingAccounts/invoiceSections/transfers | Нет  | 
| billingAccounts/products | Нет  | 
| billingAccounts/projects | Нет  | 
| billingAccounts/projects/billingSubscriptions | Нет  | 
| billingAccounts/projects/importRequests | Нет  | 
| billingAccounts/projects/initiateImportRequest | Нет  | 
| billingAccounts/projects/operationStatus | Нет  | 
| billingAccounts/projects/products | Нет  | 
| billingAccounts/transactions | Нет  | 
| billingPeriods | Нет  | 
| BillingPermissions | Нет  | 
| billingProperty | Нет  | 
| BillingRoleAssignments | Нет  | 
| BillingRoleDefinitions | Нет  | 
| CreateBillingRoleAssignment | Нет  | 
| departments | Нет  | 
| enrollmentAccounts | Нет  | 
| importRequests | Нет  | 
| importRequests/acceptImportRequest | Нет  | 
| importRequests/declineImportRequest | Нет  | 
| invoices | Нет  | 
| transfers | Нет  | 
| transfers/acceptTransfer | Нет  | 
| transfers/declineTransfer | Нет  | 
| transfers/operationStatus | Нет  | 
| usagePlans | Нет  | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| mapApis | Yes | 
| updateCommunicationPreference | Нет  | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| BizTalk | Yes | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| blueprintAssignments | Нет  | 
| blueprintAssignments/assignmentOperations | Нет  | 
| blueprintAssignments/operations | Нет  | 
| blueprints | Нет  | 
| blueprints/artifacts | Нет  | 
| blueprints/versions | Нет  | 
| blueprints/versions/artifacts | Нет  | 

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| botServices | Yes | 
| botServices/channels | Нет  | 
| botServices/connections | Нет  | 

## <a name="microsoftcache"></a>Microsoft.Cache
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| Redis | Yes | 
| RedisConfigDefinition | Нет  | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| appliedReservations | Нет  | 
| calculatePrice | Нет  | 
| catalogs | Нет  | 
| commercialReservationOrders | Нет  | 
| reservationOrders | Нет  | 
| reservationOrders/calculateRefund | Нет  | 
| reservationOrders/merge | Нет  | 
| reservationOrders/reservations | Нет  | 
| reservationOrders/reservations/revisions | Нет  | 
| reservationOrders/return | Нет  | 
| reservationOrders/split | Нет  | 
| reservationOrders/swap | Нет  | 
| reservations | Нет  | 
| ресурсов | Нет  | 
| validateReservationOrder | Нет  | 

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| edgenodes | Нет  | 
| профили | Yes | 
| profiles/endpoints | Yes | 
| profiles/endpoints/customdomains | Нет  | 
| profiles/endpoints/origins | Нет  | 
| validateProbe | Нет  | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| certificateOrders | Yes | 
| certificateOrders/certificates | Нет  | 
| validateCertificateRegistrationInformation | Нет  | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| capabilities | Нет  | 
| domainNames | Нет  | 
| domainNames/capabilities | Нет  | 
| domainNames/internalLoadBalancers | Нет  | 
| domainNames/serviceCertificates | Нет  | 
| domainNames/slots | Нет  | 
| domainNames/slots/roles | Нет  | 
| moveSubscriptionResources | Нет  | 
| operatingSystemFamilies | Нет  | 
| operatingSystems | Нет  | 
| quotas | Нет  | 
| resourceTypes | Нет  | 
| validateSubscriptionMoveAvailability | Нет  | 
| virtualMachines | Нет  | 
| virtualMachines/diagnosticSettings | Нет  | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| classicInfrastructureResources | Нет  | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| capabilities | Нет  | 
| expressRouteCrossConnections | Нет  | 
| expressRouteCrossConnections/peerings | Нет  | 
| gatewaySupportedDevices | Нет  | 
| networkSecurityGroups | Нет  | 
| quotas | Нет  | 
| reservedIps | Нет  | 
| virtualNetworks | Нет  | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Нет  | 
| virtualNetworks/virtualNetworkPeerings | Нет  | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| capabilities | Нет  | 
| disks | Нет  | 
| images | Нет  | 
| osImages | Нет  | 
| osPlatformImages | Нет  | 
| publicImages | Нет  | 
| quotas | Нет  | 
| storageAccounts | Нет  | 
| storageAccounts/services | Нет  | 
| storageAccounts/services/diagnosticSettings | Нет  | 
| storageAccounts/vmImages | Нет  | 
| vmImages | Нет  | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| RateCard | Нет  | 
| UsageAggregates | Нет  | 

## <a name="microsoftcompute"></a>Microsoft.Compute;
| Тип ресурса | Удаление ресурсов в полном режиме |
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
| virtualMachineScaleSets | Yes | 
| virtualMachineScaleSets/extensions | Нет  | 
| virtualMachineScaleSets/networkInterfaces | Нет  | 
| virtualMachineScaleSets/publicIPAddresses | Нет  | 
| virtualMachineScaleSets/virtualMachines | Нет  | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Нет  | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| AggregatedCost | Нет  | 
| сведения о балансе. | Нет  | 
| сведения о бюджете; | Нет  | 
| Расходы | Нет  | 
| CostTags | Нет  | 
| credits | Нет  | 
| events | Нет  | 
| Прогнозы | Нет  | 
| lots | Нет  | 
| Marketplace | Нет  | 
| Pricesheets | Нет  | 
| products | Нет  | 
| ReservationDetails | Нет  | 
| ReservationRecommendations | Нет  | 
| ReservationSummaries | Нет  | 
| ReservationTransactions | Нет  | 
| Теги | Нет  | 
| Термины | Нет  | 
| UsageDetails | Нет  | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| containerGroups | Yes | 
| serviceAssociationLinks | Нет  | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| registries | Yes | 
| registries/builds | Нет  | 
| registries/builds/cancel | Нет  | 
| registries/builds/getLogLink | Нет  | 
| registries/buildTasks | Yes | 
| registries/buildTasks/steps | Нет  | 
| registries/eventGridFilters | Нет  | 
| registries/getBuildSourceUploadUrl | Нет  | 
| registries/GetCredentials | Нет  | 
| registries/importImage | Нет  | 
| registries/queueBuild | Нет  | 
| registries/regenerateCredential | Нет  | 
| registries/regenerateCredentials | Нет  | 
| registries/replications | Yes | 
| registries/runs | Нет  | 
| registries/runs/cancel | Нет  | 
| registries/scheduleRun | Нет  | 
| registries/tasks | Yes | 
| registries/updatePolicies | Нет  | 
| registries/webhooks | Yes | 
| registries/webhooks/getCallbackConfig | Нет  | 
| registries/webhooks/ping | Нет  | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| containerServices | Yes | 
| managedClusters | Yes | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| веб-масштабированием; | Yes | 
| updateCommunicationPreference | Нет  | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| Оповещения | Нет  | 
| BillingAccounts | Нет  | 
| Соединители | Yes | 
| Departments | Нет  | 
| Измерения | Нет  | 
| EnrollmentAccounts | Нет  | 
| Запрос | Нет  | 
| регистрация | Нет  | 
| Reportconfigs | Нет  | 
| Отчеты | Нет  | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| hubs | Yes | 
| hubs/authorizationPolicies | Нет  | 
| hubs/connectors | Нет  | 
| hubs/connectors/mappings | Нет  | 
| hubs/interactions | Нет  | 
| hubs/kpi | Нет  | 
| hubs/links | Нет  | 
| hubs/profiles | Нет  | 
| hubs/roleAssignments | Нет  | 
| hubs/roles | Нет  | 
| hubs/suggestTypeSchema | Нет  | 
| hubs/views | Нет  | 
| hubs/widgetTypes | Нет  | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| jobs | Yes | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| DataBoxEdgeDevices | Yes | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| workspaces | Yes | 
| workspaces/virtualNetworkPeerings | Нет  | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| catalogs | Yes | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| connectionManagers | Yes | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| dataFactories | Yes | 
| dataFactories/diagnosticSettings | Нет  | 
| dataFactorySchema | Нет  | 
| factories | Yes | 
| factories/integrationRuntimes | Нет  | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| accounts | Yes | 
| accounts/dataLakeStoreAccounts | Нет  | 
| accounts/storageAccounts | Нет  | 
| accounts/storageAccounts/containers | Нет  | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| accounts | Yes | 
| accounts/eventGridFilters | Нет  | 
| accounts/firewallRules | Нет  | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| services; | Yes | 
| services/projects | Yes | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| servers | Yes | 
| servers/recoverableServers | Нет  | 
| servers/virtualNetworkRules | Нет  | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| servers | Yes | 
| servers/recoverableServers | Нет  | 
| servers/virtualNetworkRules | Нет  | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| servers | Yes | 
| servers/advisors | Нет  | 
| servers/queryTexts | Нет  | 
| servers/recoverableServers | Нет  | 
| servers/topQueryStatistics | Нет  | 
| servers/virtualNetworkRules | Нет  | 
| servers/waitStatistics | Нет  | 

## <a name="microsoftdevices"></a>Microsoft.Devices
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| IotHubs | Yes | 
| IotHubs/eventGridFilters | Нет  | 
| ProvisioningServices | Yes | 
| usages | Нет  | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| controllers | Yes | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| labs | Yes | 
| labs/serviceRunners | Yes | 
| labs/virtualMachines | Yes | 
| schedules | Yes | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| databaseAccountNames | Нет  | 
| databaseAccounts | Yes | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| domains | Yes | 
| domains/domainOwnershipIdentifiers | Нет  | 
| generateSsoRequest | Нет  | 
| topLevelDomains | Нет  | 
| validateDomainRegistrationInformation | Нет  | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| lcsprojects | Нет  | 
| lcsprojects/clouddeployments | Нет  | 
| lcsprojects/connectors | Нет  | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| domains | Yes | 
| domains/topics | Нет  | 
| eventSubscriptions | Нет  | 
| extensionTopics | Нет  | 
| topics | Yes | 
| topicTypes | Нет  | 

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| clusters | Yes | 
| namespaces | Yes | 
| namespaces/authorizationrules | Нет  | 
| namespaces/disasterrecoveryconfigs | Нет  | 
| namespaces/eventhubs | Нет  | 
| namespaces/eventhubs/authorizationrules | Нет  | 
| namespaces/eventhubs/consumergroups | Нет  | 

## <a name="microsoftfeatures"></a>Microsoft.Features
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| features | Нет  | 
| providers | Нет  | 

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| enroll | Нет  | 
| galleryitems | Нет  | 
| generateartifactaccessuri | Нет  | 
| myareas | Нет  | 
| myareas/areas | Нет  | 
| myareas/areas/areas | Нет  | 
| myareas/areas/areas/galleryitems | Нет  | 
| myareas/areas/galleryitems | Нет  | 
| myareas/galleryitems | Нет  | 
| регистрация | Нет  | 
| ресурсов | Нет  | 
| retrieveresourcesbyid | Нет  | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| guestConfigurationAssignments | Нет  | 
| software | Нет  | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| hanaInstances | Yes | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| clusters | Нет  | 
| clusters/applications | Нет  | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| jobs | Yes | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| labelGroups | Нет  | 
| labelGroups/labels | Нет  | 
| labelGroups/labels/conditions | Нет  | 
| labelGroups/labels/subLabels | Нет  | 
| labelGroups/labels/subLabels/conditions | Нет  | 

## <a name="microsoftinsights"></a>microsoft.insights
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| actiongroups | Yes | 
| activityLogAlerts | Yes | 
| alertrules | Yes | 
| automatedExportSettings | Нет  | 
| autoscalesettings | Yes | 
| baseline | Нет  | 
| calculatebaseline | Нет  | 
| components | Yes | 
| components/events | Нет  | 
| components/pricingPlans | Нет  | 
| components/query | Нет  | 
| diagnosticSettings | Нет  | 
| diagnosticSettingsCategories | Нет  | 
| eventCategories | Нет  | 
| eventtypes | Нет  | 
| extendedDiagnosticSettings | Нет  | 
| logDefinitions | Нет  | 
| logprofiles | Нет  | 
| журналы | Нет  | 
| metricAlerts | Yes |
| migrateToNewPricingModel | Нет  | 
| myWorkbooks | Нет  | 
| Запросы | Нет  | 
| rollbackToLegacyPricingModel | Нет  | 
| scheduledqueryrules | Yes | 
| vmInsightsOnboardingStatuses | Нет  | 
| webtests | Yes | 
| workbooks | Yes | 

## <a name="microsoftintune"></a>Microsoft.Intune
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| diagnosticSettings | Нет  | 
| diagnosticSettingsCategories | Нет  | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| IoTApps | Yes | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| График | Yes | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| deletedVaults | Нет  | 
| vaults | Yes | 
| vaults/accessPolicies | Нет  | 
| vaults/secrets | Нет  | 

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| clusters | Yes | 
| clusters/databases | Нет  | 
| clusters/databases/dataconnections | Нет  | 
| clusters/databases/eventhubconnections | Нет  | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| labaccounts | Yes | 
| users | Нет  | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| журналы | Нет  | 

## <a name="microsoftlogic"></a>Microsoft.Logic
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| integrationAccounts | Yes | 
| workflows | Yes | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| commitmentPlans | Yes | 
| webServices | Yes | 
| Рабочие области | Yes | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| accounts | Yes | 
| accounts/workspaces | Yes | 
| accounts/workspaces/projects | Yes | 
| teamAccounts | Yes | 
| teamAccounts/workspaces | Yes | 
| teamAccounts/workspaces/projects | Yes | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| workspaces | Yes | 
| workspaces/computes | Нет  | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| Identities | Нет  | 
| userAssignedIdentities | Yes | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| getEntities | Нет  | 
| managementGroups | Нет  | 
| ресурсов | Нет  | 
| startTenantBackfill | Нет  | 
| tenantBackfillStatus | Нет  | 

## <a name="microsoftmaps"></a>Microsoft.Maps
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| accounts | Yes | 
| accounts/eventGridFilters | Нет  | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| offers | Нет  | 
| offerTypes | Нет  | 
| offerTypes/publishers | Нет  | 
| offerTypes/publishers/offers | Нет  | 
| offerTypes/publishers/offers/plans | Нет  | 
| offerTypes/publishers/offers/plans/agreements | Нет  | 
| offerTypes/publishers/offers/plans/configs | Нет  | 
| offerTypes/publishers/offers/plans/configs/importImage | Нет  | 
| privategalleryitems | Нет  | 
| products | Нет  | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| classicDevServices | Yes | 
| updateCommunicationPreference | Нет  | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| agreements | Нет  | 
| offertypes | Нет  | 

## <a name="microsoftmedia"></a>Microsoft.Media
| Тип ресурса | Удаление ресурсов в полном режиме |
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
| mediaservices/streamingEndpointOperations | Нет  | 
| mediaservices/streamingEndpoints | Yes | 
| mediaservices/streamingLocators | Нет  | 
| mediaservices/streamingPolicies | Нет  | 
| mediaservices/transforms | Нет  | 
| mediaservices/transforms/jobs | Нет  | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| projects | Yes | 

## <a name="microsoftnetwork"></a>Microsoft.Network.
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| applicationGateways | Yes | 
| applicationSecurityGroups | Yes | 
| azureFirewallFqdnTags | Нет  | 
| azureFirewalls | Yes | 
| bgpServiceCommunities | Нет  | 
| connections | Yes | 
| ddosCustomPolicies | Yes | 
| ddosProtectionPlans | Yes | 
| dnsOperationStatuses | Нет  | 
| dnszones | Yes | 
| dnszones/A | Нет  | 
| dnszones/AAAA | Нет  | 
| dnszones/all | Нет  | 
| dnszones/CAA | Нет  | 
| dnszones/CNAME | Нет  | 
| dnszones/MX | Нет  | 
| dnszones/NS | Нет  | 
| dnszones/PTR | Нет  | 
| dnszones/recordsets | Нет  | 
| dnszones/SOA | Нет  | 
| dnszones/SRV | Нет  | 
| dnszones/TXT | Нет  | 
| expressRouteCircuits | Yes | 
| expressRouteServiceProviders | Нет  | 
| frontdoors | Yes | 
| frontdoorWebApplicationFirewallPolicies | Yes | 
| getDnsResourceReference | Нет  | 
| interfaceEndpoints | Yes | 
| internalNotify | Нет  | 
| loadBalancers | Yes | 
| localNetworkGateways | Yes | 
| natGateways | Yes | 
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
| trafficManagerGeographicHierarchies | Нет  | 
| trafficmanagerprofiles | Yes | 
| trafficmanagerprofiles/heatMaps | Нет  | 
| virtualHubs | Yes | 
| virtualNetworkGateways | Yes | 
| virtualNetworks | Yes | 
| virtualNetworkTaps | Yes | 
| virtualWans | Yes | 
| vpnGateways | Yes | 
| vpnSites | Yes | 
| webApplicationFirewallPolicies | Yes | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| namespaces | Yes | 
| namespaces/notificationHubs | Yes | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| устройства | Нет  | 
| linkTargets | Нет  | 
| storageInsightConfigs | Нет  | 
| workspaces | Yes | 
| workspaces/dataSources | Нет  | 
| workspaces/linkedServices | Нет  | 
| workspaces/query | Нет  | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| managementassociations | Нет  | 
| managementconfigurations | Yes | 
| solutions | Yes | 
| узел "Представления" | Yes | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| policyEvents | Нет  | 
| policyStates | Нет  | 
| policyTrackedResources | Нет  | 
| remediations | Нет  | 

## <a name="microsoftportal"></a>Microsoft.Portal
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| consoles | Нет  | 
| dashboards | Yes | 
| userSettings | Нет  | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| workspaceCollections | Yes | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| capacities | Yes | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| backupProtectedItems | Нет  | 
| vaults | Yes | 

## <a name="microsoftrelay"></a>Microsoft.Relay
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| namespaces | Yes | 
| namespaces/authorizationrules | Нет  | 
| namespaces/hybridconnections | Нет  | 
| namespaces/hybridconnections/authorizationrules | Нет  | 
| namespaces/wcfrelays | Нет  | 
| namespaces/wcfrelays/authorizationrules | Нет  | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| ресурсов | Нет  | 
| subscriptionsStatus | Нет  | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| availabilityStatuses | Нет  | 
| childAvailabilityStatuses | Нет  | 
| childResources | Нет  | 
| events | Нет  | 
| impactedResources | Нет  | 
| Уведомления | Нет  | 

## <a name="microsoftresources"></a>Microsoft.Resources
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| deployments | Нет  | 
| deployments/operations | Нет  | 
| links | Нет  | 
| notifyResourceJobs | Нет  | 
| providers | Нет  | 
| resourceGroups | Нет  | 
| ресурсов | Нет  | 
| subscriptions | Нет  | 
| subscriptions/providers | Нет  | 
| subscriptions/resourceGroups | Нет  | 
| subscriptions/resourcegroups/resources | Нет  | 
| subscriptions/resources | Нет  | 
| subscriptions/tagnames | Нет  | 
| subscriptions/tagNames/tagValues | Нет  | 
| tenants | Нет  | 

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| веб-масштабированием; | Yes | 
| saasresources | Нет  | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| flows | Yes | 
| jobcollections | Yes | 

## <a name="microsoftsearch"></a>Microsoft.Search
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| resourceHealthMetadata | Нет  | 
| searchServices | Yes | 

## <a name="microsoftsecurity"></a>Microsoft.Security
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| advancedThreatProtectionSettings | Нет  | 
| оповещения | Нет  | 
| allowedConnections | Нет  | 
| appliances | Нет  | 
| applicationWhitelistings | Нет  | 
| AutoProvisioningSettings | Нет  | 
| Compliances | Нет  | 
| dataCollectionAgents | Нет  | 
| discoveredSecuritySolutions | Нет  | 
| externalSecuritySolutions | Нет  | 
| InformationProtectionPolicies | Нет  | 
| jitNetworkAccessPolicies | Нет  | 
| monitoring | Нет  | 
| monitoring/antimalware | Нет  | 
| monitoring/baseline | Нет  | 
| monitoring/patch | Нет  | 
| политики | Нет  | 
| pricings | Нет  | 
| securityContacts | Нет  | 
| securitySolutions | Нет  | 
| securitySolutionsReferenceData | Нет  | 
| securityStatus | Нет  | 
| securityStatus/endpoints | Нет  | 
| securityStatus/subnets | Нет  | 
| securityStatus/virtualMachines | Нет  | 
| securityStatuses | Нет  | 
| securityStatusesSummaries | Нет  | 
| Параметры | Нет  | 
| задачи; | Нет  | 
| topologies | Нет  | 
| workspaceSettings | Нет  | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| diagnosticSettings | Нет  | 
| diagnosticSettingsCategories | Нет  | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| namespaces | Yes | 
| namespaces/authorizationrules | Нет  | 
| namespaces/disasterrecoveryconfigs | Нет  | 
| namespaces/eventgridfilters | Нет  | 
| namespaces/queues | Нет  | 
| namespaces/queues/authorizationrules | Нет  | 
| namespaces/topics | Нет  | 
| namespaces/topics/authorizationrules | Нет  | 
| namespaces/topics/subscriptions | Нет  | 
| namespaces/topics/subscriptions/rules | Нет  | 
| premiumMessagingRegions | Нет  | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| clusters | Yes | 
| clusters/applications | Нет  | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| веб-масштабированием; | Yes | 
| gateways | Yes | 
| networks | Yes | 
| секретные коды | Yes | 
| volumes. | Yes | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| SignalR | Yes | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| applianceDefinitions | Yes | 
| appliances | Yes | 
| applicationDefinitions | Yes | 
| веб-масштабированием; | Yes | 
| jitRequests | Yes | 

## <a name="microsoftsql"></a>Microsoft.SQL
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| managedInstances | Yes |
| managedInstances/databases | Да (см. примечание ниже) |
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
| servers/communicationLinks | Нет  | 
| servers/databases | Да (см. примечание ниже) | 
| servers/encryptionProtector | Нет  | 
| servers/firewallRules | Нет  | 
| servers/keys | Нет  | 
| servers/restorableDroppedDatabases | Нет  | 
| servers/serviceobjectives | Нет  | 
| servers/tdeCertificates | Нет  | 

> [!NOTE]
> База данных Master не поддерживает теги, но другие базы данных, в том числе базы данных хранилища данных, поддерживают их.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| SqlVirtualMachineGroups | Yes | 
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Нет  | 
| SqlVirtualMachines | Yes | 

## <a name="microsoftstorage"></a>Microsoft.Storage;
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| storageAccounts | Yes | 
| storageAccounts/blobServices | Нет  | 
| storageAccounts/fileServices | Нет  | 
| storageAccounts/queueServices | Нет  | 
| storageAccounts/services | Нет  | 
| storageAccounts/tableServices | Нет  | 
| usages | Нет  | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| storageSyncServices | Yes | 
| storageSyncServices/registeredServers | Нет  | 
| storageSyncServices/syncGroups | Нет  | 
| storageSyncServices/syncGroups/cloudEndpoints | Нет  | 
| storageSyncServices/syncGroups/serverEndpoints | Нет  | 
| storageSyncServices/workflows | Нет  | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| managers | Yes | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| streamingjobs | Да (см. примечание ниже) | 
| streamingjobs/diagnosticSettings | Нет  | 

> [!NOTE]
> Невозможно добавить тег при выполнении streamingjobs. Остановите ресурс, чтобы добавить тег.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| CreateSubscription | Нет  | 
| SubscriptionDefinitions | Нет  | 
| SubscriptionOperations | Нет  | 

## <a name="microsoftsupport"></a>microsoft.support
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| supporttickets | Нет  | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| providerRegistrations | Yes | 
| ресурсов | Yes | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| environments | Yes | 
| environments/accessPolicies | Нет  | 
| environments/eventsources | Yes | 
| environments/referenceDataSets | Yes | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| учетная запись | Yes | 
| account/extension | Yes | 
| account/project | Yes | 

## <a name="microsoftweb"></a>Microsoft.Web
| Тип ресурса | Удаление ресурсов в полном режиме |
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
| hostingEnvironments/multiRolePools | Нет  | 
| hostingEnvironments/multiRolePools/instances | Нет  | 
| hostingEnvironments/workerPools | Нет  | 
| hostingEnvironments/workerPools/instances | Нет  | 
| publishingUsers | Нет  | 
| к просмотру фильмов | Нет  | 
| resourceHealthMetadata | Нет  | 
| runtimes | Нет  | 
| serverFarms | Yes | 
| serverFarms/workers | Нет  | 
| sites | Yes | 
| sites/domainOwnershipIdentifiers | Нет  | 
| sites/hostNameBindings | Нет  | 
| sites/instances | Нет  | 
| sites/instances/extensions | Нет  | 
| sites/premieraddons | Yes | 
| sites/recommendations | Нет  | 
| sites/resourceHealthMetadata | Нет  | 
| sites/slots | Yes | 
| sites/slots/hostNameBindings | Нет  | 
| sites/slots/instances | Нет  | 
| sites/slots/instances/extensions | Нет  | 
| sourceControls | Нет  | 
| validate | Нет  | 
| verifyHostingEnvironmentVnet | Нет  | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| diagnosticSettings | Нет  | 
| diagnosticSettingsCategories | Нет  | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| DeviceServices | Yes | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Тип ресурса | Удаление ресурсов в полном режиме |
| ------------- | ----------- |
| components | Нет  | 
| componentsSummary | Нет  | 
| monitorInstances | Нет  | 
| monitorInstancesSummary | Нет  | 
| monitors | Нет  | 
| notificationSettings | Нет  | 

## <a name="next-steps"></a>Дальнейшие действия
Сведения о том, как применить теги к ресурсам, см. в статье, посвященной [использованию тегов для организации ресурсов Azure](resource-group-using-tags.md).
