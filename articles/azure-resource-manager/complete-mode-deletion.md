---
title: Удаление ресурсов в полном режиме
description: Здесь показано, как происходит удаление ресурсов в полном режиме в шаблонах Azure Resource Manager по типу ресурса.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 075dd5071d6c59c6cc73fff8d51a495b012665dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232691"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Удаление ресурсов Azure для развертываний в полном режиме

В этой статье описывается, как процесс удаления будет обрабатываться различными типами, когда он не происходит в шаблоне, развернутом в завершенном режиме.

The resource types marked with **Yes** are deleted when the type isn't in the template deployed with complete mode.

The resource types marked with **No** aren't automatically deleted when not in the template; however, they're deleted if the parent resource is deleted. Чтобы получить полное описание поведения см. статью [Режимы развертывания в Azure Resource Manager](deployment-modes.md).

If you deploy to [more than one resource group in a template](resource-manager-cross-resource-group-deployment.md), resources in the resource group specified in the deployment operation are eligible to be deleted. Resources in the secondary resource groups aren't deleted.

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
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | DomainServices | ДА |
> | DomainServices / oucontainer | Нет |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | supportProviders | Нет |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | aadsupportcases | Нет |
> | addsservices | Нет |
> | agents | Нет |
> | anonymousapiusers | Нет |
> | Конфигурация | Нет |
> | журналы | Нет |
> | reports | Нет |
> | servicehealthmetrics | Нет |
> | services; | Нет |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | конфигурации | Нет |
> | generateRecommendations | Нет |
> | metadata | Нет |
> | к просмотру фильмов | Нет |
> | suppressions | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | actionRules | ДА |
> | оповещения | Нет |
> | alertsList | Нет |
> | alertsMetaData | Нет |
> | alertsSummary | Нет |
> | alertsSummaryList | Нет |
> | feedback | Нет |
> | smartDetectorAlertRules | ДА |
> | smartDetectorRuntimeEnvironments | Нет |
> | smartGroups | Нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | серверы | ДА |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | reportFeedback | Нет |
> | Служба | ДА |
> | validateServiceName | Нет |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | configurationStores | ДА |
> | configurationStores / eventGridFilters | Нет |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Spring | ДА |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | attestationProviders | Нет |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | classicAdministrators | Нет |
> | dataAliases | Нет |
> | denyAssignments | Нет |
> | elevateAccess | Нет |
> | findOrphanRoleAssignments | Нет |
> | locks | Нет |
> | permissions | Нет |
> | policyAssignments | Нет |
> | policyDefinitions | Нет |
> | policySetDefinitions | Нет |
> | providerOperations | Нет |
> | roleAssignments | Нет |
> | roleDefinitions | Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | automationAccounts | ДА |
> | automationAccounts / configurations | ДА |
> | automationAccounts / jobs | Нет |
> | automationAccounts / runbooks | ДА |
> | automationAccounts / softwareUpdateConfigurations | Нет |
> | automationAccounts / webhooks | Нет |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | configurationStores | ДА |
> | configurationStores / eventGridFilters | Нет |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | environments | Нет |
> | environments / accounts | Нет |
> | environments / accounts / namespaces | Нет |
> | environments / accounts / namespaces / configurations | Нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | b2cDirectories | ДА |
> | b2ctenants | Нет |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | hybridDataManagers | ДА |
> | postgresInstances | ДА |
> | sqlBigDataClusters | ДА |
> | sqlInstances | ДА |
> | sqlServerRegistrations | ДА |
> | sqlServerRegistrations / sqlServers | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | registrations | ДА |
> | registrations / customerSubscriptions | Нет |
> | registrations / products | Нет |
> | verificationKeys | Нет |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | batchAccounts | ДА |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | billingAccounts | Нет |
> | billingAccounts / agreements | Нет |
> | billingAccounts / billingPermissions | Нет |
> | billingAccounts / billingProfiles | Нет |
> | billingAccounts / billingProfiles / billingPermissions | Нет |
> | billingAccounts / billingProfiles / billingRoleAssignments | Нет |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Нет |
> | billingAccounts / billingProfiles / billingSubscriptions | Нет |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Нет |
> | billingAccounts / billingProfiles / customers | Нет |
> | billingAccounts / billingProfiles / invoices | Нет |
> | billingAccounts / billingProfiles / invoices / pricesheet | Нет |
> | billingAccounts / billingProfiles / invoiceSections | Нет |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Нет |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Нет |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Нет |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Нет |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Нет |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Нет |
> | billingAccounts / billingProfiles / invoiceSections / products | Нет |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Нет |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Нет |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Нет |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Нет |
> | billingAccounts / BillingProfiles / patchOperations | Нет |
> | billingAccounts / billingProfiles / paymentMethods | Нет |
> | billingAccounts / billingProfiles / policies | Нет |
> | billingAccounts / billingProfiles / pricesheet | Нет |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Нет |
> | billingAccounts / billingProfiles / products | Нет |
> | billingAccounts / billingProfiles / transactions | Нет |
> | billingAccounts / billingRoleAssignments | Нет |
> | billingAccounts / billingRoleDefinitions | Нет |
> | billingAccounts / billingSubscriptions | Нет |
> | billingAccounts / billingSubscriptions / invoices | Нет |
> | billingAccounts / createBillingRoleAssignment | Нет |
> | billingAccounts / createInvoiceSectionOperations | Нет |
> | billingAccounts / customers | Нет |
> | billingAccounts / customers / billingPermissions | Нет |
> | billingAccounts / customers / billingSubscriptions | Нет |
> | billingAccounts / customers / initiateTransfer | Нет |
> | billingAccounts / customers / policies | Нет |
> | billingAccounts / customers / products | Нет |
> | billingAccounts / customers / transactions | Нет |
> | billingAccounts / customers / transfers | Нет |
> | billingAccounts / departments | Нет |
> | billingAccounts / enrollmentAccounts | Нет |
> | billingAccounts / invoices | Нет |
> | billingAccounts / invoiceSections | Нет |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Нет |
> | billingAccounts / invoiceSections / billingSubscriptions | Нет |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Нет |
> | billingAccounts / invoiceSections / elevate | Нет |
> | billingAccounts / invoiceSections / initiateTransfer | Нет |
> | billingAccounts / invoiceSections / patchOperations | Нет |
> | billingAccounts / invoiceSections / productMoveOperations | Нет |
> | billingAccounts / invoiceSections / products | Нет |
> | billingAccounts / invoiceSections / products / transfer | Нет |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Нет |
> | billingAccounts / invoiceSections / transactions | Нет |
> | billingAccounts / invoiceSections / transfers | Нет |
> | billingAccounts / lineOfCredit | Нет |
> | billingAccounts / patchOperations | Нет |
> | billingAccounts / paymentMethods | Нет |
> | billingAccounts / products | Нет |
> | billingAccounts / transactions | Нет |
> | billingPeriods | Нет |
> | billingPermissions | Нет |
> | billingProperty | Нет |
> | billingRoleAssignments | Нет |
> | billingRoleDefinitions | Нет |
> | createBillingRoleAssignment | Нет |
> | departments | Нет |
> | enrollmentAccounts | Нет |
> | invoices | Нет |
> | transfers | Нет |
> | transfers / acceptTransfer | Нет |
> | transfers / declineTransfer | Нет |
> | transfers / operationStatus | Нет |
> | transfers / validateTransfer | Нет |
> | validateAddress | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | mapApis | ДА |
> | updateCommunicationPreference | Нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | blockchainMembers | ДА |
> | cordaMembers | ДА |
> | наблюдатели | ДА |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | blueprintAssignments | Нет |
> | blueprintAssignments / assignmentOperations | Нет |
> | blueprintAssignments / operations | Нет |
> | blueprints | Нет |
> | blueprints / artifacts | Нет |
> | blueprints / versions | Нет |
> | blueprints / versions / artifacts | Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | botServices | ДА |
> | botServices / channels | Нет |
> | botServices / connections | Нет |
> | языки | Нет |
> | шаблоны | Нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Redis | ДА |
> | RedisConfigDefinition | Нет |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | appliedReservations | Нет |
> | calculateExchange | Нет |
> | calculatePrice | Нет |
> | calculatePurchasePrice | Нет |
> | catalogs | Нет |
> | commercialReservationOrders | Нет |
> | exchange | Нет |
> | placePurchaseOrder | Нет |
> | reservationOrders | Нет |
> | reservationOrders / calculateRefund | Нет |
> | reservationOrders / merge | Нет |
> | reservationOrders / reservations | Нет |
> | reservationOrders / reservations / revisions | Нет |
> | reservationOrders / return | Нет |
> | reservationOrders / split | Нет |
> | reservationOrders / swap | Нет |
> | reservations | Нет |
> | ресурсов | Нет |
> | validateReservationOrder | Нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Нет |
> | CdnWebApplicationFirewallPolicies | ДА |
> | edgenodes | Нет |
> | профили | ДА |
> | profiles / endpoints | ДА |
> | profiles / endpoints / customdomains | Нет |
> | profiles / endpoints / origins | Нет |
> | validateProbe | Нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | certificateOrders | ДА |
> | certificateOrders / certificates | Нет |
> | validateCertificateRegistrationInformation | Нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capabilities | Нет |
> | domainNames | ДА |
> | domainNames / capabilities | Нет |
> | domainNames / internalLoadBalancers | Нет |
> | domainNames / serviceCertificates | Нет |
> | domainNames / slots | Нет |
> | domainNames / slots / roles | Нет |
> | domainNames / slots / roles / metricDefinitions | Нет |
> | domainNames / slots / roles / metrics | Нет |
> | moveSubscriptionResources | Нет |
> | operatingSystemFamilies | Нет |
> | operatingSystems | Нет |
> | quotas | Нет |
> | resourceTypes | Нет |
> | validateSubscriptionMoveAvailability | Нет |
> | virtualMachines | ДА |
> | virtualMachines / diagnosticSettings | Нет |
> | virtualMachines / metricDefinitions | Нет |
> | virtualMachines / metrics | Нет |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | classicInfrastructureResources | Нет |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capabilities | Нет |
> | expressRouteCrossConnections | Нет |
> | expressRouteCrossConnections / peerings | Нет |
> | gatewaySupportedDevices | Нет |
> | networkSecurityGroups | ДА |
> | quotas | Нет |
> | reservedIps | ДА |
> | virtualNetworks | ДА |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Нет |
> | virtualNetworks / virtualNetworkPeerings | Нет |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capabilities | Нет |
> | disks | Нет |
> | images | Нет |
> | osImages | Нет |
> | osPlatformImages | Нет |
> | publicImages | Нет |
> | quotas | Нет |
> | storageAccounts | ДА |
> | storageAccounts / blobServices | Нет |
> | storageAccounts / fileServices | Нет |
> | storageAccounts / metricDefinitions | Нет |
> | storageAccounts / metrics | Нет |
> | storageAccounts / queueServices | Нет |
> | storageAccounts / services | Нет |
> | storageAccounts / services / diagnosticSettings | Нет |
> | storageAccounts / services / metricDefinitions | Нет |
> | storageAccounts / services / metrics | Нет |
> | storageAccounts / tableServices | Нет |
> | storageAccounts / vmImages | Нет |
> | vmImages | Нет |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | ДА |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | RateCard | Нет |
> | UsageAggregates | Нет |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | availabilitySets | ДА |
> | diskEncryptionSets | ДА |
> | disks | ДА |
> | galleries | ДА |
> | galleries / applications | Нет |
> | galleries / applications / versions | Нет |
> | galleries / images | Нет |
> | galleries / images / versions | Нет |
> | hostGroups | ДА |
> | hostGroups / hosts | ДА |
> | images | ДА |
> | proximityPlacementGroups | ДА |
> | restorePointCollections | ДА |
> | restorePointCollections / restorePoints | Нет |
> | sharedVMImages | ДА |
> | sharedVMImages / versions | Нет |
> | snapshots | ДА |
> | virtualMachines | ДА |
> | virtualMachines / extensions | ДА |
> | virtualMachines / metricDefinitions | Нет |
> | virtualMachineScaleSets | ДА |
> | virtualMachineScaleSets / extensions | Нет |
> | virtualMachineScaleSets / networkInterfaces | Нет |
> | virtualMachineScaleSets / publicIPAddresses | Нет |
> | virtualMachineScaleSets / virtualMachines | Нет |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Нет |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | AggregatedCost | Нет |
> | сведения о балансе. | Нет |
> | Расходы | Нет |
> | Расходы | Нет |
> | CostTags | Нет |
> | credits | Нет |
> | events | Нет |
> | Прогнозы | Нет |
> | lots | Нет |
> | Marketplace | Нет |
> | Pricesheets | Нет |
> | products | Нет |
> | ReservationDetails | Нет |
> | ReservationRecommendations | Нет |
> | ReservationSummaries | Нет |
> | ReservationTransactions | Нет |
> | Теги | Нет |
> | tenants | Нет |
> | Термины | Нет |
> | UsageDetails | Нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | containerGroups | ДА |
> | serviceAssociationLinks | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | registries | ДА |
> | registries / builds | Нет |
> | registries / builds / cancel | Нет |
> | registries / builds / getLogLink | Нет |
> | registries / buildTasks | ДА |
> | registries / buildTasks / steps | Нет |
> | registries / eventGridFilters | Нет |
> | registries / generateCredentials | Нет |
> | registries / getBuildSourceUploadUrl | Нет |
> | registries / GetCredentials | Нет |
> | registries / importImage | Нет |
> | registries / queueBuild | Нет |
> | registries / regenerateCredential | Нет |
> | registries / regenerateCredentials | Нет |
> | registries / replications | ДА |
> | registries / runs | Нет |
> | registries / runs / cancel | Нет |
> | registries / scheduleRun | Нет |
> | registries / scopeMaps | Нет |
> | registries / taskRuns | ДА |
> | registries / tasks | ДА |
> | registries / tokens | Нет |
> | registries / updatePolicies | Нет |
> | registries / webhooks | ДА |
> | registries / webhooks / getCallbackConfig | Нет |
> | registries / webhooks / ping | Нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | containerServices | ДА |
> | managedClusters | ДА |
> | openShiftManagedClusters | ДА |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | ДА |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Оповещения | Нет |
> | BillingAccounts | Нет |
> | Расходы | Нет |
> | CloudConnectors | Нет |
> | Коннекторы | ДА |
> | Departments | Нет |
> | Измерения | Нет |
> | EnrollmentAccounts | Нет |
> | Экспорт | Нет |
> | ExternalBillingAccounts | Нет |
> | ExternalBillingAccounts / Alerts | Нет |
> | ExternalBillingAccounts / Dimensions | Нет |
> | ExternalBillingAccounts / Forecast | Нет |
> | ExternalBillingAccounts / Query | Нет |
> | ExternalSubscriptions | Нет |
> | ExternalSubscriptions / Alerts | Нет |
> | ExternalSubscriptions / Dimensions | Нет |
> | ExternalSubscriptions / Forecast | Нет |
> | ExternalSubscriptions / Query | Нет |
> | Forecast | Нет |
> | Запрос | Нет |
> | регистрация | Нет |
> | Reportconfigs | Нет |
> | Отчеты | Нет |
> | Настройки | Нет |
> | showbackRules | Нет |
> | Представления | Нет |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | requests | Нет |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | associations | Нет |
> | resourceProviders | ДА |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | jobs | ДА |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | ДА |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | workspaces | ДА |
> | workspaces / virtualNetworkPeerings | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | catalogs | ДА |
> | datacatalogs | ДА |
> | datacatalogs / datasources | Нет |
> | datacatalogs / datasources / scans | Нет |
> | datacatalogs / datasources / scans / datasets | Нет |
> | datacatalogs / datasources / scans / triggers | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | dataFactories | ДА |
> | dataFactories / diagnosticSettings | Нет |
> | dataFactories / metricDefinitions | Нет |
> | dataFactorySchema | Нет |
> | factories | ДА |
> | factories / integrationRuntimes | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | ДА |
> | accounts / dataLakeStoreAccounts | Нет |
> | accounts / storageAccounts | Нет |
> | accounts / storageAccounts / containers | Нет |
> | accounts / transferAnalyticsUnits | Нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | ДА |
> | accounts / eventGridFilters | Нет |
> | accounts / firewallRules | Нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | services; | ДА |
> | services / projects | ДА |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | ДА |
> | accounts / shares | Нет |
> | accounts / shares / datasets | Нет |
> | accounts / shares / invitations | Нет |
> | accounts / shares / providersharesubscriptions | Нет |
> | accounts / shares / synchronizationSettings | Нет |
> | accounts / sharesubscriptions | Нет |
> | accounts / sharesubscriptions / consumerSourceDataSets | Нет |
> | accounts / sharesubscriptions / datasetmappings | Нет |
> | accounts / sharesubscriptions / triggers | Нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | серверы | ДА |
> | servers / advisors | Нет |
> | servers / privateEndpointConnectionProxies | Нет |
> | servers / privateEndpointConnections | Нет |
> | servers / privateLinkResources | Нет |
> | servers / queryTexts | Нет |
> | servers / recoverableServers | Нет |
> | servers / topQueryStatistics | Нет |
> | servers / virtualNetworkRules | Нет |
> | servers / waitStatistics | Нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | серверы | ДА |
> | servers / advisors | Нет |
> | servers / privateEndpointConnectionProxies | Нет |
> | servers / privateEndpointConnections | Нет |
> | servers / privateLinkResources | Нет |
> | servers / queryTexts | Нет |
> | servers / recoverableServers | Нет |
> | servers / topQueryStatistics | Нет |
> | servers / virtualNetworkRules | Нет |
> | servers / waitStatistics | Нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | serverGroups | ДА |
> | серверы | ДА |
> | servers / advisors | Нет |
> | servers / keys | Нет |
> | servers / privateEndpointConnectionProxies | Нет |
> | servers / privateEndpointConnections | Нет |
> | servers / privateLinkResources | Нет |
> | servers / queryTexts | Нет |
> | servers / recoverableServers | Нет |
> | servers / topQueryStatistics | Нет |
> | servers / virtualNetworkRules | Нет |
> | servers / waitStatistics | Нет |
> | serversv2 | ДА |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | artifactSources | ДА |
> | rollouts | ДА |
> | serviceTopologies | ДА |
> | serviceTopologies / services | ДА |
> | serviceTopologies / services / serviceUnits | ДА |
> | steps | ДА |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | applicationgroups | ДА |
> | applicationgroups / applications | Нет |
> | applicationgroups / desktops | Нет |
> | applicationgroups / startmenuitems | Нет |
> | hostpools | ДА |
> | hostpools / sessionhosts | Нет |
> | hostpools / sessionhosts / usersessions | Нет |
> | hostpools / usersessions | Нет |
> | workspaces | ДА |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | ElasticPools | ДА |
> | ElasticPools / IotHubTenants | ДА |
> | IotHubs | ДА |
> | IotHubs / eventGridFilters | Нет |
> | ProvisioningServices | ДА |
> | usages | Нет |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | pipelines | ДА |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | controllers | ДА |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | labcenters | ДА |
> | labs | ДА |
> | labs / environments | ДА |
> | labs / serviceRunners | ДА |
> | labs / virtualMachines | ДА |
> | schedules | ДА |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | databaseAccountNames | Нет |
> | databaseAccounts | ДА |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | domains | ДА |
> | domains / domainOwnershipIdentifiers | Нет |
> | generateSsoRequest | Нет |
> | topLevelDomains | Нет |
> | validateDomainRegistrationInformation | Нет |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | lcsprojects | Нет |
> | lcsprojects / clouddeployments | Нет |
> | lcsprojects / connectors | Нет |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | services; | ДА |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | domains | ДА |
> | domains / topics | Нет |
> | eventSubscriptions | Нет |
> | extensionTopics | Нет |
> | topics | ДА |
> | topicTypes | Нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | ДА |
> | namespaces | ДА |
> | namespaces / authorizationrules | Нет |
> | namespaces / disasterrecoveryconfigs | Нет |
> | namespaces / eventhubs | Нет |
> | namespaces / eventhubs / authorizationrules | Нет |
> | namespaces / eventhubs / consumergroups | Нет |
> | namespaces / networkrulesets | Нет |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | эластичной базы данных | Нет |
> | providers | Нет |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | enroll | Нет |
> | galleryitems | Нет |
> | generateartifactaccessuri | Нет |
> | myareas | Нет |
> | myareas / areas | Нет |
> | myareas / areas / areas | Нет |
> | myareas / areas / areas / galleryitems | Нет |
> | myareas / areas / galleryitems | Нет |
> | myareas / galleryitems | Нет |
> | регистрация | Нет |
> | ресурсов | Нет |
> | retrieveresourcesbyid | Нет |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | ДА |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | configurationProfileAssignments | Нет |
> | guestConfigurationAssignments | Нет |
> | software | Нет |
> | softwareUpdateProfile | Нет |
> | softwareUpdates | Нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | hanaInstances | ДА |
> | sapMonitors | ДА |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | dedicatedHSMs | ДА |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | ДА |
> | clusters / applications | Нет |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | services; | ДА |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | machines | ДА |
> | machines / extensions | ДА |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | dataManagers | ДА |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | components | ДА |
> | networkScopes | ДА |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | jobs | ДА |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | diagnosticSettings | Нет |
> | diagnosticSettingsCategories | Нет |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | appTemplates | Нет |
> | IoTApps | ДА |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Граф | ДА |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | deletedVaults | Нет |
> | hsmPools | ДА |
> | vaults | ДА |
> | vaults / accessPolicies | Нет |
> | vaults / eventGridFilters | Нет |
> | vaults / secrets | Нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | ДА |
> | clusters / attacheddatabaseconfigurations | Нет |
> | clusters / databases | Нет |
> | clusters / databases / dataconnections | Нет |
> | clusters / databases / eventhubconnections | Нет |
> | clusters / sharedidentities | Нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | labaccounts | ДА |
> | пользователей | Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | hostingEnvironments | ДА |
> | integrationAccounts | ДА |
> | integrationServiceEnvironments | ДА |
> | integrationServiceEnvironments / managedApis | ДА |
> | isolatedEnvironments | ДА |
> | workflows | ДА |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | commitmentPlans | ДА |
> | webServices | ДА |
> | Рабочие области | ДА |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | workspaces | ДА |
> | workspaces / computes | Нет |
> | workspaces / eventGridFilters | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Identities | Нет |
> | userAssignedIdentities | ДА |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Нет |
> | registrationAssignments | Нет |
> | registrationDefinitions | Нет |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | getEntities | Нет |
> | managementGroups | Нет |
> | ресурсов | Нет |
> | startTenantBackfill | Нет |
> | tenantBackfillStatus | Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | ДА |
> | accounts / eventGridFilters | Нет |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | offers | Нет |
> | offerTypes | Нет |
> | offerTypes / publishers | Нет |
> | offerTypes / publishers / offers | Нет |
> | offerTypes / publishers / offers / plans | Нет |
> | offerTypes / publishers / offers / plans / agreements | Нет |
> | offerTypes / publishers / offers / plans / configs | Нет |
> | offerTypes / publishers / offers / plans / configs / importImage | Нет |
> | privategalleryitems | Нет |
> | products | Нет |
> | publishers | Нет |
> | publishers / offers | Нет |
> | publishers / offers / amendments | Нет |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | classicDevServices | ДА |
> | updateCommunicationPreference | Нет |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | agreements | Нет |
> | offertypes | Нет |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | mediaservices | ДА |
> | mediaservices / accountFilters | Нет |
> | mediaservices / assets | Нет |
> | mediaservices / assets / assetFilters | Нет |
> | mediaservices / contentKeyPolicies | Нет |
> | mediaservices / eventGridFilters | Нет |
> | mediaservices / liveEventOperations | Нет |
> | mediaservices / liveEvents | ДА |
> | mediaservices / liveEvents / liveOutputs | Нет |
> | mediaservices / liveOutputOperations | Нет |
> | mediaservices / mediaGraphs | Нет |
> | mediaservices / streamingEndpointOperations | Нет |
> | mediaservices / streamingEndpoints | ДА |
> | mediaservices / streamingLocators | Нет |
> | mediaservices / streamingPolicies | Нет |
> | mediaservices / transforms | Нет |
> | mediaservices / transforms / jobs | Нет |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | appClusters | ДА |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | assessmentProjects | ДА |
> | migrateprojects | ДА |
> | projects | ДА |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | ДА |
> | objectUnderstandingAccounts | ДА |
> | remoteRenderingAccounts | ДА |
> | spatialAnchorsAccounts | ДА |
> | surfaceReconstructionAccounts | ДА |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | netAppAccounts | ДА |
> | netAppAccounts / capacityPools | ДА |
> | netAppAccounts / capacityPools / volumes | ДА |
> | netAppAccounts / capacityPools / volumes / mountTargets | ДА |
> | netAppAccounts / capacityPools / volumes / snapshots | ДА |
## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | applicationGateways | ДА |
> | applicationGatewayWebApplicationFirewallPolicies | ДА |
> | applicationSecurityGroups | ДА |
> | azureFirewallFqdnTags | Нет |
> | azureFirewalls | ДА |
> | bastionHosts | ДА |
> | bgpServiceCommunities | Нет |
> | connections | ДА |
> | ddosCustomPolicies | ДА |
> | ddosProtectionPlans | ДА |
> | dnsOperationStatuses | Нет |
> | dnszones | ДА |
> | dnszones / A | Нет |
> | dnszones / AAAA | Нет |
> | dnszones / all | Нет |
> | dnszones / CAA | Нет |
> | dnszones / CNAME | Нет |
> | dnszones / MX | Нет |
> | dnszones / NS | Нет |
> | dnszones / PTR | Нет |
> | dnszones / recordsets | Нет |
> | dnszones / SOA | Нет |
> | dnszones / SRV | Нет |
> | dnszones / TXT | Нет |
> | expressRouteCircuits | ДА |
> | expressRouteCrossConnections | ДА |
> | expressRouteGateways | ДА |
> | expressRoutePorts | ДА |
> | expressRouteServiceProviders | Нет |
> | firewallPolicies | ДА |
> | frontdoors | ДА |
> | frontdoorWebApplicationFirewallManagedRuleSets | Нет |
> | frontdoorWebApplicationFirewallPolicies | ДА |
> | getDnsResourceReference | Нет |
> | internalNotify | Нет |
> | loadBalancers | ДА |
> | localNetworkGateways | ДА |
> | natGateways | ДА |
> | networkIntentPolicies | ДА |
> | networkInterfaces | ДА |
> | networkProfiles | ДА |
> | networkSecurityGroups | ДА |
> | networkWatchers | ДА |
> | networkWatchers / connectionMonitors | ДА |
> | networkWatchers / lenses | ДА |
> | networkWatchers / pingMeshes | ДА |
> | p2sVpnGateways | ДА |
> | privateDnsOperationStatuses | Нет |
> | privateDnsZones | ДА |
> | privateDnsZones / A | Нет |
> | privateDnsZones / AAAA | Нет |
> | privateDnsZones / all | Нет |
> | privateDnsZones / CNAME | Нет |
> | privateDnsZones / MX | Нет |
> | privateDnsZones / PTR | Нет |
> | privateDnsZones / SOA | Нет |
> | privateDnsZones / SRV | Нет |
> | privateDnsZones / TXT | Нет |
> | privateDnsZones / virtualNetworkLinks | ДА |
> | privateEndpoints | ДА |
> | privateLinkServices | ДА |
> | publicIPAddresses | ДА |
> | publicIPPrefixes | ДА |
> | routeFilters | ДА |
> | routeTables | ДА |
> | serviceEndpointPolicies | ДА |
> | trafficManagerGeographicHierarchies | Нет |
> | trafficmanagerprofiles | ДА |
> | trafficmanagerprofiles / heatMaps | Нет |
> | trafficManagerUserMetricsKeys | Нет |
> | virtualHubs | ДА |
> | virtualNetworkGateways | ДА |
> | virtualNetworks | ДА |
> | virtualNetworkTaps | ДА |
> | virtualWans | ДА |
> | vpnGateways | ДА |
> | vpnSites | ДА |
> | webApplicationFirewallPolicies | ДА |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | namespaces | ДА |
> | namespaces / notificationHubs | ДА |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | osNamespaces | ДА |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | HyperVSites | ДА |
> | ImportSites | ДА |
> | ServerSites | ДА |
> | VMwareSites | ДА |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | ДА |
> | устройства | Нет |
> | linkTargets | Нет |
> | storageInsightConfigs | Нет |
> | workspaces | ДА |
> | workspaces / dataSources | Нет |
> | workspaces / linkedServices | Нет |
> | workspaces / query | Нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managementassociations | Нет |
> | managementconfigurations | ДА |
> | solutions | ДА |
> | узел "Представления" | ДА |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | legacyPeerings | Нет |
> | peerAsns | Нет |
> | peerings | ДА |
> | peeringServiceProviders | Нет |
> | peeringServices | ДА |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | policyEvents | Нет |
> | policyMetadata | Нет |
> | policyStates | Нет |
> | policyTrackedResources | Нет |
> | remediations | Нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | consoles | Нет |
> | dashboards | ДА |
> | userSettings | Нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | workspaceCollections | ДА |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capacities | ДА |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | backupProtectedItems | Нет |
> | vaults | ДА |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | namespaces | ДА |
> | namespaces / authorizationrules | Нет |
> | namespaces / hybridconnections | Нет |
> | namespaces / hybridconnections / authorizationrules | Нет |
> | namespaces / wcfrelays | Нет |
> | namespaces / wcfrelays / authorizationrules | Нет |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Нет |
> | collections | ДА |
> | collections / applications | Нет |
> | collections / securityprincipals | Нет |
> | templateImages | Нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | запросы | ДА |
> | resourceChangeDetails | Нет |
> | resourceChanges | Нет |
> | ресурсов | Нет |
> | resourcesHistory | Нет |
> | subscriptionsStatus | Нет |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | availabilityStatuses | Нет |
> | childAvailabilityStatuses | Нет |
> | childResources | Нет |
> | events | Нет |
> | impactedResources | Нет |
> | metadata | Нет |
> | Уведомления | Нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | deployments | Нет |
> | deployments / operations | Нет |
> | deploymentScripts | ДА |
> | deploymentScripts / logs | Нет |
> | links | Нет |
> | notifyResourceJobs | Нет |
> | providers | Нет |
> | resourceGroups | Нет |
> | ресурсов | Нет |
> | subscriptions | Нет |
> | subscriptions / providers | Нет |
> | subscriptions / resources | Нет |
> | subscriptions / tagnames | Нет |
> | subscriptions / tagNames / tagValues | Нет |
> | tenants | Нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | приложений | ДА |
> | saasresources | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | jobcollections | ДА |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | resourceHealthMetadata | Нет |
> | searchServices | ДА |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Нет |
> | advancedThreatProtectionSettings | Нет |
> | оповещения | Нет |
> | allowedConnections | Нет |
> | applicationWhitelistings | Нет |
> | assessmentMetadata | Нет |
> | assessments | Нет |
> | autoDismissAlertsRules | Нет |
> | automations | ДА |
> | AutoProvisioningSettings | Нет |
> | Compliances | Нет |
> | dataCollectionAgents | Нет |
> | deviceSecurityGroups | Нет |
> | discoveredSecuritySolutions | Нет |
> | externalSecuritySolutions | Нет |
> | InformationProtectionPolicies | Нет |
> | iotSecuritySolutions | ДА |
> | iotSecuritySolutions / analyticsModels | Нет |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Нет |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Нет |
> | jitNetworkAccessPolicies | Нет |
> | networkData | Нет |
> | политики | Нет |
> | pricings | Нет |
> | regulatoryComplianceStandards | Нет |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Нет |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Нет |
> | securityContacts | Нет |
> | securitySolutions | Нет |
> | securitySolutionsReferenceData | Нет |
> | securityStatuses | Нет |
> | securityStatusesSummaries | Нет |
> | serverVulnerabilityAssessments | Нет |
> | Параметры | Нет |
> | subAssessments | Нет |
> | задачи; | Нет |
> | topologies | Нет |
> | workspaceSettings | Нет |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | diagnosticSettings | Нет |
> | diagnosticSettingsCategories | Нет |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | aggregations | Нет |
> | alertRules | Нет |
> | alertRuleTemplates | Нет |
> | bookmarks | Нет |
> | cases | Нет |
> | dataConnectors | Нет |
> | Сущности | Нет |
> | entityQueries | Нет |
> | officeConsents | Нет |
> | Параметры | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | namespaces | ДА |
> | namespaces / authorizationrules | Нет |
> | namespaces / disasterrecoveryconfigs | Нет |
> | namespaces / eventgridfilters | Нет |
> | namespaces / networkrulesets | Нет |
> | namespaces / queues | Нет |
> | namespaces / queues / authorizationrules | Нет |
> | namespaces / topics | Нет |
> | namespaces / topics / authorizationrules | Нет |
> | namespaces / topics / subscriptions | Нет |
> | namespaces / topics / subscriptions / rules | Нет |
> | premiumMessagingRegions | Нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | приложений | ДА |
> | clusters | ДА |
> | clusters / applications | Нет |
> | containerGroups | ДА |
> | containerGroupSets | ДА |
> | edgeclusters | ДА |
> | edgeclusters / applications | Нет |
> | networks | ДА |
> | secretstores | ДА |
> | secretstores / certificates | Нет |
> | secretstores / secrets | Нет |
> | volumes. | ДА |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | приложений | ДА |
> | containerGroups | ДА |
> | gateways | ДА |
> | networks | ДА |
> | секретные коды | ДА |
> | volumes. | ДА |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | providerRegistrations | Нет |
> | providerRegistrations / resourceTypeRegistrations | Нет |
> | rollouts | ДА |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SignalR | ДА |
> | SignalR / eventGridFilters | Нет |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SiteRecoveryVault | ДА |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | hybridUseBenefits | Нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | applicationDefinitions | ДА |
> | приложений | ДА |
> | jitRequests | ДА |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managedInstances | ДА |
> | managedInstances / databases | ДА |
> | managedInstances / databases / backupShortTermRetentionPolicies | Нет |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Нет |
> | managedInstances / databases / vulnerabilityAssessments | Нет |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Нет |
> | managedInstances / encryptionProtector | Нет |
> | managedInstances / keys | Нет |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Нет |
> | managedInstances / vulnerabilityAssessments | Нет |
> | серверы | ДА |
> | servers / administrators | Нет |
> | servers / communicationLinks | Нет |
> | servers / databases | ДА |
> | servers / encryptionProtector | Нет |
> | servers / firewallRules | Нет |
> | servers / keys | Нет |
> | servers / restorableDroppedDatabases | Нет |
> | servers / serviceobjectives | Нет |
> | servers / tdeCertificates | Нет |
> | virtualClusters | Нет |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | ДА |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Нет |
> | SqlVirtualMachines | ДА |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageAccounts | ДА |
> | storageAccounts / blobServices | Нет |
> | storageAccounts / fileServices | Нет |
> | storageAccounts / queueServices | Нет |
> | storageAccounts / services | Нет |
> | storageAccounts / services / metricDefinitions | Нет |
> | storageAccounts / tableServices | Нет |
> | usages | Нет |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | caches | ДА |
> | caches / storageTargets | Нет |
> | usageModels | Нет |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | replicationGroups | Нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | ДА |
> | storageSyncServices / registeredServers | Нет |
> | storageSyncServices / syncGroups | Нет |
> | storageSyncServices / syncGroups / cloudEndpoints | Нет |
> | storageSyncServices / syncGroups / serverEndpoints | Нет |
> | storageSyncServices / workflows | Нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | ДА |
> | storageSyncServices / registeredServers | Нет |
> | storageSyncServices / syncGroups | Нет |
> | storageSyncServices / syncGroups / cloudEndpoints | Нет |
> | storageSyncServices / syncGroups / serverEndpoints | Нет |
> | storageSyncServices / workflows | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | ДА |
> | storageSyncServices / registeredServers | Нет |
> | storageSyncServices / syncGroups | Нет |
> | storageSyncServices / syncGroups / cloudEndpoints | Нет |
> | storageSyncServices / syncGroups / serverEndpoints | Нет |
> | storageSyncServices / workflows | Нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managers | ДА |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | streamingjobs | ДА |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | cancel | Нет |
> | CreateSubscription | Нет |
> | enable | Нет |
> | rename | Нет |
> | SubscriptionDefinitions | Нет |
> | SubscriptionOperations | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | environments | ДА |
> | environments / accessPolicies | Нет |
> | environments / eventsources | ДА |
> | environments / referenceDataSets | ДА |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | dedicatedCloudNodes | ДА |
> | dedicatedCloudServices | ДА |
> | virtualMachines | ДА |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | apiManagementAccounts | Нет |
> | apiManagementAccounts / apiAcls | Нет |
> | apiManagementAccounts / apis | Нет |
> | apiManagementAccounts / apis / apiAcls | Нет |
> | apiManagementAccounts / apis / connectionAcls | Нет |
> | apiManagementAccounts / apis / connections | Нет |
> | apiManagementAccounts / apis / connections / connectionAcls | Нет |
> | apiManagementAccounts / apis / localizedDefinitions | Нет |
> | apiManagementAccounts / connectionAcls | Нет |
> | apiManagementAccounts / connections | Нет |
> | billingMeters | Нет |
> | certificates | ДА |
> | connectionGateways | ДА |
> | connections | ДА |
> | customApis | ДА |
> | deletedSites | Нет |
> | функции | Нет |
> | hostingEnvironments | ДА |
> | hostingEnvironments / multiRolePools | Нет |
> | hostingEnvironments / workerPools | Нет |
> | publishingUsers | Нет |
> | к просмотру фильмов | Нет |
> | resourceHealthMetadata | Нет |
> | runtimes | Нет |
> | serverFarms | ДА |
> | serverFarms / eventGridFilters | Нет |
> | sites | ДА |
> | sites/config  | Нет |
> | sites / eventGridFilters | Нет |
> | sites / hostNameBindings | Нет |
> | sites / networkConfig | Нет |
> | sites / premieraddons | ДА |
> | sites / slots | ДА |
> | sites / slots / eventGridFilters | Нет |
> | sites / slots / hostNameBindings | Нет |
> | sites / slots / networkConfig | Нет |
> | sourceControls | Нет |
> | validate | Нет |
> | verifyHostingEnvironmentVnet | Нет |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | diagnosticSettings | Нет |
> | diagnosticSettingsCategories | Нет |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | DeviceServices | ДА |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | components | Нет |
> | componentsSummary | Нет |
> | monitorInstances | Нет |
> | monitorInstancesSummary | Нет |
> | monitors | Нет |
> | notificationSettings | Нет |

## <a name="next-steps"></a>Дальнейшие действия

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
