---
title: Tag support for resources
description: В этой статье описываются типы ресурсов Azure, поддерживающие теги, а также приводятся сведения о всех службах Azure.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 3a6fae9e0f85f7767b8d2d0b7f1364dbd83a81db
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230249"
---
# <a name="tag-support-for-azure-resources"></a>Поддержка тегов для ресурсов Azure
В этой статье приводятся сведения о поддержке типом ресурса функции [тегов](resource-group-using-tags.md). The column labeled **Supports tags** indicates whether the resource type has a property for the tag. The column labeled **Tag in cost report** indicates whether that resource type passes the tag to the cost report.

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Jump to a resource provider namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights).
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DomainServices | ДА | ДА |
> | DomainServices / oucontainer | Нет | Нет |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | supportProviders | Нет | Нет |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Нет | Нет |
> | addsservices | Нет | Нет |
> | agents | Нет | Нет |
> | anonymousapiusers | Нет | Нет |
> | Конфигурация | Нет | Нет |
> | журналы | Нет | Нет |
> | reports | Нет | Нет |
> | servicehealthmetrics | Нет | Нет |
> | services; | Нет | Нет |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | конфигурации | Нет | Нет |
> | generateRecommendations | Нет | Нет |
> | metadata | Нет | Нет |
> | к просмотру фильмов | Нет | Нет |
> | suppressions | Нет | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | actionRules | ДА | ДА |
> | оповещения | Нет | Нет |
> | alertsList | Нет | Нет |
> | alertsMetaData | Нет | Нет |
> | alertsSummary | Нет | Нет |
> | alertsSummaryList | Нет | Нет |
> | feedback | Нет | Нет |
> | smartDetectorAlertRules | ДА | ДА |
> | smartDetectorRuntimeEnvironments | Нет | Нет |
> | smartGroups | Нет | Нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | серверы | ДА | ДА |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Нет | Нет |
> | Служба | ДА | ДА |
> | validateServiceName | Нет | Нет |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | ДА | ДА |
> | configurationStores / eventGridFilters | Нет | Нет |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Spring | ДА | ДА |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Нет | Нет |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Нет | Нет |
> | dataAliases | Нет | Нет |
> | denyAssignments | Нет | Нет |
> | elevateAccess | Нет | Нет |
> | findOrphanRoleAssignments | Нет | Нет |
> | locks | Нет | Нет |
> | permissions | Нет | Нет |
> | policyAssignments | Нет | Нет |
> | policyDefinitions | Нет | Нет |
> | policySetDefinitions | Нет | Нет |
> | providerOperations | Нет | Нет |
> | roleAssignments | Нет | Нет |
> | roleDefinitions | Нет | Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | automationAccounts | ДА | ДА |
> | automationAccounts / configurations | ДА | ДА |
> | automationAccounts / jobs | Нет | Нет |
> | automationAccounts / runbooks | ДА | ДА |
> | automationAccounts / softwareUpdateConfigurations | Нет | Нет |
> | automationAccounts / webhooks | Нет | Нет |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | ДА | ДА |
> | configurationStores / eventGridFilters | Нет | Нет |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | Нет | Нет |
> | environments / accounts | Нет | Нет |
> | environments / accounts / namespaces | Нет | Нет |
> | environments / accounts / namespaces / configurations | Нет | Нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | ДА | Нет |
> | b2ctenants | Нет | Нет |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | ДА | ДА |
> | postgresInstances | ДА | ДА |
> | sqlBigDataClusters | ДА | ДА |
> | sqlInstances | ДА | ДА |
> | sqlServerRegistrations | ДА | ДА |
> | sqlServerRegistrations / sqlServers | Нет | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registrations | ДА | ДА |
> | registrations / customerSubscriptions | Нет | Нет |
> | registrations / products | Нет | Нет |
> | verificationKeys | Нет | Нет |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | batchAccounts | ДА | ДА |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Нет | Нет |
> | billingAccounts / agreements | Нет | Нет |
> | billingAccounts / billingPermissions | Нет | Нет |
> | billingAccounts / billingProfiles | Нет | Нет |
> | billingAccounts / billingProfiles / billingPermissions | Нет | Нет |
> | billingAccounts / billingProfiles / billingRoleAssignments | Нет | Нет |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Нет | Нет |
> | billingAccounts / billingProfiles / billingSubscriptions | Нет | Нет |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Нет | Нет |
> | billingAccounts / billingProfiles / customers | Нет | Нет |
> | billingAccounts / billingProfiles / invoices | Нет | Нет |
> | billingAccounts / billingProfiles / invoices / pricesheet | Нет | Нет |
> | billingAccounts / billingProfiles / invoiceSections | Нет | Нет |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Нет | Нет |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Нет | Нет |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Нет | Нет |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Нет | Нет |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Нет | Нет |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Нет | Нет |
> | billingAccounts / billingProfiles / invoiceSections / products | Нет | Нет |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Нет | Нет |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Нет | Нет |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Нет | Нет |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Нет | Нет |
> | billingAccounts / BillingProfiles / patchOperations | Нет | Нет |
> | billingAccounts / billingProfiles / paymentMethods | Нет | Нет |
> | billingAccounts / billingProfiles / policies | Нет | Нет |
> | billingAccounts / billingProfiles / pricesheet | Нет | Нет |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Нет | Нет |
> | billingAccounts / billingProfiles / products | Нет | Нет |
> | billingAccounts / billingProfiles / transactions | Нет | Нет |
> | billingAccounts / billingRoleAssignments | Нет | Нет |
> | billingAccounts / billingRoleDefinitions | Нет | Нет |
> | billingAccounts / billingSubscriptions | Нет | Нет |
> | billingAccounts / billingSubscriptions / invoices | Нет | Нет |
> | billingAccounts / createBillingRoleAssignment | Нет | Нет |
> | billingAccounts / createInvoiceSectionOperations | Нет | Нет |
> | billingAccounts / customers | Нет | Нет |
> | billingAccounts / customers / billingPermissions | Нет | Нет |
> | billingAccounts / customers / billingSubscriptions | Нет | Нет |
> | billingAccounts / customers / initiateTransfer | Нет | Нет |
> | billingAccounts / customers / policies | Нет | Нет |
> | billingAccounts / customers / products | Нет | Нет |
> | billingAccounts / customers / transactions | Нет | Нет |
> | billingAccounts / customers / transfers | Нет | Нет |
> | billingAccounts / departments | Нет | Нет |
> | billingAccounts / enrollmentAccounts | Нет | Нет |
> | billingAccounts / invoices | Нет | Нет |
> | billingAccounts / invoiceSections | Нет | Нет |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Нет | Нет |
> | billingAccounts / invoiceSections / billingSubscriptions | Нет | Нет |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Нет | Нет |
> | billingAccounts / invoiceSections / elevate | Нет | Нет |
> | billingAccounts / invoiceSections / initiateTransfer | Нет | Нет |
> | billingAccounts / invoiceSections / patchOperations | Нет | Нет |
> | billingAccounts / invoiceSections / productMoveOperations | Нет | Нет |
> | billingAccounts / invoiceSections / products | Нет | Нет |
> | billingAccounts / invoiceSections / products / transfer | Нет | Нет |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Нет | Нет |
> | billingAccounts / invoiceSections / transactions | Нет | Нет |
> | billingAccounts / invoiceSections / transfers | Нет | Нет |
> | billingAccounts / lineOfCredit | Нет | Нет |
> | billingAccounts / patchOperations | Нет | Нет |
> | billingAccounts / paymentMethods | Нет | Нет |
> | billingAccounts / products | Нет | Нет |
> | billingAccounts / transactions | Нет | Нет |
> | billingPeriods | Нет | Нет |
> | billingPermissions | Нет | Нет |
> | billingProperty | Нет | Нет |
> | billingRoleAssignments | Нет | Нет |
> | billingRoleDefinitions | Нет | Нет |
> | createBillingRoleAssignment | Нет | Нет |
> | departments | Нет | Нет |
> | enrollmentAccounts | Нет | Нет |
> | invoices | Нет | Нет |
> | transfers | Нет | Нет |
> | transfers / acceptTransfer | Нет | Нет |
> | transfers / declineTransfer | Нет | Нет |
> | transfers / operationStatus | Нет | Нет |
> | transfers / validateTransfer | Нет | Нет |
> | validateAddress | Нет | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mapApis | ДА | ДА |
> | updateCommunicationPreference | Нет | Нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | ДА | ДА |
> | cordaMembers | ДА | ДА |
> | наблюдатели | ДА | ДА |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Нет | Нет |
> | blueprintAssignments / assignmentOperations | Нет | Нет |
> | blueprintAssignments / operations | Нет | Нет |
> | blueprints | Нет | Нет |
> | blueprints / artifacts | Нет | Нет |
> | blueprints / versions | Нет | Нет |
> | blueprints / versions / artifacts | Нет | Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | botServices | ДА | ДА |
> | botServices / channels | Нет | Нет |
> | botServices / connections | Нет | Нет |
> | языки | Нет | Нет |
> | шаблоны | Нет | Нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Redis | ДА | ДА |
> | RedisConfigDefinition | Нет | Нет |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Нет | Нет |
> | calculateExchange | Нет | Нет |
> | calculatePrice | Нет | Нет |
> | calculatePurchasePrice | Нет | Нет |
> | catalogs | Нет | Нет |
> | commercialReservationOrders | Нет | Нет |
> | exchange | Нет | Нет |
> | placePurchaseOrder | Нет | Нет |
> | reservationOrders | Нет | Нет |
> | reservationOrders / calculateRefund | Нет | Нет |
> | reservationOrders / merge | Нет | Нет |
> | reservationOrders / reservations | Нет | Нет |
> | reservationOrders / reservations / revisions | Нет | Нет |
> | reservationOrders / return | Нет | Нет |
> | reservationOrders / split | Нет | Нет |
> | reservationOrders / swap | Нет | Нет |
> | reservations | Нет | Нет |
> | ресурсов | Нет | Нет |
> | validateReservationOrder | Нет | Нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Нет | Нет |
> | CdnWebApplicationFirewallPolicies | ДА | ДА |
> | edgenodes | Нет | Нет |
> | профили | ДА | ДА |
> | profiles / endpoints | ДА | ДА |
> | profiles / endpoints / customdomains | Нет | Нет |
> | profiles / endpoints / origins | Нет | Нет |
> | validateProbe | Нет | Нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | certificateOrders | ДА | ДА |
> | certificateOrders / certificates | Нет | Нет |
> | validateCertificateRegistrationInformation | Нет | Нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Нет | Нет |
> | domainNames | Нет | Нет |
> | domainNames / capabilities | Нет | Нет |
> | domainNames / internalLoadBalancers | Нет | Нет |
> | domainNames / serviceCertificates | Нет | Нет |
> | domainNames / slots | Нет | Нет |
> | domainNames / slots / roles | Нет | Нет |
> | domainNames / slots / roles / metricDefinitions | Нет | Нет |
> | domainNames / slots / roles / metrics | Нет | Нет |
> | moveSubscriptionResources | Нет | Нет |
> | operatingSystemFamilies | Нет | Нет |
> | operatingSystems | Нет | Нет |
> | quotas | Нет | Нет |
> | resourceTypes | Нет | Нет |
> | validateSubscriptionMoveAvailability | Нет | Нет |
> | virtualMachines | Нет | Нет |
> | virtualMachines / diagnosticSettings | Нет | Нет |
> | virtualMachines / metricDefinitions | Нет | Нет |
> | virtualMachines / metrics | Нет | Нет |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Нет | Нет |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Нет | Нет |
> | expressRouteCrossConnections | Нет | Нет |
> | expressRouteCrossConnections / peerings | Нет | Нет |
> | gatewaySupportedDevices | Нет | Нет |
> | networkSecurityGroups | Нет | Нет |
> | quotas | Нет | Нет |
> | reservedIps | Нет | Нет |
> | virtualNetworks | Нет | Нет |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Нет | Нет |
> | virtualNetworks / virtualNetworkPeerings | Нет | Нет |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Нет | Нет |
> | disks | Нет | Нет |
> | images | Нет | Нет |
> | osImages | Нет | Нет |
> | osPlatformImages | Нет | Нет |
> | publicImages | Нет | Нет |
> | quotas | Нет | Нет |
> | storageAccounts | Нет | Нет |
> | storageAccounts / blobServices | Нет | Нет |
> | storageAccounts / fileServices | Нет | Нет |
> | storageAccounts / metricDefinitions | Нет | Нет |
> | storageAccounts / metrics | Нет | Нет |
> | storageAccounts / queueServices | Нет | Нет |
> | storageAccounts / services | Нет | Нет |
> | storageAccounts / services / diagnosticSettings | Нет | Нет |
> | storageAccounts / services / metricDefinitions | Нет | Нет |
> | storageAccounts / services / metrics | Нет | Нет |
> | storageAccounts / tableServices | Нет | Нет |
> | storageAccounts / vmImages | Нет | Нет |
> | vmImages | Нет | Нет |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | ДА | ДА |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | RateCard | Нет | Нет |
> | UsageAggregates | Нет | Нет |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilitySets | ДА | ДА |
> | diskEncryptionSets | ДА | ДА |
> | disks | ДА | ДА |
> | galleries | ДА | ДА |
> | galleries / applications | Нет | Нет |
> | galleries / applications / versions | Нет | Нет |
> | galleries / images | Нет | Нет |
> | galleries / images / versions | Нет | Нет |
> | hostGroups | ДА | ДА |
> | hostGroups / hosts | ДА | ДА |
> | images | ДА | ДА |
> | proximityPlacementGroups | ДА | ДА |
> | restorePointCollections | ДА | ДА |
> | restorePointCollections / restorePoints | Нет | Нет |
> | sharedVMImages | ДА | ДА |
> | sharedVMImages / versions | Нет | Нет |
> | snapshots | ДА | ДА |
> | virtualMachines | ДА | ДА |
> | virtualMachines / extensions | ДА | ДА |
> | virtualMachines / metricDefinitions | Нет | Нет |
> | virtualMachineScaleSets | ДА | ДА |
> | virtualMachineScaleSets / extensions | Нет | Нет |
> | virtualMachineScaleSets / networkInterfaces | Нет | Нет |
> | virtualMachineScaleSets / publicIPAddresses | Нет | Нет |
> | virtualMachineScaleSets / virtualMachines | Нет | Нет |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Нет | Нет |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Нет | Нет |
> | сведения о балансе. | Нет | Нет |
> | Расходы | Нет | Нет |
> | Расходы | Нет | Нет |
> | CostTags | Нет | Нет |
> | credits | Нет | Нет |
> | events | Нет | Нет |
> | Прогнозы | Нет | Нет |
> | lots | Нет | Нет |
> | Marketplace | Нет | Нет |
> | Pricesheets | Нет | Нет |
> | products | Нет | Нет |
> | ReservationDetails | Нет | Нет |
> | ReservationRecommendations | Нет | Нет |
> | ReservationSummaries | Нет | Нет |
> | ReservationTransactions | Нет | Нет |
> | Теги | Нет | Нет |
> | tenants | Нет | Нет |
> | Термины | Нет | Нет |
> | UsageDetails | Нет | Нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerGroups | ДА | ДА |
> | serviceAssociationLinks | Нет | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registries | ДА | ДА |
> | registries / builds | Нет | Нет |
> | registries / builds / cancel | Нет | Нет |
> | registries / builds / getLogLink | Нет | Нет |
> | registries / buildTasks | ДА | ДА |
> | registries / buildTasks / steps | Нет | Нет |
> | registries / eventGridFilters | Нет | Нет |
> | registries / generateCredentials | Нет | Нет |
> | registries / getBuildSourceUploadUrl | Нет | Нет |
> | registries / GetCredentials | Нет | Нет |
> | registries / importImage | Нет | Нет |
> | registries / queueBuild | Нет | Нет |
> | registries / regenerateCredential | Нет | Нет |
> | registries / regenerateCredentials | Нет | Нет |
> | registries / replications | ДА | ДА |
> | registries / runs | Нет | Нет |
> | registries / runs / cancel | Нет | Нет |
> | registries / scheduleRun | Нет | Нет |
> | registries / scopeMaps | Нет | Нет |
> | registries / taskRuns | ДА | ДА |
> | registries / tasks | ДА | ДА |
> | registries / tokens | Нет | Нет |
> | registries / updatePolicies | Нет | Нет |
> | registries / webhooks | ДА | ДА |
> | registries / webhooks / getCallbackConfig | Нет | Нет |
> | registries / webhooks / ping | Нет | Нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerServices | ДА | ДА |
> | managedClusters | ДА | ДА |
> | openShiftManagedClusters | ДА | ДА |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | ДА | ДА |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Оповещения | Нет | Нет |
> | BillingAccounts | Нет | Нет |
> | Расходы | Нет | Нет |
> | CloudConnectors | Нет | Нет |
> | Коннекторы | ДА | ДА |
> | Departments | Нет | Нет |
> | Измерения | Нет | Нет |
> | EnrollmentAccounts | Нет | Нет |
> | Экспорт | Нет | Нет |
> | ExternalBillingAccounts | Нет | Нет |
> | ExternalBillingAccounts / Alerts | Нет | Нет |
> | ExternalBillingAccounts / Dimensions | Нет | Нет |
> | ExternalBillingAccounts / Forecast | Нет | Нет |
> | ExternalBillingAccounts / Query | Нет | Нет |
> | ExternalSubscriptions | Нет | Нет |
> | ExternalSubscriptions / Alerts | Нет | Нет |
> | ExternalSubscriptions / Dimensions | Нет | Нет |
> | ExternalSubscriptions / Forecast | Нет | Нет |
> | ExternalSubscriptions / Query | Нет | Нет |
> | Forecast | Нет | Нет |
> | Запрос | Нет | Нет |
> | регистрация | Нет | Нет |
> | Reportconfigs | Нет | Нет |
> | Отчеты | Нет | Нет |
> | Настройки | Нет | Нет |
> | showbackRules | Нет | Нет |
> | Представления | Нет | Нет |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | requests | Нет | Нет |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | associations | Нет | Нет |
> | resourceProviders | ДА | ДА |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | ДА | ДА |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | ДА | ДА |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | ДА | Нет |
> | workspaces / virtualNetworkPeerings | Нет | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | catalogs | ДА | ДА |
> | datacatalogs | ДА | ДА |
> | datacatalogs / datasources | Нет | Нет |
> | datacatalogs / datasources / scans | Нет | Нет |
> | datacatalogs / datasources / scans / datasets | Нет | Нет |
> | datacatalogs / datasources / scans / triggers | Нет | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataFactories | ДА | Нет |
> | dataFactories / diagnosticSettings | Нет | Нет |
> | dataFactories / metricDefinitions | Нет | Нет |
> | dataFactorySchema | Нет | Нет |
> | factories | ДА | Нет |
> | factories / integrationRuntimes | Нет | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | ДА | ДА |
> | accounts / dataLakeStoreAccounts | Нет | Нет |
> | accounts / storageAccounts | Нет | Нет |
> | accounts / storageAccounts / containers | Нет | Нет |
> | accounts / transferAnalyticsUnits | Нет | Нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | ДА | ДА |
> | accounts / eventGridFilters | Нет | Нет |
> | accounts / firewallRules | Нет | Нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services; | Нет | Нет |
> | services / projects | Нет | Нет |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | ДА | ДА |
> | accounts / shares | Нет | Нет |
> | accounts / shares / datasets | Нет | Нет |
> | accounts / shares / invitations | Нет | Нет |
> | accounts / shares / providersharesubscriptions | Нет | Нет |
> | accounts / shares / synchronizationSettings | Нет | Нет |
> | accounts / sharesubscriptions | Нет | Нет |
> | accounts / sharesubscriptions / consumerSourceDataSets | Нет | Нет |
> | accounts / sharesubscriptions / datasetmappings | Нет | Нет |
> | accounts / sharesubscriptions / triggers | Нет | Нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | серверы | ДА | ДА |
> | servers / advisors | Нет | Нет |
> | servers / privateEndpointConnectionProxies | Нет | Нет |
> | servers / privateEndpointConnections | Нет | Нет |
> | servers / privateLinkResources | Нет | Нет |
> | servers / queryTexts | Нет | Нет |
> | servers / recoverableServers | Нет | Нет |
> | servers / topQueryStatistics | Нет | Нет |
> | servers / virtualNetworkRules | Нет | Нет |
> | servers / waitStatistics | Нет | Нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | серверы | ДА | ДА |
> | servers / advisors | Нет | Нет |
> | servers / privateEndpointConnectionProxies | Нет | Нет |
> | servers / privateEndpointConnections | Нет | Нет |
> | servers / privateLinkResources | Нет | Нет |
> | servers / queryTexts | Нет | Нет |
> | servers / recoverableServers | Нет | Нет |
> | servers / topQueryStatistics | Нет | Нет |
> | servers / virtualNetworkRules | Нет | Нет |
> | servers / waitStatistics | Нет | Нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | serverGroups | ДА | ДА |
> | серверы | ДА | ДА |
> | servers / advisors | Нет | Нет |
> | servers / keys | Нет | Нет |
> | servers / privateEndpointConnectionProxies | Нет | Нет |
> | servers / privateEndpointConnections | Нет | Нет |
> | servers / privateLinkResources | Нет | Нет |
> | servers / queryTexts | Нет | Нет |
> | servers / recoverableServers | Нет | Нет |
> | servers / topQueryStatistics | Нет | Нет |
> | servers / virtualNetworkRules | Нет | Нет |
> | servers / waitStatistics | Нет | Нет |
> | serversv2 | ДА | ДА |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | artifactSources | ДА | ДА |
> | rollouts | ДА | ДА |
> | serviceTopologies | ДА | ДА |
> | serviceTopologies / services | ДА | ДА |
> | serviceTopologies / services / serviceUnits | ДА | ДА |
> | steps | ДА | ДА |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationgroups | ДА | ДА |
> | applicationgroups / applications | Нет | Нет |
> | applicationgroups / desktops | Нет | Нет |
> | applicationgroups / startmenuitems | Нет | Нет |
> | hostpools | ДА | ДА |
> | hostpools / sessionhosts | Нет | Нет |
> | hostpools / sessionhosts / usersessions | Нет | Нет |
> | hostpools / usersessions | Нет | Нет |
> | workspaces | ДА | ДА |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | ElasticPools | ДА | ДА |
> | ElasticPools / IotHubTenants | ДА | ДА |
> | IotHubs | ДА | ДА |
> | IotHubs / eventGridFilters | Нет | Нет |
> | ProvisioningServices | ДА | ДА |
> | usages | Нет | Нет |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | pipelines | ДА | ДА |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | controllers | ДА | ДА |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labcenters | ДА | ДА |
> | labs | ДА | ДА |
> | labs / environments | ДА | ДА |
> | labs / serviceRunners | ДА | ДА |
> | labs / virtualMachines | ДА | ДА |
> | schedules | ДА | ДА |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Нет | Нет |
> | databaseAccounts | ДА | ДА |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | ДА | ДА |
> | domains / domainOwnershipIdentifiers | Нет | Нет |
> | generateSsoRequest | Нет | Нет |
> | topLevelDomains | Нет | Нет |
> | validateDomainRegistrationInformation | Нет | Нет |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Нет | Нет |
> | lcsprojects / clouddeployments | Нет | Нет |
> | lcsprojects / connectors | Нет | Нет |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services; | ДА | ДА |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | ДА | ДА |
> | domains / topics | Нет | Нет |
> | eventSubscriptions | Нет | Нет |
> | extensionTopics | Нет | Нет |
> | topics | ДА | ДА |
> | topicTypes | Нет | Нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | ДА | ДА |
> | namespaces | ДА | ДА |
> | namespaces / authorizationrules | Нет | Нет |
> | namespaces / disasterrecoveryconfigs | Нет | Нет |
> | namespaces / eventhubs | Нет | Нет |
> | namespaces / eventhubs / authorizationrules | Нет | Нет |
> | namespaces / eventhubs / consumergroups | Нет | Нет |
> | namespaces / networkrulesets | Нет | Нет |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | эластичной базы данных | Нет | Нет |
> | providers | Нет | Нет |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | enroll | Нет | Нет |
> | galleryitems | Нет | Нет |
> | generateartifactaccessuri | Нет | Нет |
> | myareas | Нет | Нет |
> | myareas / areas | Нет | Нет |
> | myareas / areas / areas | Нет | Нет |
> | myareas / areas / areas / galleryitems | Нет | Нет |
> | myareas / areas / galleryitems | Нет | Нет |
> | myareas / galleryitems | Нет | Нет |
> | регистрация | Нет | Нет |
> | ресурсов | Нет | Нет |
> | retrieveresourcesbyid | Нет | Нет |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | ДА | ДА |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationProfileAssignments | Нет | Нет |
> | guestConfigurationAssignments | Нет | Нет |
> | software | Нет | Нет |
> | softwareUpdateProfile | Нет | Нет |
> | softwareUpdates | Нет | Нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hanaInstances | ДА | ДА |
> | sapMonitors | ДА | ДА |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | ДА | ДА |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | ДА | ДА |
> | clusters / applications | Нет | Нет |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services; | ДА | ДА |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | machines | ДА | ДА |
> | machines / extensions | ДА | ДА |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataManagers | ДА | ДА |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | ДА | ДА |
> | networkScopes | ДА | ДА |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | ДА | ДА |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Нет | Нет |
> | diagnosticSettingsCategories | Нет | Нет |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appTemplates | Нет | Нет |
> | IoTApps | ДА | ДА |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Граф | ДА | ДА |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Нет | Нет |
> | hsmPools | ДА | ДА |
> | vaults | ДА | ДА |
> | vaults / accessPolicies | Нет | Нет |
> | vaults / eventGridFilters | Нет | Нет |
> | vaults / secrets | Нет | Нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | ДА | ДА |
> | clusters / attacheddatabaseconfigurations | Нет | Нет |
> | clusters / databases | Нет | Нет |
> | clusters / databases / dataconnections | Нет | Нет |
> | clusters / databases / eventhubconnections | Нет | Нет |
> | clusters / sharedidentities | Нет | Нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labaccounts | ДА | ДА |
> | пользователей | Нет | Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | ДА | ДА |
> | integrationAccounts | ДА | ДА |
> | integrationServiceEnvironments | ДА | ДА |
> | integrationServiceEnvironments / managedApis | ДА | ДА |
> | isolatedEnvironments | ДА | ДА |
> | workflows | ДА | ДА |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | ДА | ДА |
> | webServices | ДА | ДА |
> | Рабочие области | ДА | ДА |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | ДА | ДА |
> | workspaces / computes | Нет | Нет |
> | workspaces / eventGridFilters | Нет | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Identities | Нет | Нет |
> | userAssignedIdentities | ДА | ДА |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Нет | Нет |
> | registrationAssignments | Нет | Нет |
> | registrationDefinitions | Нет | Нет |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | getEntities | Нет | Нет |
> | managementGroups | Нет | Нет |
> | ресурсов | Нет | Нет |
> | startTenantBackfill | Нет | Нет |
> | tenantBackfillStatus | Нет | Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | ДА | ДА |
> | accounts / eventGridFilters | Нет | Нет |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | offers | Нет | Нет |
> | offerTypes | Нет | Нет |
> | offerTypes / publishers | Нет | Нет |
> | offerTypes / publishers / offers | Нет | Нет |
> | offerTypes / publishers / offers / plans | Нет | Нет |
> | offerTypes / publishers / offers / plans / agreements | Нет | Нет |
> | offerTypes / publishers / offers / plans / configs | Нет | Нет |
> | offerTypes / publishers / offers / plans / configs / importImage | Нет | Нет |
> | privategalleryitems | Нет | Нет |
> | products | Нет | Нет |
> | publishers | Нет | Нет |
> | publishers / offers | Нет | Нет |
> | publishers / offers / amendments | Нет | Нет |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicDevServices | ДА | ДА |
> | updateCommunicationPreference | Нет | Нет |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | agreements | Нет | Нет |
> | offertypes | Нет | Нет |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mediaservices | ДА | ДА |
> | mediaservices / accountFilters | Нет | Нет |
> | mediaservices / assets | Нет | Нет |
> | mediaservices / assets / assetFilters | Нет | Нет |
> | mediaservices / contentKeyPolicies | Нет | Нет |
> | mediaservices / eventGridFilters | Нет | Нет |
> | mediaservices / liveEventOperations | Нет | Нет |
> | mediaservices / liveEvents | ДА | ДА |
> | mediaservices / liveEvents / liveOutputs | Нет | Нет |
> | mediaservices / liveOutputOperations | Нет | Нет |
> | mediaservices / mediaGraphs | Нет | Нет |
> | mediaservices / streamingEndpointOperations | Нет | Нет |
> | mediaservices / streamingEndpoints | ДА | ДА |
> | mediaservices / streamingLocators | Нет | Нет |
> | mediaservices / streamingPolicies | Нет | Нет |
> | mediaservices / transforms | Нет | Нет |
> | mediaservices / transforms / jobs | Нет | Нет |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appClusters | ДА | ДА |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | ДА | ДА |
> | migrateprojects | ДА | ДА |
> | projects | ДА | ДА |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | ДА | ДА |
> | objectUnderstandingAccounts | ДА | ДА |
> | remoteRenderingAccounts | ДА | ДА |
> | spatialAnchorsAccounts | ДА | ДА |
> | surfaceReconstructionAccounts | ДА | ДА |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | ДА | Нет |
> | netAppAccounts / capacityPools | ДА | Нет |
> | netAppAccounts / capacityPools / volumes | ДА | Нет |
> | netAppAccounts / capacityPools / volumes / mountTargets | ДА | Нет |
> | netAppAccounts / capacityPools / volumes / snapshots | ДА | Нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationGateways | ДА | ДА |
> | applicationGatewayWebApplicationFirewallPolicies | ДА | ДА |
> | applicationSecurityGroups | ДА | ДА |
> | azureFirewallFqdnTags | Нет | Нет |
> | azureFirewalls | ДА | Нет |
> | bastionHosts | ДА | ДА |
> | bgpServiceCommunities | Нет | Нет |
> | connections | ДА | ДА |
> | ddosCustomPolicies | ДА | ДА |
> | ddosProtectionPlans | ДА | ДА |
> | dnsOperationStatuses | Нет | Нет |
> | dnszones | ДА | ДА |
> | dnszones / A | Нет | Нет |
> | dnszones / AAAA | Нет | Нет |
> | dnszones / all | Нет | Нет |
> | dnszones / CAA | Нет | Нет |
> | dnszones / CNAME | Нет | Нет |
> | dnszones / MX | Нет | Нет |
> | dnszones / NS | Нет | Нет |
> | dnszones / PTR | Нет | Нет |
> | dnszones / recordsets | Нет | Нет |
> | dnszones / SOA | Нет | Нет |
> | dnszones / SRV | Нет | Нет |
> | dnszones / TXT | Нет | Нет |
> | expressRouteCircuits | ДА | ДА |
> | expressRouteCrossConnections | ДА | ДА |
> | expressRouteGateways | ДА | ДА |
> | expressRoutePorts | ДА | ДА |
> | expressRouteServiceProviders | Нет | Нет |
> | firewallPolicies | ДА | ДА |
> | frontdoors | Yes, but limited (see [note below](#frontdoor)) | ДА |
> | frontdoorWebApplicationFirewallManagedRuleSets | Yes, but limited (see [note below](#frontdoor)) | Нет |
> | frontdoorWebApplicationFirewallPolicies | Yes, but limited (see [note below](#frontdoor)) | ДА |
> | getDnsResourceReference | Нет | Нет |
> | internalNotify | Нет | Нет |
> | loadBalancers | ДА | Нет |
> | localNetworkGateways | ДА | ДА |
> | natGateways | ДА | ДА |
> | networkIntentPolicies | ДА | ДА |
> | networkInterfaces | ДА | ДА |
> | networkProfiles | ДА | ДА |
> | networkSecurityGroups | ДА | ДА |
> | networkWatchers | ДА | Нет |
> | networkWatchers / connectionMonitors | ДА | Нет |
> | networkWatchers / lenses | ДА | Нет |
> | networkWatchers / pingMeshes | ДА | Нет |
> | p2sVpnGateways | ДА | ДА |
> | privateDnsOperationStatuses | Нет | Нет |
> | privateDnsZones | ДА | ДА |
> | privateDnsZones / A | Нет | Нет |
> | privateDnsZones / AAAA | Нет | Нет |
> | privateDnsZones / all | Нет | Нет |
> | privateDnsZones / CNAME | Нет | Нет |
> | privateDnsZones / MX | Нет | Нет |
> | privateDnsZones / PTR | Нет | Нет |
> | privateDnsZones / SOA | Нет | Нет |
> | privateDnsZones / SRV | Нет | Нет |
> | privateDnsZones / TXT | Нет | Нет |
> | privateDnsZones / virtualNetworkLinks | ДА | ДА |
> | privateEndpoints | ДА | ДА |
> | privateLinkServices | ДА | ДА |
> | publicIPAddresses | ДА | ДА |
> | publicIPPrefixes | ДА | ДА |
> | routeFilters | ДА | ДА |
> | routeTables | ДА | ДА |
> | serviceEndpointPolicies | ДА | ДА |
> | trafficManagerGeographicHierarchies | Нет | Нет |
> | trafficmanagerprofiles | ДА | ДА |
> | trafficmanagerprofiles/heatMaps | Нет | Нет |
> | trafficManagerUserMetricsKeys | Нет | Нет |
> | virtualHubs | ДА | ДА |
> | virtualNetworkGateways | ДА | ДА |
> | virtualNetworks | ДА | ДА |
> | virtualNetworkTaps | ДА | ДА |
> | virtualWans | ДА | ДА |
> | vpnGateways | ДА | Нет |
> | vpnSites | ДА | ДА |
> | webApplicationFirewallPolicies | ДА | ДА |

<a id="frontdoor" />

> [!NOTE]
> For Azure Front Door Service, you can apply tags when creating the resource, but updating or adding tags is not currently supported.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | ДА | Нет |
> | namespaces / notificationHubs | ДА | Нет |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | osNamespaces | ДА | ДА |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | HyperVSites | ДА | ДА |
> | ImportSites | ДА | ДА |
> | ServerSites | ДА | ДА |
> | VMwareSites | ДА | ДА |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | ДА | ДА |
> | устройства | Нет | Нет |
> | linkTargets | Нет | Нет |
> | storageInsightConfigs | Нет | Нет |
> | workspaces | ДА | ДА |
> | workspaces / dataSources | Нет | Нет |
> | workspaces / linkedServices | Нет | Нет |
> | workspaces / query | Нет | Нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managementassociations | Нет | Нет |
> | managementconfigurations | ДА | ДА |
> | solutions | ДА | ДА |
> | узел "Представления" | ДА | ДА |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Нет | Нет |
> | peerAsns | Нет | Нет |
> | peerings | ДА | ДА |
> | peeringServiceProviders | Нет | Нет |
> | peeringServices | ДА | ДА |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | policyEvents | Нет | Нет |
> | policyMetadata | Нет | Нет |
> | policyStates | Нет | Нет |
> | policyTrackedResources | Нет | Нет |
> | remediations | Нет | Нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | consoles | Нет | Нет |
> | dashboards | ДА | ДА |
> | userSettings | Нет | Нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | ДА | ДА |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capacities | ДА | ДА |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Нет | Нет |
> | vaults | ДА | ДА |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | ДА | ДА |
> | namespaces / authorizationrules | Нет | Нет |
> | namespaces / hybridconnections | Нет | Нет |
> | namespaces / hybridconnections / authorizationrules | Нет | Нет |
> | namespaces / wcfrelays | Нет | Нет |
> | namespaces / wcfrelays / authorizationrules | Нет | Нет |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Нет | Нет |
> | collections | ДА | ДА |
> | collections / applications | Нет | Нет |
> | collections / securityprincipals | Нет | Нет |
> | templateImages | Нет | Нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | запросы | ДА | ДА |
> | resourceChangeDetails | Нет | Нет |
> | resourceChanges | Нет | Нет |
> | ресурсов | Нет | Нет |
> | resourcesHistory | Нет | Нет |
> | subscriptionsStatus | Нет | Нет |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Нет | Нет |
> | childAvailabilityStatuses | Нет | Нет |
> | childResources | Нет | Нет |
> | events | Нет | Нет |
> | impactedResources | Нет | Нет |
> | metadata | Нет | Нет |
> | Уведомления | Нет | Нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deployments | ДА | Нет |
> | deployments / operations | Нет | Нет |
> | deploymentScripts | ДА | ДА |
> | deploymentScripts / logs | Нет | Нет |
> | links | Нет | Нет |
> | notifyResourceJobs | Нет | Нет |
> | providers | Нет | Нет |
> | resourceGroups | ДА | Нет |
> | ресурсов | Нет | Нет |
> | subscriptions | Нет | Нет |
> | subscriptions / providers | Нет | Нет |
> | subscriptions / resources | Нет | Нет |
> | subscriptions / tagnames | Нет | Нет |
> | subscriptions / tagNames / tagValues | Нет | Нет |
> | tenants | Нет | Нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | приложений | ДА | ДА |
> | saasresources | Нет | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobcollections | ДА | ДА |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Нет | Нет |
> | searchServices | ДА | ДА |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Нет | Нет |
> | advancedThreatProtectionSettings | Нет | Нет |
> | оповещения | Нет | Нет |
> | allowedConnections | Нет | Нет |
> | applicationWhitelistings | Нет | Нет |
> | assessmentMetadata | Нет | Нет |
> | assessments | Нет | Нет |
> | autoDismissAlertsRules | Нет | Нет |
> | automations | ДА | ДА |
> | AutoProvisioningSettings | Нет | Нет |
> | Compliances | Нет | Нет |
> | dataCollectionAgents | Нет | Нет |
> | deviceSecurityGroups | Нет | Нет |
> | discoveredSecuritySolutions | Нет | Нет |
> | externalSecuritySolutions | Нет | Нет |
> | InformationProtectionPolicies | Нет | Нет |
> | iotSecuritySolutions | ДА | ДА |
> | iotSecuritySolutions / analyticsModels | Нет | Нет |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Нет | Нет |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Нет | Нет |
> | jitNetworkAccessPolicies | Нет | Нет |
> | networkData | Нет | Нет |
> | политики | Нет | Нет |
> | pricings | Нет | Нет |
> | regulatoryComplianceStandards | Нет | Нет |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Нет | Нет |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Нет | Нет |
> | securityContacts | Нет | Нет |
> | securitySolutions | Нет | Нет |
> | securitySolutionsReferenceData | Нет | Нет |
> | securityStatuses | Нет | Нет |
> | securityStatusesSummaries | Нет | Нет |
> | serverVulnerabilityAssessments | Нет | Нет |
> | Параметры | Нет | Нет |
> | subAssessments | Нет | Нет |
> | задачи; | Нет | Нет |
> | topologies | Нет | Нет |
> | workspaceSettings | Нет | Нет |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Нет | Нет |
> | diagnosticSettingsCategories | Нет | Нет |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aggregations | Нет | Нет |
> | alertRules | Нет | Нет |
> | alertRuleTemplates | Нет | Нет |
> | bookmarks | Нет | Нет |
> | cases | Нет | Нет |
> | dataConnectors | Нет | Нет |
> | Сущности | Нет | Нет |
> | entityQueries | Нет | Нет |
> | officeConsents | Нет | Нет |
> | Параметры | Нет | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | ДА | Нет |
> | namespaces / authorizationrules | Нет | Нет |
> | namespaces / disasterrecoveryconfigs | Нет | Нет |
> | namespaces / eventgridfilters | Нет | Нет |
> | namespaces / networkrulesets | Нет | Нет |
> | namespaces / queues | Нет | Нет |
> | namespaces / queues / authorizationrules | Нет | Нет |
> | namespaces / topics | Нет | Нет |
> | namespaces / topics / authorizationrules | Нет | Нет |
> | namespaces / topics / subscriptions | Нет | Нет |
> | namespaces / topics / subscriptions / rules | Нет | Нет |
> | premiumMessagingRegions | Нет | Нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | приложений | ДА | ДА |
> | clusters | ДА | ДА |
> | clusters / applications | Нет | Нет |
> | containerGroups | ДА | ДА |
> | containerGroupSets | ДА | ДА |
> | edgeclusters | ДА | ДА |
> | edgeclusters / applications | Нет | Нет |
> | networks | ДА | ДА |
> | secretstores | ДА | ДА |
> | secretstores / certificates | Нет | Нет |
> | secretstores / secrets | Нет | Нет |
> | volumes. | ДА | ДА |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | приложений | ДА | ДА |
> | containerGroups | ДА | ДА |
> | gateways | ДА | ДА |
> | networks | ДА | ДА |
> | секретные коды | ДА | ДА |
> | volumes. | ДА | ДА |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Нет | Нет |
> | providerRegistrations / resourceTypeRegistrations | Нет | Нет |
> | rollouts | ДА | ДА |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SignalR | ДА | ДА |
> | SignalR / eventGridFilters | Нет | Нет |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | ДА | ДА |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Нет | Нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | ДА | ДА |
> | приложений | ДА | ДА |
> | jitRequests | ДА | ДА |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managedInstances | ДА | ДА |
> | managedInstances / databases | Yes (see [note below](#sqlnote)) | ДА |
> | managedInstances / databases / backupShortTermRetentionPolicies | Нет | Нет |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Нет | Нет |
> | managedInstances / databases / vulnerabilityAssessments | Нет | Нет |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Нет | Нет |
> | managedInstances / encryptionProtector | Нет | Нет |
> | managedInstances / keys | Нет | Нет |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Нет | Нет |
> | managedInstances / vulnerabilityAssessments | Нет | Нет |
> | серверы | ДА | ДА |
> | servers / administrators | Нет | Нет |
> | servers / communicationLinks | Нет | Нет |
> | servers / databases | Yes (see [note below](#sqlnote)) | ДА |
> | servers / encryptionProtector | Нет | Нет |
> | servers / firewallRules | Нет | Нет |
> | servers / keys | Нет | Нет |
> | servers / restorableDroppedDatabases | Нет | Нет |
> | servers / serviceobjectives | Нет | Нет |
> | servers / tdeCertificates | Нет | Нет |
> | virtualClusters | Нет | Нет |

<a id="sqlnote" />

> [!NOTE]
> База данных Master не поддерживает теги, но другие базы данных, в том числе базы данных хранилища данных Azure SQL, поддерживают их. Базы данных Хранилища данных SQL Azure должны быть в активном состоянии (работа не приостановлена).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | ДА | ДА |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Нет | Нет |
> | SqlVirtualMachines | ДА | ДА |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageAccounts | ДА | ДА |
> | storageAccounts / blobServices | Нет | Нет |
> | storageAccounts / fileServices | Нет | Нет |
> | storageAccounts / queueServices | Нет | Нет |
> | storageAccounts / services | Нет | Нет |
> | storageAccounts / services / metricDefinitions | Нет | Нет |
> | storageAccounts / tableServices | Нет | Нет |
> | usages | Нет | Нет |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | caches | ДА | ДА |
> | caches / storageTargets | Нет | Нет |
> | usageModels | Нет | Нет |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Нет | Нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | ДА | ДА |
> | storageSyncServices / registeredServers | Нет | Нет |
> | storageSyncServices / syncGroups | Нет | Нет |
> | storageSyncServices / syncGroups / cloudEndpoints | Нет | Нет |
> | storageSyncServices / syncGroups / serverEndpoints | Нет | Нет |
> | storageSyncServices / workflows | Нет | Нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | ДА | ДА |
> | storageSyncServices / registeredServers | Нет | Нет |
> | storageSyncServices / syncGroups | Нет | Нет |
> | storageSyncServices / syncGroups / cloudEndpoints | Нет | Нет |
> | storageSyncServices / syncGroups / serverEndpoints | Нет | Нет |
> | storageSyncServices / workflows | Нет | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | ДА | ДА |
> | storageSyncServices / registeredServers | Нет | Нет |
> | storageSyncServices / syncGroups | Нет | Нет |
> | storageSyncServices / syncGroups / cloudEndpoints | Нет | Нет |
> | storageSyncServices / syncGroups / serverEndpoints | Нет | Нет |
> | storageSyncServices / workflows | Нет | Нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managers | ДА | ДА |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Да (см. примечание ниже) | ДА |

> [!NOTE]
> Невозможно добавить тег при выполнении streamingjobs. Остановите ресурс, чтобы добавить тег.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | cancel | Нет | Нет |
> | CreateSubscription | Нет | Нет |
> | enable | Нет | Нет |
> | rename | Нет | Нет |
> | SubscriptionDefinitions | Нет | Нет |
> | SubscriptionOperations | Нет | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | ДА | Нет |
> | environments / accessPolicies | Нет | Нет |
> | environments / eventsources | ДА | Нет |
> | environments / referenceDataSets | ДА | Нет |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | ДА | ДА |
> | dedicatedCloudServices | ДА | ДА |
> | virtualMachines | ДА | ДА |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Нет | Нет |
> | apiManagementAccounts / apiAcls | Нет | Нет |
> | apiManagementAccounts / apis | Нет | Нет |
> | apiManagementAccounts / apis / apiAcls | Нет | Нет |
> | apiManagementAccounts / apis / connectionAcls | Нет | Нет |
> | apiManagementAccounts / apis / connections | Нет | Нет |
> | apiManagementAccounts / apis / connections / connectionAcls | Нет | Нет |
> | apiManagementAccounts / apis / localizedDefinitions | Нет | Нет |
> | apiManagementAccounts / connectionAcls | Нет | Нет |
> | apiManagementAccounts / connections | Нет | Нет |
> | billingMeters | Нет | Нет |
> | certificates | ДА | ДА |
> | connectionGateways | ДА | ДА |
> | connections | ДА | ДА |
> | customApis | ДА | ДА |
> | deletedSites | Нет | Нет |
> | функции | Нет | Нет |
> | hostingEnvironments | ДА | ДА |
> | hostingEnvironments / multiRolePools | Нет | Нет |
> | hostingEnvironments / workerPools | Нет | Нет |
> | publishingUsers | Нет | Нет |
> | к просмотру фильмов | Нет | Нет |
> | resourceHealthMetadata | Нет | Нет |
> | runtimes | Нет | Нет |
> | serverFarms | ДА | ДА |
> | serverFarms / eventGridFilters | Нет | Нет |
> | sites | ДА | ДА |
> | sites / config  | Нет | Нет |
> | sites / eventGridFilters | Нет | Нет |
> | sites / hostNameBindings | Нет | Нет |
> | sites / networkConfig | Нет | Нет |
> | sites / premieraddons | ДА | ДА |
> | sites / slots | ДА | ДА |
> | sites / slots / eventGridFilters | Нет | Нет |
> | sites / slots / hostNameBindings | Нет | Нет |
> | sites / slots / networkConfig | Нет | Нет |
> | sourceControls | Нет | Нет |
> | validate | Нет | Нет |
> | verifyHostingEnvironmentVnet | Нет | Нет |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Нет | Нет |
> | diagnosticSettingsCategories | Нет | Нет |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DeviceServices | ДА | ДА |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | Нет | Нет |
> | componentsSummary | Нет | Нет |
> | monitorInstances | Нет | Нет |
> | monitorInstancesSummary | Нет | Нет |
> | monitors | Нет | Нет |
> | notificationSettings | Нет | Нет |

## <a name="next-steps"></a>Дальнейшие действия

Сведения о том, как применить теги к ресурсам, см. в статье, посвященной [использованию тегов для организации ресурсов Azure](resource-group-using-tags.md).
