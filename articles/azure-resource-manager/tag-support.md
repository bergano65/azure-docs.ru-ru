---
title: Поддержка тегов Azure Resource Manager для ресурсов
description: В этой статье описываются типы ресурсов Azure, поддерживающие теги, а также приводятся сведения о всех службах Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 05/10/2019
ms.author: tomfitz
ms.openlocfilehash: 7ef37323fb8150e3a6b52800bfafa2585ae328c2
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523221"
---
# <a name="tag-support-for-azure-resources"></a>Поддержка тегов для ресурсов Azure
В этой статье приводятся сведения о поддержке типом ресурса функции [тегов](resource-group-using-tags.md). Столбец с меткой **поддерживает теги** указывает, имеет ли тип ресурса свойство для тега. Столбец с меткой **тег в отчет о стоимости** указывает, передает ли этот тип ресурса тега отчет о стоимости.

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| DomainServices | Да | Да |
| DomainServices/oucontainer | Нет | Нет |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Нет |  Нет |
| diagnosticSettingsCategories | Нет |  Нет |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| supportProviders | Нет |  Нет |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| aadsupportcases | Нет |  Нет |
| addsservices | Нет |  Нет |
| agents | Нет |  Нет |
| anonymousapiusers | Нет |  Нет |
| конфигурация | Нет |  Нет |
| журналы | Нет |  Нет |
| Отчеты | Нет |  Нет |
| services; | Нет |  Нет |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| конфигурации | Нет |  Нет |
| generateRecommendations | Нет |  Нет |
| рекомендации | Нет |  Нет |
| suppressions | Нет |  Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| actionRules | Нет |  Нет |
| оповещений | Нет |  Нет |
| alertsList | Нет |  Нет |
| alertsSummary | Нет |  Нет |
| alertsSummaryList | Нет |  Нет |
| smartDetectorAlertRules | Нет |  Нет |
| smartDetectorRuntimeEnvironments | Нет |  Нет |
| smartGroups | Нет |  Нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| серверы | Да | Да |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| reportFeedback | Нет |  Нет |
| служба | Да | Да |
| validateServiceName | Нет |  Нет |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| attestationProviders | Нет |  Нет |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| classicAdministrators | Нет |  Нет |
| denyAssignments | Нет |  Нет |
| elevateAccess | Нет |  Нет |
| locks | Нет |  Нет |
| разрешения | Нет |  Нет |
| policyAssignments | Нет |  Нет |
| policyDefinitions | Нет |  Нет |
| policySetDefinitions | Нет |  Нет |
| providerOperations | Нет |  Нет |
| roleAssignments | Нет |  Нет |
| roleDefinitions | Нет |  Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| automationAccounts | Да | Да |
| automationAccounts/configurations | Да | Да |
| automationAccounts/jobs | Нет |  Нет |
| automationAccounts/runbooks | Да | Да |
| automationAccounts/softwareUpdateConfigurations | Нет | Нет |
| automationAccounts/webhooks | Нет |  Нет |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| environments | Нет |  Нет |
| environments/accounts | Нет |  Нет |
| environments/accounts/namespaces | Нет |  Нет |
| environments/accounts/namespaces/configurations | Нет |  Нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| b2cDirectories | Да | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| registrations | Да | Да |
| registrations/customerSubscriptions | Нет |  Нет |
| registrations/products | Нет |  Нет |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| batchAccounts | Да | Да |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| billingAccounts | Нет |  Нет |
| billingAccounts/billingProfiles | Нет |  Нет |
| billingAccounts/billingProfiles/billingSubscriptions | Нет |  Нет |
| billingAccounts/billingProfiles/invoices | Нет |  Нет |
| billingAccounts/billingProfiles/invoices/pricesheet | Нет |  Нет |
| billingAccounts/billingProfiles/operationStatus | Нет |  Нет |
| billingAccounts/billingProfiles/paymentMethods | Нет |  Нет |
| billingAccounts/billingProfiles/policies | Нет |  Нет |
| billingAccounts/billingProfiles/pricesheet | Нет |  Нет |
| billingAccounts/billingProfiles/products | Нет |  Нет |
| billingAccounts/billingProfiles/transactions | Нет |  Нет |
| billingAccounts/billingSubscriptions | Нет |  Нет |
| billingAccounts/departments | Нет |  Нет |
| billingAccounts/eligibleOffers | Нет |  Нет |
| billingAccounts/enrollmentAccounts | Нет |  Нет |
| billingAccounts/invoices | Нет |  Нет |
| billingAccounts/invoiceSections | Нет |  Нет |
| billingAccounts/invoiceSections/billingSubscriptions | Нет |  Нет |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Нет |  Нет |
| billingAccounts/invoiceSections/importRequests | Нет |  Нет |
| billingAccounts/invoiceSections/initiateImportRequest | Нет |  Нет |
| billingAccounts/invoiceSections/initiateTransfer | Нет |  Нет |
| billingAccounts/invoiceSections/operationStatus | Нет |  Нет |
| billingAccounts/invoiceSections/products | Нет |  Нет |
| billingAccounts/invoiceSections/transfers | Нет |  Нет |
| billingAccounts/products | Нет |  Нет |
| billingAccounts/projects | Нет |  Нет |
| billingAccounts/projects/billingSubscriptions | Нет |  Нет |
| billingAccounts/projects/importRequests | Нет |  Нет |
| billingAccounts/projects/initiateImportRequest | Нет |  Нет |
| billingAccounts/projects/operationStatus | Нет |  Нет |
| billingAccounts/projects/products | Нет |  Нет |
| billingAccounts/transactions | Нет |  Нет |
| billingPeriods | Нет |  Нет |
| BillingPermissions | Нет |  Нет |
| billingProperty | Нет |  Нет |
| BillingRoleAssignments | Нет |  Нет |
| BillingRoleDefinitions | Нет |  Нет |
| CreateBillingRoleAssignment | Нет |  Нет |
| departments | Нет |  Нет |
| enrollmentAccounts | Нет |  Нет |
| importRequests | Нет |  Нет |
| importRequests/acceptImportRequest | Нет |  Нет |
| importRequests/declineImportRequest | Нет |  Нет |
| invoices | Нет |  Нет |
| transfers | Нет |  Нет |
| transfers/acceptTransfer | Нет |  Нет |
| transfers/declineTransfer | Нет |  Нет |
| transfers/operationStatus | Нет |  Нет |
| usagePlans | Нет |  Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| mapApis | Да | Да |
| updateCommunicationPreference | Нет |  Нет |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| BizTalk | Да | Да |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| blueprintAssignments | Нет |  Нет |
| blueprintAssignments/assignmentOperations | Нет |  Нет |
| blueprintAssignments/operations | Нет |  Нет |
| blueprints | Нет |  Нет |
| blueprints/artifacts | Нет |  Нет |
| blueprints/versions | Нет |  Нет |
| blueprints/versions/artifacts | Нет |  Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| botServices | Да | Да |
| botServices/channels | Нет |  Нет |
| botServices/connections | Нет |  Нет |

## <a name="microsoftcache"></a>Microsoft.Cache
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| Redis | Да | Да |
| RedisConfigDefinition | Нет |  Нет |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| appliedReservations | Нет |  Нет |
| calculatePrice | Нет |  Нет |
| catalogs | Нет |  Нет |
| commercialReservationOrders | Нет |  Нет |
| reservationOrders | Нет |  Нет |
| reservationOrders/calculateRefund | Нет |  Нет |
| reservationOrders/merge | Нет |  Нет |
| reservationOrders/reservations | Нет |  Нет |
| reservationOrders/reservations/revisions | Нет |  Нет |
| reservationOrders/return | Нет |  Нет |
| reservationOrders/split | Нет |  Нет |
| reservationOrders/swap | Нет |  Нет |
| reservations | Нет |  Нет |
| ресурсы | Нет |  Нет |
| validateReservationOrder | Нет |  Нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| edgenodes | Нет |  Нет |
| профили | Да | Да |
| profiles/endpoints | Да | Да |
| profiles/endpoints/customdomains | Нет |  Нет |
| profiles/endpoints/origins | Нет |  Нет |
| validateProbe | Нет |  Нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| certificateOrders | Да | Да |
| certificateOrders/certificates | Нет |  Нет |
| validateCertificateRegistrationInformation | Нет |  Нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| capabilities | Нет |  Нет |
| domainNames | Нет |  Нет |
| domainNames/capabilities | Нет |  Нет |
| domainNames/internalLoadBalancers | Нет |  Нет |
| domainNames/serviceCertificates | Нет |  Нет |
| domainNames/slots | Нет |  Нет |
| domainNames/slots/roles | Нет |  Нет |
| moveSubscriptionResources | Нет |  Нет |
| operatingSystemFamilies | Нет |  Нет |
| operatingSystems | Нет |  Нет |
| quotas | Нет |  Нет |
| resourceTypes | Нет |  Нет |
| validateSubscriptionMoveAvailability | Нет |  Нет |
| virtualMachines | Нет |  Нет |
| virtualMachines/diagnosticSettings | Нет |  Нет |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | Нет |  Нет |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| capabilities | Нет |  Нет |
| expressRouteCrossConnections | Нет |  Нет |
| expressRouteCrossConnections/peerings | Нет |  Нет |
| gatewaySupportedDevices | Нет |  Нет |
| networkSecurityGroups | Нет |  Нет |
| quotas | Нет |  Нет |
| reservedIps | Нет |  Нет |
| virtualNetworks | Нет |  Нет |
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Нет |  Нет |
| virtualNetworks/virtualNetworkPeerings | Нет |  Нет |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| capabilities | Нет |  Нет |
| диски | Нет |  Нет |
| образы | Нет |  Нет |
| osImages | Нет |  Нет |
| osPlatformImages | Нет |  Нет |
| publicImages | Нет |  Нет |
| quotas | Нет |  Нет |
| storageAccounts | Нет |  Нет |
| storageAccounts/services | Нет |  Нет |
| storageAccounts/services/diagnosticSettings | Нет |  Нет |
| storageAccounts/vmImages | Нет |  Нет |
| vmImages | Нет |  Нет |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| учетные записи | Да | Да |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| RateCard | Нет |  Нет |
| UsageAggregates | Нет |  Нет |

## <a name="microsoftcompute"></a>Microsoft.Compute;
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| availabilitySets | Да | Да |
| диски | Да | Да |
| образы | Да | Да |
| restorePointCollections | Да | Да |
| restorePointCollections/restorePoints | Нет |  Нет |
| sharedVMImages | Да | Да |
| sharedVMImages/versions | Да | Да |
| моментальные снимки | Да | Да |
| virtualMachines | Да | Да |
| virtualMachines/diagnosticSettings | Нет |  Нет |
| virtualMachines/extensions | Да | Да |
| virtualMachineScaleSets | Да | Да |
| virtualMachineScaleSets/extensions | Нет |  Нет |
| virtualMachineScaleSets/networkInterfaces | Нет |  Нет |
| virtualMachineScaleSets/publicIPAddresses | Нет |  Нет |
| virtualMachineScaleSets/virtualMachines | Нет |  Нет |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Нет |  Нет |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| AggregatedCost | Нет |  Нет |
| сведения о балансе. | Нет |  Нет |
| Бюджеты | Нет |  Нет |
| Расходы | Нет |  Нет |
| CostTags | Нет |  Нет |
| credits | Нет |  Нет |
| события | Нет |  Нет |
| Прогнозы | Нет |  Нет |
| lots | Нет |  Нет |
| Marketplace | Нет |  Нет |
| Pricesheets | Нет |  Нет |
| products | Нет |  Нет |
| ReservationDetails | Нет |  Нет |
| ReservationRecommendations | Нет |  Нет |
| ReservationSummaries | Нет |  Нет |
| ReservationTransactions | Нет |  Нет |
| Tags | Нет |  Нет |
| Условия | Нет |  Нет |
| UsageDetails | Нет |  Нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| containerGroups | Да | Да |
| serviceAssociationLinks | Нет |  Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| registries | Да | Да |
| registries/builds | Нет |  Нет |
| registries/builds/cancel | Нет |  Нет |
| registries/builds/getLogLink | Нет |  Нет |
| registries/buildTasks | Да | Да |
| registries/buildTasks/steps | Нет |  Нет |
| registries/eventGridFilters | Нет |  Нет |
| registries/getBuildSourceUploadUrl | Нет |  Нет |
| registries/GetCredentials | Нет |  Нет |
| registries/importImage | Нет |  Нет |
| registries/queueBuild | Нет |  Нет |
| registries/regenerateCredential | Нет |  Нет |
| registries/regenerateCredentials | Нет |  Нет |
| registries/replications | Да | Да |
| registries/runs | Нет |  Нет |
| registries/runs/cancel | Нет |  Нет |
| registries/scheduleRun | Нет |  Нет |
| registries/tasks | Да | Да |
| registries/updatePolicies | Нет |  Нет |
| registries/webhooks | Да | Да |
| registries/webhooks/getCallbackConfig | Нет |  Нет |
| registries/webhooks/ping | Нет |  Нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| containerServices | Да | Да |
| managedClusters | Да | Да |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| приложения | Да | Да |
| updateCommunicationPreference | Нет |  Нет |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| учетные записи | Да | Да |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| Оповещения | Нет |  Нет |
| BillingAccounts | Нет |  Нет |
| Соединители | Да | Да |
| Departments | Нет |  Нет |
| Размеры | Нет |  Нет |
| EnrollmentAccounts | Нет |  Нет |
| Запрос | Нет |  Нет |
| регистрация | Нет |  Нет |
| Reportconfigs | Нет |  Нет |
| Отчеты | Нет |  Нет |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| hubs | Да | Да |
| hubs/authorizationPolicies | Нет |  Нет |
| hubs/connectors | Нет |  Нет |
| hubs/connectors/mappings | Нет |  Нет |
| hubs/interactions | Нет |  Нет |
| hubs/kpi | Нет |  Нет |
| hubs/links | Нет |  Нет |
| hubs/profiles | Нет |  Нет |
| hubs/roleAssignments | Нет |  Нет |
| hubs/roles | Нет |  Нет |
| hubs/suggestTypeSchema | Нет |  Нет |
| hubs/views | Нет |  Нет |
| hubs/widgetTypes | Нет |  Нет |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| задания | Да | Да |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Да | Да |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| workspaces | Да | Нет |
| workspaces/virtualNetworkPeerings | Нет |  Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| catalogs | Да | Да |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| connectionManagers | Да | Да |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| dataFactories | Да | Нет |
| dataFactories/diagnosticSettings | Нет |  Нет |
| dataFactorySchema | Нет |  Нет |
| factories | Да | Нет |
| factories/integrationRuntimes | Нет |  Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| учетные записи | Да | Да |
| accounts/dataLakeStoreAccounts | Нет |  Нет |
| accounts/storageAccounts | Нет |  Нет |
| accounts/storageAccounts/containers | Нет |  Нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| учетные записи | Да | Да |
| accounts/eventGridFilters | Нет |  Нет |
| accounts/firewallRules | Нет |  Нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| services; | Да | Да |
| services/projects | Да | Да |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| серверы | Да | Да |
| servers/recoverableServers | Нет |  Нет |
| servers/virtualNetworkRules | Нет |  Нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| серверы | Да | Да |
| servers/recoverableServers | Нет |  Нет |
| servers/virtualNetworkRules | Нет |  Нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| серверы | Да | Да |
| servers/advisors | Нет |  Нет |
| servers/queryTexts | Нет |  Нет |
| servers/recoverableServers | Нет |  Нет |
| servers/topQueryStatistics | Нет |  Нет |
| servers/virtualNetworkRules | Нет |  Нет |
| servers/waitStatistics | Нет |  Нет |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| IotHubs | Да | Да |
| IotHubs/eventGridFilters | Нет |  Нет |
| ProvisioningServices | Да | Да |
| usages | Нет |  Нет |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| controllers | Да | Да |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| labs | Да | Да |
| labs/serviceRunners | Да | Да |
| labs/virtualMachines | Да | Да |
| schedules | Да | Да |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| databaseAccountNames | Нет |  Нет |
| databaseAccounts | Да | Да |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| domains | Да | Да |
| domains/domainOwnershipIdentifiers | Нет |  Нет |
| generateSsoRequest | Нет |  Нет |
| topLevelDomains | Нет |  Нет |
| validateDomainRegistrationInformation | Нет |  Нет |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| lcsprojects | Нет |  Нет |
| lcsprojects/clouddeployments | Нет |  Нет |
| lcsprojects/connectors | Нет |  Нет |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| domains | Да | Нет |
| domains/topics | Нет |  Нет |
| eventSubscriptions | Нет |  Нет |
| extensionTopics | Нет |  Нет |
| topics | Да | Нет |
| topicTypes | Нет |  Нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| clusters | Да | Нет |
| пространства имен | Да | Нет |
| namespaces/authorizationrules | Нет |  Нет |
| namespaces/disasterrecoveryconfigs | Нет |  Нет |
| namespaces/eventhubs | Нет |  Нет |
| namespaces/eventhubs/authorizationrules | Нет |  Нет |
| namespaces/eventhubs/consumergroups | Нет |  Нет |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| features | Нет |  Нет |
| providers | Нет |  Нет |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| зарегистрировать | Нет |  Нет |
| galleryitems | Нет |  Нет |
| generateartifactaccessuri | Нет |  Нет |
| myareas | Нет |  Нет |
| myareas/areas | Нет |  Нет |
| myareas/areas/areas | Нет |  Нет |
| myareas/areas/areas/galleryitems | Нет |  Нет |
| myareas/areas/galleryitems | Нет |  Нет |
| myareas/galleryitems | Нет |  Нет |
| регистрация | Нет |  Нет |
| ресурсы | Нет |  Нет |
| retrieveresourcesbyid | Нет |  Нет |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | Нет |  Нет |
| software | Нет |  Нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| hanaInstances | Да |  Да |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| clusters | Да | Да |
| clusters/applications | Нет |  Нет |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| задания | Да | Да |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| labelGroups | Нет |  Нет |
| labelGroups/labels | Нет |  Нет |
| labelGroups/labels/conditions | Нет |  Нет |
| labelGroups/labels/subLabels | Нет |  Нет |
| labelGroups/labels/subLabels/conditions | Нет |  Нет |

## <a name="microsoftinsights"></a>microsoft.insights
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| actiongroups | Да | Да |
| activityLogAlerts | Да | Да |
| alertrules | Да | Да |
| automatedExportSettings | Нет |  Нет |
| autoscalesettings | Да | Да |
| baseline | Нет |  Нет |
| calculatebaseline | Нет |  Нет |
| components | Да | Да |
| components/events | Нет |  Нет |
| components/pricingPlans | Нет |  Нет |
| components/query | Нет |  Нет |
| diagnosticSettings | Нет |  Нет |
| diagnosticSettingsCategories | Нет |  Нет |
| eventCategories | Нет |  Нет |
| eventtypes | Нет |  Нет |
| extendedDiagnosticSettings | Нет |  Нет |
| logDefinitions | Нет |  Нет |
| logprofiles | Нет |  Нет |
| журналы | Нет |  Нет |
| metricAlerts | Да | Да |
| migrateToNewPricingModel | Нет |  Нет |
| myWorkbooks | Нет |  Нет |
| Запросы | Нет |  Нет |
| rollbackToLegacyPricingModel | Нет |  Нет |
| scheduledqueryrules | Да | Да |
| vmInsightsOnboardingStatuses | Нет |  Нет |
| webtests | Да | Да |
| книги | Да | Да |

## <a name="microsoftintune"></a>Microsoft.Intune
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Нет |  Нет |
| diagnosticSettingsCategories | Нет |  Нет |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| IoTApps | Да | Да |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| Граф | Да | Да |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| deletedVaults | Нет |  Нет |
| vaults | Да | Да |
| vaults/accessPolicies | Нет |  Нет |
| vaults/secrets | Нет |  Нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| clusters | Да | Да |
| clusters/databases | Нет |  Нет |
| clusters/databases/dataconnections | Нет |  Нет |
| clusters/databases/eventhubconnections | Нет |  Нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| labaccounts | Да | Да |
| пользователей | Нет |  Нет |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| учетные записи | Да | Да |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| учетные записи | Да | Да |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| журналы | Нет |  Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| integrationAccounts | Да | Да |
| workflows | Да | Да |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| commitmentPlans | Да | Да |
| webServices | Да | Да |
| Рабочие области | Да | Да |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| учетные записи | Да | Да |
| accounts/workspaces | Да | Да |
| accounts/workspaces/projects | Да | Да |
| teamAccounts | Да | Да |
| teamAccounts/workspaces | Да | Да |
| teamAccounts/workspaces/projects | Да | Да |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| учетные записи | Да | Да |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| workspaces | Да | Да |
| workspaces/computes | Нет |  Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| Удостоверения | Нет |  Нет |
| userAssignedIdentities | Да | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| getEntities | Нет |  Нет |
| managementGroups | Нет |  Нет |
| ресурсы | Нет |  Нет |
| startTenantBackfill | Нет |  Нет |
| tenantBackfillStatus | Нет |  Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| учетные записи | Да | Да |
| accounts/eventGridFilters | Нет |  Нет |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| offers | Нет |  Нет |
| offerTypes | Нет |  Нет |
| offerTypes/publishers | Нет |  Нет |
| offerTypes/publishers/offers | Нет |  Нет |
| offerTypes/publishers/offers/plans | Нет |  Нет |
| offerTypes/publishers/offers/plans/agreements | Нет |  Нет |
| offerTypes/publishers/offers/plans/configs | Нет |  Нет |
| offerTypes/publishers/offers/plans/configs/importImage | Нет |  Нет |
| privategalleryitems | Нет |  Нет |
| products | Нет |  Нет |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| classicDevServices | Да | Да |
| updateCommunicationPreference | Нет |  Нет |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| agreements | Нет |  Нет |
| offertypes | Нет |  Нет |

## <a name="microsoftmedia"></a>Microsoft.Media
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| mediaservices | Да | Да |
| mediaservices/accountFilters | Нет |  Нет |
| mediaservices/assets | Нет |  Нет |
| mediaservices/assets/assetFilters | Нет |  Нет |
| mediaservices/contentKeyPolicies | Нет |  Нет |
| mediaservices/eventGridFilters | Нет |  Нет |
| mediaservices/liveEventOperations | Нет |  Нет |
| mediaservices/liveEvents | Да | Да |
| mediaservices/liveEvents/liveOutputs | Нет |  Нет |
| mediaservices/liveOutputOperations | Нет |  Нет |
| mediaservices/streamingEndpointOperations | Нет |  Нет |
| mediaservices/streamingEndpoints | Да | Да |
| mediaservices/streamingLocators | Нет |  Нет |
| mediaservices/streamingPolicies | Нет |  Нет |
| mediaservices/transforms | Нет |  Нет |
| mediaservices/transforms/jobs | Нет |  Нет |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| projects | Да | Да |

## <a name="microsoftnetwork"></a>Microsoft.Network.
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| applicationGateways | Да | Нет |
| applicationSecurityGroups | Да | Да |
| azureFirewallFqdnTags | Нет |  Нет |
| azureFirewalls | Да | Нет |
| bgpServiceCommunities | Нет |  Нет |
| подключения | Да | Да |
| ddosCustomPolicies | Да | Да |
| ddosProtectionPlans | Да | Да |
| dnsOperationStatuses | Нет |  Нет |
| dnszones | Да | Да |
| dnszones/A | Нет |  Нет |
| dnszones/AAAA | Нет |  Нет |
| dnszones/all | Нет |  Нет |
| dnszones/CAA | Нет |  Нет |
| dnszones/CNAME | Нет |  Нет |
| dnszones/MX | Нет |  Нет |
| dnszones/NS | Нет |  Нет |
| dnszones/PTR | Нет |  Нет |
| dnszones/recordsets | Нет |  Нет |
| dnszones/SOA | Нет |  Нет |
| dnszones/SRV | Нет |  Нет |
| dnszones/TXT | Нет |  Нет |
| expressRouteCircuits | Да  | Нет |
| expressRouteServiceProviders | Нет |  Нет |
| frontdoor | Да | Да |
| frontdoorWebApplicationFirewallPolicies | Да | Да |
| getDnsResourceReference | Нет |  Нет |
| interfaceEndpoints | Да | Да |
| internalNotify | Нет |  Нет |
| loadBalancers | Да | Нет |
| localNetworkGateways | Да | Да |
| natGateways | Да | Да |
| networkIntentPolicies | Да | Да |
| networkInterfaces | Да | Да |
| networkProfiles | Да | Да |
| networkSecurityGroups | Да | Да |
| networkWatchers | Да | Нет |
| networkWatchers/connectionMonitors | Да | Нет |
| networkWatchers/lenses | Да | Нет |
| networkWatchers/pingMeshes | Да | Нет |
| privateLinkServices | Да | Да |
| publicIPAddresses | Да | Да |
| publicIPPrefixes | Да | Да |
| routeFilters | Да | Да |
| routeTables | Да | Да |
| serviceEndpointPolicies | Да | Да |
| trafficManagerGeographicHierarchies | Нет |  Нет |
| trafficmanagerprofiles | Да | Да |
| trafficmanagerprofiles/heatMaps | Нет |  Нет |
| virtualHubs | Да | Да |
| virtualNetworkGateways | Да | Нет |
| virtualNetworks | Да | Да |
| рамках/подсетей | Нет |  Нет |
| virtualNetworkTaps | Да | Да |
| virtualWans | Да | Да |
| vpnGateways | Да | Нет |
| vpnSites | Да | Да |
| webApplicationFirewallPolicies | Да | Да |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| пространства имен | Да | Нет |
| namespaces/notificationHubs | Да | Нет |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| устройства | Нет |  Нет |
| linkTargets | Нет |  Нет |
| storageInsightConfigs | Нет |  Нет |
| workspaces | Да | Да |
| workspaces/dataSources | Нет |  Нет |
| workspaces/linkedServices | Нет |  Нет |
| workspaces/query | Нет |  Нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| managementassociations | Нет |  Нет |
| managementconfigurations | Да | Да |
| solutions | Да | Да |
| просмотры | Да | Да |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| policyEvents | Нет |  Нет |
| policyStates | Нет |  Нет |
| policyTrackedResources | Нет |  Нет |
| remediations | Нет |  Нет |

## <a name="microsoftportal"></a>Microsoft.Portal
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| consoles | Нет |  Нет |
| dashboards | Да | Да |
| userSettings | Нет |  Нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| workspaceCollections | Да | Да |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| capacities | Да | Да |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| учетные записи | Да | Да |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| backupProtectedItems | Нет |  Нет |
| vaults | Да | Да |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| пространства имен | Да | Да |
| namespaces/authorizationrules | Нет |  Нет |
| namespaces/hybridconnections | Нет |  Нет |
| namespaces/hybridconnections/authorizationrules | Нет |  Нет |
| namespaces/wcfrelays | Нет |  Нет |
| namespaces/wcfrelays/authorizationrules | Нет |  Нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| ресурсы | Нет |  Нет |
| subscriptionsStatus | Нет |  Нет |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| availabilityStatuses | Нет |  Нет |
| childAvailabilityStatuses | Нет |  Нет |
| childResources | Нет |  Нет |
| события | Нет |  Нет |
| impactedResources | Нет |  Нет |
| уведомления | Нет |  Нет |

## <a name="microsoftresources"></a>Microsoft.Resources
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| развертывания | Нет |  Нет |
| deployments/operations | Нет |  Нет |
| links | Нет |  Нет |
| notifyResourceJobs | Нет |  Нет |
| providers | Нет |  Нет |
| resourceGroups | Нет |  Нет |
| ресурсы | Нет |  Нет |
| — подписки | Нет |  Нет |
| subscriptions/providers | Нет |  Нет |
| subscriptions/resourceGroups | Нет |  Нет |
| subscriptions/resourcegroups/resources | Нет |  Нет |
| subscriptions/resources | Нет |  Нет |
| subscriptions/tagnames | Нет |  Нет |
| subscriptions/tagNames/tagValues | Нет |  Нет |
| tenants | Нет |  Нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| приложения | Да | Да |
| saasresources | Нет |  Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| Потоки | Да | Да |
| jobcollections | Да | Да |

## <a name="microsoftsearch"></a>Microsoft.Search
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | Нет |  Нет |
| searchServices | Да | Да |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | Нет |  Нет |
| оповещений | Нет |  Нет |
| allowedConnections | Нет |  Нет |
| appliances | Нет |  Нет |
| applicationWhitelistings | Нет |  Нет |
| AutoProvisioningSettings | Нет |  Нет |
| Compliances | Нет |  Нет |
| dataCollectionAgents | Нет |  Нет |
| discoveredSecuritySolutions | Нет |  Нет |
| externalSecuritySolutions | Нет |  Нет |
| InformationProtectionPolicies | Нет |  Нет |
| jitNetworkAccessPolicies | Нет |  Нет |
| monitoring | Нет |  Нет |
| monitoring/antimalware | Нет |  Нет |
| monitoring/baseline | Нет |  Нет |
| monitoring/patch | Нет |  Нет |
| политики | Нет |  Нет |
| pricings | Нет |  Нет |
| securityContacts | Нет |  Нет |
| securitySolutions | Нет |  Нет |
| securitySolutionsReferenceData | Нет |  Нет |
| securityStatus | Нет |  Нет |
| securityStatus/endpoints | Нет |  Нет |
| securityStatus/subnets | Нет |  Нет |
| securityStatus/virtualMachines | Нет |  Нет |
| securityStatuses | Нет |  Нет |
| securityStatusesSummaries | Нет |  Нет |
| параметры | Нет |  Нет |
| задачи; | Нет |  Нет |
| topologies | Нет |  Нет |
| workspaceSettings | Нет |  Нет |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Нет |  Нет |
| diagnosticSettingsCategories | Нет |  Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| пространства имен | Да | Нет |
| namespaces/authorizationrules | Нет |  Нет |
| namespaces/disasterrecoveryconfigs | Нет |  Нет |
| namespaces/eventgridfilters | Нет |  Нет |
| namespaces/queues | Нет |  Нет |
| namespaces/queues/authorizationrules | Нет |  Нет |
| namespaces/topics | Нет |  Нет |
| namespaces/topics/authorizationrules | Нет |  Нет |
| namespaces/topics/subscriptions | Нет |  Нет |
| namespaces/topics/subscriptions/rules | Нет |  Нет |
| premiumMessagingRegions | Нет |  Нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| clusters | Да | Да |
| clusters/applications | Нет |  Нет |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| приложения | Да | Да |
| gateways | Да | Да |
| Сети | Да | Да |
| секреты | Да | Да |
| тома | Да | Да |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| SignalR | Да | Да |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Да | Да |
| appliances | Да | Да |
| applicationDefinitions | Да | Да |
| приложения | Да | Да |
| jitRequests | Да | Да |

## <a name="microsoftsql"></a>Microsoft.SQL
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| managedInstances | Да | Да |
| managedInstances/databases | Да (см. примечание ниже) | Да |
| managedInstances/databases/backupShortTermRetentionPolicies | Нет | Нет |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Нет | Нет |
| managedInstances/databases/vulnerabilityAssessments | Нет | Нет |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Нет | Нет |
| managedInstances/encryptionProtector | Нет | Нет |
| managedInstances/keys | Нет | Нет |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Нет | Нет |
| managedInstances/vulnerabilityAssessments | Нет | Нет |
| серверы | Да | Да |
| servers/administrators | Нет |  Нет |
| servers/communicationLinks | Нет |  Нет |
| servers/databases | Да (см. примечание ниже) | Да |
| servers/encryptionProtector | Нет |  Нет |
| servers/firewallRules | Нет |  Нет |
| servers/keys | Нет |  Нет |
| servers/restorableDroppedDatabases | Нет |  Нет |
| servers/serviceobjectives | Нет |  Нет |
| servers/tdeCertificates | Нет |  Нет |

> [!NOTE]
> База данных Master не поддерживает теги, но другие базы данных, в том числе базы данных хранилища данных Azure SQL, поддерживают их. Базы данных Хранилища данных SQL Azure должны быть в активном состоянии (работа не приостановлена).


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Да | Да |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Нет |  Нет |
| SqlVirtualMachines | Да | Да |

## <a name="microsoftstorage"></a>Microsoft.Storage;
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| storageAccounts | Да | Да |
| storageAccounts/blobServices | Нет |  Нет |
| storageAccounts/fileServices | Нет |  Нет |
| storageAccounts/queueServices | Нет |  Нет |
| storageAccounts/services | Нет |  Нет |
| storageAccounts/tableServices | Нет |  Нет |
| usages | Нет |  Нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| storageSyncServices | Да | Да |
| storageSyncServices/registeredServers | Нет |  Нет |
| storageSyncServices/syncGroups | Нет |  Нет |
| storageSyncServices/syncGroups/cloudEndpoints | Нет |  Нет |
| storageSyncServices/syncGroups/serverEndpoints | Нет |  Нет |
| storageSyncServices/workflows | Нет |  Нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| managers | Да | Да |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| streamingjobs | Да (см. примечание ниже) | Да |
| streamingjobs/diagnosticSettings | Нет |  Нет |

> [!NOTE]
> Невозможно добавить тег при выполнении streamingjobs. Остановите ресурс, чтобы добавить тег.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| CreateSubscription | Нет |  Нет |
| SubscriptionDefinitions | Нет |  Нет |
| SubscriptionOperations | Нет |  Нет |

## <a name="microsoftsupport"></a>microsoft.support
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| supporttickets | Нет |  Нет |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| providerRegistrations | Да | Да |
| ресурсы | Да | Да |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| environments | Да | Нет |
| environments/accessPolicies | Нет |  Нет |
| environments/eventsources | Да | Нет |
| environments/referenceDataSets | Да | Нет |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| учетная запись | Да | Да |
| account/extension | Да | Да |
| account/project | Да | Да |

## <a name="microsoftweb"></a>Microsoft.Web
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | Нет |  Нет |
| apiManagementAccounts/apiAcls | Нет |  Нет |
| apiManagementAccounts/apis | Нет |  Нет |
| apiManagementAccounts/apis/apiAcls | Нет |  Нет |
| apiManagementAccounts/apis/connectionAcls | Нет |  Нет |
| apiManagementAccounts/apis/connections | Нет |  Нет |
| apiManagementAccounts/apis/connections/connectionAcls | Нет |  Нет |
| apiManagementAccounts/apis/localizedDefinitions | Нет |  Нет |
| apiManagementAccounts/connectionAcls | Нет |  Нет |
| apiManagementAccounts/connections | Нет |  Нет |
| billingMeters | Нет |  Нет |
| сертификаты | Да | Да |
| connectionGateways | Да | Да |
| подключения | Да | Да |
| customApis | Да | Да |
| deletedSites | Нет |  Нет |
| функции | Нет |  Нет |
| hostingEnvironments | Да | Нет |
| hostingEnvironments/multiRolePools | Нет |  Нет |
| hostingEnvironments/multiRolePools/instances | Нет |  Нет |
| hostingEnvironments/workerPools | Нет |  Нет |
| hostingEnvironments/workerPools/instances | Нет |  Нет |
| publishingUsers | Нет |  Нет |
| рекомендации | Нет |  Нет |
| resourceHealthMetadata | Нет |  Нет |
| runtimes | Нет |  Нет |
| serverFarms | Да | Да |
| serverFarms/workers | Нет |  Нет |
| сайты | Да | Да |
| sites/domainOwnershipIdentifiers | Нет |  Нет |
| sites/hostNameBindings | Нет |  Нет |
| sites/instances | Нет |  Нет |
| sites/instances/extensions | Нет |  Нет |
| sites/premieraddons | Да | Да |
| sites/recommendations | Нет |  Нет |
| sites/resourceHealthMetadata | Нет |  Нет |
| sites/slots | Да | Да |
| sites/slots/hostNameBindings | Нет |  Нет |
| sites/slots/instances | Нет |  Нет |
| sites/slots/instances/extensions | Нет |  Нет |
| sourceControls | Нет |  Нет |
| validate | Нет |  Нет |
| verifyHostingEnvironmentVnet | Нет |  Нет |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Нет |  Нет |
| diagnosticSettingsCategories | Нет |  Нет |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| DeviceServices | Да | Да |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Тип ресурса | Поддержка тегов | Пометка в отчет о стоимости |
| ------------- | ----------- | ----------- |
| components | Нет |  Нет |
| componentsSummary | Нет |  Нет |
| monitorInstances | Нет |  Нет |
| monitorInstancesSummary | Нет |  Нет |
| monitors | Нет |  Нет |
| notificationSettings | Нет |  Нет  |

## <a name="next-steps"></a>Дальнейшие действия
Сведения о том, как применить теги к ресурсам, см. в статье, посвященной [использованию тегов для организации ресурсов Azure](resource-group-using-tags.md).
