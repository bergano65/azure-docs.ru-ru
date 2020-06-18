---
title: Поддержка операции перемещения типом ресурсов
description: Список типов ресурсов Azure, подлежащих перемещению в новую группу ресурсов или подписку.
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 4f2ed7f1cb24b9896b533fb5d18ac4e57db48e2c
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780326"
---
# <a name="move-operation-support-for-resources"></a>Поддержка операции перемещения для ресурсов

В этой статье указано, поддерживается ли для тех или иных типов ресурсов Azure операция перемещения. В нем также содержатся сведения о специальных условиях, которые следует учитывать при перемещении ресурса.

Переход к пространству имен поставщика ресурсов:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.Cognition](#microsoftcognition)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CostManagementExports](#microsoftcostmanagementexports)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
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
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
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
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
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
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | domainservices | нет | нет |
> | domainservices / oucontainer | нет | нет |
> | Расположения | нет | нет |
> | locations / operationresults | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | privatelinkforazuread | нет | нет |
> | tenants | нет | нет |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | operationresults | нет | нет |
> | Операции | нет | нет |
> | supportproviders | нет | нет |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | нет | нет |
> | addsservices | нет | нет |
> | agents | нет | нет |
> | anonymousapiusers | нет | нет |
> | настройка | нет | нет |
> | журналы | нет | нет |
> | Операции | нет | нет |
> | reports | нет | нет |
> | servicehealthmetrics | нет | нет |
> | services; | нет | нет |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | конфигурации | нет | нет |
> | generaterecommendations | нет | нет |
> | метаданные | нет | нет |
> | Операции | нет | нет |
> | к просмотру фильмов | нет | нет |
> | suppressions | нет | нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | actionrules | Да | Да |
> | оповещения | нет | нет |
> | alertslist | нет | нет |
> | alertsmetadata | нет | нет |
> | alertssummary | нет | нет |
> | alertssummarylist | нет | нет |
> | Операции | нет | нет |
> | smartdetectoralertrules | Да | Да |
> | smartgroups | нет | нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |
> | servers | Да | Да |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | нет | нет |
> | checknameavailability | нет | нет |
> | checkservicenameavailability | нет | нет |
> | Операции | нет | нет |
> | reportfeedback | нет | нет |
> | служба | Да | Да |
> | validateservicename | нет | нет |

> [!IMPORTANT]
> Невозможно переместить службу управления API, для которой настроено значение "SKU потребления".

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | configurationstores | Да | Да |
> | configurationstores / eventgridfilters | нет | нет |
> | Расположения | нет | нет |
> | locations / operationsstatus | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operationstatus | нет | нет |
> | Операции | нет | нет |
> | spring | Да | Да |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | apiapps | нет | нет |
> | appidentities | нет | нет |
> | gateways | нет | нет |

> [!IMPORTANT]
> См. статью [Руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Да | Да |
> | Операции | нет | нет |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkaccess | нет | нет |
> | classicadministrators | нет | нет |
> | dataaliases | нет | нет |
> | denyassignments | нет | нет |
> | elevateaccess | нет | нет |
> | findorphanroleassignments | нет | нет |
> | locks | нет | нет |
> | Операции | нет | нет |
> | разрешения | нет | нет |
> | policyassignments | нет | нет |
> | policydefinitions | нет | нет |
> | policysetdefinitions | нет | нет |
> | provideroperations | нет | нет |
> | roleassignments | нет | нет |
> | roleassignmentsusagemetrics | нет | нет |
> | roledefinitions | нет | нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Да | Да |
> | automationaccounts / configurations | Да | Да |
> | automationaccounts / jobs | нет | нет |
> | automationaccounts / privateendpointconnectionproxies | нет | нет |
> | automationaccounts / privateendpointconnections | нет | нет |
> | automationaccounts / privatelinkresources | нет | нет |
> | automationaccounts / runbooks | Да | Да |
> | automationaccounts / softwareupdateconfigurations | нет | нет |
> | automationaccounts / webhooks | нет | нет |
> | Операции | нет | нет |

> [!IMPORTANT]
> Модули Runbook должны находиться в той же группе ресурсов, что и учетная запись службы автоматизации.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / checkquotaavailability | нет | нет |
> | Операции | нет | нет |
> | privateclouds | Да | Да |
> | privateclouds / clusters | нет | нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Да | Да |
> | b2ctenants | нет | нет |
> | checknameavailability | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | datacontrollers | нет | нет |
> | hybriddatamanagers | нет | нет |
> | Операции | нет | нет |
> | postgresinstances | нет | нет |
> | sqlinstances | нет | нет |
> | sqlmanagedinstances | нет | нет |
> | sqlserverinstances | нет | нет |
> | sqlserverregistrations | Да | Да |
> | sqlserverregistrations / sqlservers | нет | нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | нет | нет |
> | Операции | нет | нет |
> | registrations | Да | Да |
> | registrations / customersubscriptions | нет | нет |
> | registrations / products | нет | нет |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | clusters | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Да | Да |
> | Расположения | нет | нет |
> | locations / accountoperationresults | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / quotas | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | clusters | нет | нет |
> | fileservers | нет | нет |
> | jobs | нет | нет |
> | Операции | нет | нет |
> | workspaces | нет | нет |
> | workspaces / clusters | нет | нет |
> | workspaces / experiments | нет | нет |
> | workspaces / experiments / jobs | нет | нет |
> | workspaces / fileservers | нет | нет |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | billingaccounts | нет | нет |
> | billingaccounts / agreements | нет | нет |
> | billingaccounts / billingpermissions | нет | нет |
> | billingaccounts / billingprofiles | нет | нет |
> | billingaccounts / billingprofiles / availablebalance | нет | нет |
> | billingaccounts / billingprofiles / billingpermissions | нет | нет |
> | billingaccounts / billingprofiles / billingroleassignments | нет | нет |
> | billingaccounts / billingprofiles / billingroledefinitions | нет | нет |
> | billingaccounts / billingprofiles / billingsubscriptions | нет | нет |
> | billingaccounts / billingprofiles / createbillingroleassignment | нет | нет |
> | billingaccounts / billingprofiles / customers | нет | нет |
> | billingaccounts / billingprofiles / instructions | нет | нет |
> | billingaccounts / billingprofiles / invoices | нет | нет |
> | billingaccounts / billingprofiles / invoices / pricesheet | нет | нет |
> | billingaccounts / billingprofiles / invoices / transactions | нет | нет |
> | billingaccounts / billingprofiles / invoicesections | нет | нет |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | нет | нет |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | нет | нет |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | нет | нет |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | нет | нет |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | нет | нет |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | нет | нет |
> | billingaccounts / billingprofiles / invoicesections / products | нет | нет |
> | billingaccounts / billingprofiles / invoicesections / products / transfer | нет | нет |
> | billingaccounts / billingprofiles / invoicesections / products / updateautorenew | нет | нет |
> | billingaccounts / billingprofiles / invoicesections / transactions | нет | нет |
> | billingaccounts / billingprofiles / invoicesections / transfers | нет | нет |
> | billingaccounts / billingprofiles / patchoperations | нет | нет |
> | billingaccounts / billingprofiles / paymentmethods | нет | нет |
> | billingaccounts / billingprofiles / policies | нет | нет |
> | billingaccounts / billingprofiles / pricesheet | нет | нет |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | нет | нет |
> | billingaccounts / billingprofiles / products | нет | нет |
> | billingaccounts / billingprofiles / transactions | нет | нет |
> | billingaccounts / billingroleassignments | нет | нет |
> | billingaccounts / billingroledefinitions | нет | нет |
> | billingaccounts / billingsubscriptions | нет | нет |
> | billingaccounts / billingsubscriptions / invoices | нет | нет |
> | billingaccounts / createbillingroleassignment | нет | нет |
> | billingaccounts / createinvoicesectionoperations | нет | нет |
> | billingaccounts / customers | нет | нет |
> | billingaccounts / customers / billingpermissions | нет | нет |
> | billingaccounts / customers / billingsubscriptions | нет | нет |
> | billingaccounts / customers / initiatetransfer | нет | нет |
> | billingaccounts / customers / policies | нет | нет |
> | billingaccounts / customers / products | нет | нет |
> | billingaccounts / customers / transactions | нет | нет |
> | billingaccounts / customers / transfers | нет | нет |
> | billingaccounts / departments | нет | нет |
> | billingaccounts / enrollmentaccounts | нет | нет |
> | billingaccounts / invoices | нет | нет |
> | billingaccounts / invoicesections | нет | нет |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | нет | нет |
> | billingaccounts / invoicesections / billingsubscriptions | нет | нет |
> | billingaccounts / invoicesections / billingsubscriptions / transfer | нет | нет |
> | billingaccounts / invoicesections / elevate | нет | нет |
> | billingaccounts / invoicesections / initiatetransfer | нет | нет |
> | billingaccounts / invoicesections / patchoperations | нет | нет |
> | billingaccounts / invoicesections / productmoveoperations | нет | нет |
> | billingaccounts / invoicesections / products | нет | нет |
> | billingaccounts / invoicesections / products / transfer | нет | нет |
> | billingaccounts / invoicesections / products / updateautorenew | нет | нет |
> | billingaccounts / invoicesections / producttransfersresults | нет | нет |
> | billingaccounts / invoicesections / transactions | нет | нет |
> | billingaccounts / invoicesections / transfers | нет | нет |
> | billingaccounts / lineofcredit | нет | нет |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | нет | нет |
> | billingaccounts / operationresults | нет | нет |
> | billingaccounts / patchoperations | нет | нет |
> | billingaccounts / paymentmethods | нет | нет |
> | billingaccounts / products | нет | нет |
> | billingaccounts / transactions | нет | нет |
> | billingperiods | нет | нет |
> | billingpermissions | нет | нет |
> | billingproperty | нет | нет |
> | billingroleassignments | нет | нет |
> | billingroledefinitions | нет | нет |
> | createbillingroleassignment | нет | нет |
> | departments | нет | нет |
> | enrollmentaccounts | нет | нет |
> | invoices | нет | нет |
> | operationresults | нет | нет |
> | Операции | нет | нет |
> | operationstatus | нет | нет |
> | transfers | нет | нет |
> | transfers / accepttransfer | нет | нет |
> | transfers / declinetransfer | нет | нет |
> | transfers / operationstatus | нет | нет |
> | transfers / validatetransfer | нет | нет |
> | validateaddress | нет | нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | нет | нет |
> | mapapis | нет | нет |
> | Операции | нет | нет |
> | updatecommunicationpreference | нет | нет |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | biztalk | нет | нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | нет | нет |
> | cordamembers | нет | нет |
> | Расположения | нет | нет |
> | locations / blockchainmemberoperationresults | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / listconsortiums | нет | нет |
> | locations / watcheroperationresults | нет | нет |
> | Операции | нет | нет |
> | watchers | нет | нет |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | нет | нет |
> | tokenservices | нет | нет |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | нет | нет |
> | blueprintassignments / assignmentoperations | нет | нет |
> | blueprintassignments / operations | нет | нет |
> | blueprints | нет | нет |
> | blueprints / artifacts | нет | нет |
> | blueprints / versions | нет | нет |
> | blueprints / versions / artifacts | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | botservices | Да | Да |
> | botservices / channels | нет | нет |
> | botservices / connections | нет | нет |
> | checknameavailability | нет | нет |
> | listauthserviceproviders | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | Расположения | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operationsstatus | нет | нет |
> | Операции | нет | нет |
> | redis | Да | Да |
> | redis / privateendpointconnections | нет | нет |
> | redis / privatelinkresources | нет | нет |
> | redisenterprise | нет | нет |

> [!IMPORTANT]
> Если экземпляр кэша Azure для Redis настроен с помощью виртуальной сети, его нельзя переместить в другую подписку. См. [ограничения перемещения сети](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | appliedreservations | нет | нет |
> | calculateexchange | нет | нет |
> | calculateprice | нет | нет |
> | calculatepurchaseprice | нет | нет |
> | catalogs | нет | нет |
> | checkoffers | нет | нет |
> | checkpurchasestatus | нет | нет |
> | checkscopes | нет | нет |
> | commercialreservationorders | нет | нет |
> | обмен валюты | нет | нет |
> | listbenefits | нет | нет |
> | Операции | нет | нет |
> | placepurchaseorder | нет | нет |
> | reservationorders | нет | нет |
> | reservationorders / availablescopes | нет | нет |
> | reservationorders / calculaterefund | нет | нет |
> | reservationorders / merge | нет | нет |
> | reservationorders / reservations | нет | нет |
> | reservationorders / reservations / availablescopes | нет | нет |
> | reservationorders / reservations / revisions | нет | нет |
> | reservationorders / return | нет | нет |
> | reservationorders / split | нет | нет |
> | reservationorders / swap | нет | нет |
> | reservations | нет | нет |
> | ресурсов | нет | нет |
> | validatereservationorder | нет | нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | нет | нет |
> | cdnwebapplicationfirewallpolicies | Да | Да |
> | checknameavailability | нет | нет |
> | checkresourceusage | нет | нет |
> | edgenodes | нет | нет |
> | operationresults | нет | нет |
> | operationresults / profileresults | нет | нет |
> | operationresults / profileresults / endpointresults | нет | нет |
> | operationresults / profileresults / endpointresults / customdomainresults | нет | нет |
> | operationresults / profileresults / endpointresults / origingroupresults | нет | нет |
> | operationresults / profileresults / endpointresults / originresults | нет | нет |
> | Операции | нет | нет |
> | профили | Да | Да |
> | profiles / endpoints | Да | Да |
> | profiles / endpoints / customdomains | нет | нет |
> | profiles / endpoints / origingroups | нет | нет |
> | profiles / endpoints / origins | нет | нет |
> | validateprobe | нет | нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | certificateorders | Да | Да |
> | certificateorders / certificates | нет | нет |
> | Операции | нет | нет |
> | validatecertificateregistrationinformation | нет | нет |

> [!IMPORTANT]
> См. статью [Руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | нет | нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | capabilities | нет | нет |
> | checkdomainnameavailability | нет | нет |
> | domainnames | Да | нет |
> | domainnames / capabilities | нет | нет |
> | domainnames / internalloadbalancers | нет | нет |
> | domainnames / servicecertificates | нет | нет |
> | domainnames / slots | нет | нет |
> | domainnames / slots / roles | нет | нет |
> | domainnames / slots / roles / metricdefinitions | нет | нет |
> | domainnames / slots / roles / metrics | нет | нет |
> | movesubscriptionresources | нет | нет |
> | operatingsystemfamilies | нет | нет |
> | operatingsystems | нет | нет |
> | Операции | нет | нет |
> | operationstatuses | нет | нет |
> | quotas | нет | нет |
> | resourcetypes | нет | нет |
> | validatesubscriptionmoveavailability | нет | нет |
> | virtualmachines | Да | нет |
> | virtualmachines / diagnosticsettings | нет | нет |
> | virtualmachines / metricdefinitions | нет | нет |
> | virtualmachines / metrics | нет | нет |

> [!IMPORTANT]
> См. [Руководство по перемещению классического развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | нет | нет |

> [!IMPORTANT]
> См. [Руководство по перемещению классического развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | capabilities | нет | нет |
> | expressroutecrossconnections | нет | нет |
> | expressroutecrossconnections / peerings | нет | нет |
> | gatewaysupporteddevices | нет | нет |
> | networksecuritygroups | нет | нет |
> | Операции | нет | нет |
> | quotas | нет | нет |
> | reservedips | нет | нет |
> | virtualnetworks | нет | нет |
> | virtualnetworks / remotevirtualnetworkpeeringproxies | нет | нет |
> | virtualnetworks / virtualnetworkpeerings | нет | нет |

> [!IMPORTANT]
> См. [Руководство по перемещению классического развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | capabilities | нет | нет |
> | checkstorageaccountavailability | нет | нет |
> | disks | нет | нет |
> | images | нет | нет |
> | Операции | нет | нет |
> | osimages | нет | нет |
> | osplatformimages | нет | нет |
> | publicimages | нет | нет |
> | quotas | нет | нет |
> | storageaccounts | Да | нет |
> | storageaccounts / blobservices | нет | нет |
> | storageaccounts / fileservices | нет | нет |
> | storageaccounts / metricdefinitions | нет | нет |
> | storageaccounts / metrics | нет | нет |
> | storageaccounts / queueservices | нет | нет |
> | storageaccounts / services | нет | нет |
> | storageaccounts / services / diagnosticsettings | нет | нет |
> | storageaccounts / services / metricdefinitions | нет | нет |
> | storageaccounts / services / metrics | нет | нет |
> | storageaccounts / tableservices | нет | нет |
> | storageaccounts / vmimages | нет | нет |
> | vmimages | нет | нет |

> [!IMPORTANT]
> См. [Руководство по перемещению классического развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | нет | нет |

> [!IMPORTANT]
> См. [Руководство по перемещению классического развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftcognition"></a>Microsoft.Cognition

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | syntheticsaccounts | нет | нет |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |
> | checkdomainavailability | нет | нет |
> | Расположения | нет | нет |
> | locations / checkskuavailability | нет | нет |
> | locations / deletevirtualnetworkorsubnets | нет | нет |
> | locations / operationresults | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | нет | нет |
> | ratecard | нет | нет |
> | usageaggregates | нет | нет |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Да | Да |
> | diskaccesses | нет | нет |
> | diskencryptionsets | нет | нет |
> | disks | Да | Да |
> | galleries | нет | нет |
> | galleries / images | нет | нет |
> | galleries / images / versions | нет | нет |
> | hostgroups | нет | нет |
> | hostgroups / hosts | нет | нет |
> | images | Да | Да |
> | Расположения | нет | нет |
> | locations / artifactpublishers | нет | нет |
> | locations / capsoperations | нет | нет |
> | locations / diskoperations | нет | нет |
> | locations / loganalytics | нет | нет |
> | locations / operations | нет | нет |
> | locations / publishers | нет | нет |
> | locations / runcommands | нет | нет |
> | locations / usages | нет | нет |
> | locations / virtualmachines | нет | нет |
> | locations / vmsizes | нет | нет |
> | locations / vsmoperations | нет | нет |
> | Операции | нет | нет |
> | proximityplacementgroups | Да | Да |
> | restorepointcollections | нет | нет |
> | restorepointcollections / restorepoints | нет | нет |
> | sharedvmextensions | нет | нет |
> | sharedvmimages | нет | нет |
> | sharedvmimages / versions | нет | нет |
> | snapshots | Да | Да |
> | sshpublickeys | нет | нет |
> | virtualmachines | Да | Да |
> | virtualmachines / extensions | Да | Да |
> | virtualmachines / metricdefinitions | нет | нет |
> | virtualmachines / runcommands | нет | нет |
> | virtualmachinescalesets | Да | Да |
> | virtualmachinescalesets / extensions | нет | нет |
> | virtualmachinescalesets / networkinterfaces | нет | нет |
> | virtualmachinescalesets / publicipaddresses | нет | нет |
> | virtualmachinescalesets / virtualmachines | нет | нет |
> | virtualmachinescalesets / virtualmachines / networkinterfaces | нет | нет |

> [!IMPORTANT]
> См. [Руководство по перемещению виртуальных машин](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | нет | нет |
> | balances | нет | нет |
> | budgets | нет | нет |
> | charges | нет | нет |
> | costtags | нет | нет |
> | credits | нет | нет |
> | события | нет | нет |
> | forecasts | нет | нет |
> | lots | нет | нет |
> | marketplaces | нет | нет |
> | operationresults | нет | нет |
> | Операции | нет | нет |
> | operationstatus | нет | нет |
> | pricesheets | нет | нет |
> | products | нет | нет |
> | reservationdetails | нет | нет |
> | reservationrecommendationdetails | нет | нет |
> | reservationrecommendations | нет | нет |
> | reservationsummaries | нет | нет |
> | reservationtransactions | нет | нет |
> | tags | нет | нет |
> | tenants | нет | нет |
> | terms | нет | нет |
> | usagedetails | нет | нет |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | containergroups | нет | нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | containergroups | нет | нет |
> | Расположения | нет | нет |
> | locations / cachedimages | нет | нет |
> | locations / capabilities | нет | нет |
> | locations / deletevirtualnetworkorsubnets | нет | нет |
> | locations / operations | нет | нет |
> | locations / usages | нет | нет |
> | Операции | нет | нет |
> | serviceassociationlinks | нет | нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | Расположения | нет | нет |
> | locations / authorize | нет | нет |
> | locations / deletevirtualnetworkorsubnets | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / setupauth | нет | нет |
> | Операции | нет | нет |
> | registries | Да | Да |
> | registries / agentpools | Да | Да |
> | registries / agentpools / listqueuestatus | нет | нет |
> | registries / builds | нет | нет |
> | registries / builds / cancel | нет | нет |
> | registries / builds / getloglink | нет | нет |
> | registries / buildtasks | Да | Да |
> | registries / buildtasks / listsourcerepositoryproperties | нет | нет |
> | registries / buildtasks / steps | нет | нет |
> | registries / buildtasks / steps / listbuildarguments | нет | нет |
> | registries / eventgridfilters | нет | нет |
> | registries / exportpipelines | нет | нет |
> | registries / generatecredentials | нет | нет |
> | registries / getbuildsourceuploadurl | нет | нет |
> | registries / getcredentials | нет | нет |
> | registries / importimage | нет | нет |
> | registries / importpipelines | нет | нет |
> | registries / listbuildsourceuploadurl | нет | нет |
> | registries / listcredentials | нет | нет |
> | registries / listpolicies | нет | нет |
> | registries / listusages | нет | нет |
> | registries / pipelineruns | нет | нет |
> | registries / privateendpointconnectionproxies | нет | нет |
> | registries / privateendpointconnectionproxies / validate | нет | нет |
> | registries / privateendpointconnections | нет | нет |
> | registries / privatelinkresources | нет | нет |
> | registries / queuebuild | нет | нет |
> | registries / regeneratecredential | нет | нет |
> | registries / regeneratecredentials | нет | нет |
> | registries / replications | Да | Да |
> | registries / runs | нет | нет |
> | registries / runs / cancel | нет | нет |
> | registries / runs / listlogsasurl | нет | нет |
> | registries / schedulerun | нет | нет |
> | registries / scopemaps | нет | нет |
> | registries / taskruns | нет | нет |
> | registries / taskruns / listdetails | нет | нет |
> | registries / tasks | Да | Да |
> | registries / tasks / listdetails | нет | нет |
> | registries / tokens | нет | нет |
> | registries / updatepolicies | нет | нет |
> | registries / webhooks | Да | Да |
> | registries / webhooks / getcallbackconfig | нет | нет |
> | registries / webhooks / listevents | нет | нет |
> | registries / webhooks / ping | нет | нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | containerservices | нет | нет |
> | Расположения | нет | нет |
> | locations / openshiftclusters | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operations | нет | нет |
> | locations / orchestrators | нет | нет |
> | managedclusters | нет | нет |
> | openshiftmanagedclusters | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | нет | нет |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | оповещения | нет | нет |
> | billingaccounts | нет | нет |
> | budgets | нет | нет |
> | cloudconnectors | нет | нет |
> | соединители | Да | Да |
> | departments | нет | нет |
> | dimensions | нет | нет |
> | enrollmentaccounts | нет | нет |
> | exports | нет | нет |
> | externalbillingaccounts | нет | нет |
> | externalbillingaccounts / alerts | нет | нет |
> | externalbillingaccounts / dimensions | нет | нет |
> | externalbillingaccounts / forecast | нет | нет |
> | externalbillingaccounts / query | нет | нет |
> | externalsubscriptions | нет | нет |
> | externalsubscriptions / alerts | нет | нет |
> | externalsubscriptions / dimensions | нет | нет |
> | externalsubscriptions / forecast | нет | нет |
> | externalsubscriptions / query | нет | нет |
> | прогноз | нет | нет |
> | Операции | нет | нет |
> | query | нет | нет |
> | регистрация | нет | нет |
> | reportconfigs | нет | нет |
> | reports | нет | нет |
> | Параметры | нет | нет |
> | showbackrules | нет | нет |
> | узел "Представления" | нет | нет |

## <a name="microsoftcostmanagementexports"></a>Microsoft.CostManagementExports

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | нет | нет |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | hubs | нет | нет |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | нет | нет |
> | requests | нет | нет |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | associations | нет | нет |
> | Расположения | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |
> | resourceproviders | Да | Да |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | jobs | нет | нет |
> | Расположения | нет | нет |
> | locations / availableskus | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / regionconfiguration | нет | нет |
> | locations / validateaddress | нет | нет |
> | locations / validateinputs | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | нет | нет |
> | databoxedgedevices / checknameavailability | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / getnetworkpolicies | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |
> | workspaces | нет | нет |
> | workspaces / dbworkspaces | нет | нет |
> | workspaces / virtualnetworkpeerings | нет | нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | catalogs | Да | Да |
> | checknameavailability | нет | нет |
> | datacatalogs | нет | нет |
> | Расположения | нет | нет |
> | locations / jobs | нет | нет |
> | locations / operationresults | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | нет | нет |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Пакеты | нет | нет |
> | Планы | нет | нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynameavailability | нет | нет |
> | checkdatafactorynameavailability | нет | нет |
> | datafactories | Да | Да |
> | datafactories / diagnosticsettings | нет | нет |
> | datafactories / metricdefinitions | нет | нет |
> | datafactoryschema | нет | нет |
> | factories | Да | Да |
> | factories / integrationruntimes | нет | нет |
> | Расположения | нет | нет |
> | locations / configurefactoryrepo | нет | нет |
> | locations / getfeaturevalue | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | нет | нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |
> | accounts / datalakestoreaccounts | нет | нет |
> | accounts / storageaccounts | нет | нет |
> | accounts / storageaccounts / containers | нет | нет |
> | accounts / storageaccounts / containers / listsastokens | нет | нет |
> | Расположения | нет | нет |
> | locations / capability | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / usages | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |
> | accounts / eventgridfilters | нет | нет |
> | accounts / firewallrules | нет | нет |
> | Расположения | нет | нет |
> | locations / capability | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / deletevirtualnetworkorsubnets | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / usages | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |
> | services; | нет | нет |
> | services / projects | нет | нет |
> | slots | нет | нет |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | backupvaults | нет | нет |
> | Расположения | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |
> | accounts / shares | нет | нет |
> | accounts / shares / datasets | нет | нет |
> | accounts / shares / invitations | нет | нет |
> | accounts / shares / providersharesubscriptions | нет | нет |
> | accounts / shares / synchronizationsettings | нет | нет |
> | accounts / sharesubscriptions | нет | нет |
> | accounts / sharesubscriptions / consumersourcedatasets | нет | нет |
> | accounts / sharesubscriptions / datasetmappings | нет | нет |
> | accounts / sharesubscriptions / triggers | нет | нет |
> | listinvitations | нет | нет |
> | Расположения | нет | нет |
> | locations / consumerinvitations | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / rejectinvitation | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | Расположения | нет | нет |
> | locations / azureasyncoperation | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / performancetiers | нет | нет |
> | locations / privateendpointconnectionazureasyncoperation | нет | нет |
> | locations / privateendpointconnectionoperationresults | нет | нет |
> | locations / privateendpointconnectionproxyazureasyncoperation | нет | нет |
> | locations / privateendpointconnectionproxyoperationresults | нет | нет |
> | locations / recommendedactionsessionsazureasyncoperation | нет | нет |
> | locations / recommendedactionsessionsoperationresults | нет | нет |
> | locations / securityalertpoliciesazureasyncoperation | нет | нет |
> | locations / securityalertpoliciesoperationresults | нет | нет |
> | locations / serverkeyazureasyncoperation | нет | нет |
> | locations / serverkeyoperationresults | нет | нет |
> | Операции | нет | нет |
> | servers | Да | Да |
> | servers / advisors | нет | нет |
> | servers / privateendpointconnectionproxies | нет | нет |
> | servers / privateendpointconnections | нет | нет |
> | servers / privatelinkresources | нет | нет |
> | servers / querytexts | нет | нет |
> | servers / recoverableservers | нет | нет |
> | servers / topquerystatistics | нет | нет |
> | servers / virtualnetworkrules | нет | нет |
> | servers / waitstatistics | нет | нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | Расположения | нет | нет |
> | locations / administratorazureasyncoperation | нет | нет |
> | locations / administratoroperationresults | нет | нет |
> | locations / azureasyncoperation | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / performancetiers | нет | нет |
> | locations / privateendpointconnectionazureasyncoperation | нет | нет |
> | locations / privateendpointconnectionoperationresults | нет | нет |
> | locations / privateendpointconnectionproxyazureasyncoperation | нет | нет |
> | locations / privateendpointconnectionproxyoperationresults | нет | нет |
> | locations / recommendedactionsessionsazureasyncoperation | нет | нет |
> | locations / recommendedactionsessionsoperationresults | нет | нет |
> | locations / securityalertpoliciesazureasyncoperation | нет | нет |
> | locations / securityalertpoliciesoperationresults | нет | нет |
> | locations / serverkeyazureasyncoperation | нет | нет |
> | locations / serverkeyoperationresults | нет | нет |
> | Операции | нет | нет |
> | servers | Да | Да |
> | servers / advisors | нет | нет |
> | servers / keys | нет | нет |
> | servers / privateendpointconnectionproxies | нет | нет |
> | servers / privateendpointconnections | нет | нет |
> | servers / privatelinkresources | нет | нет |
> | servers / querytexts | нет | нет |
> | servers / recoverableservers | нет | нет |
> | servers / topquerystatistics | нет | нет |
> | servers / virtualnetworkrules | нет | нет |
> | servers / waitstatistics | нет | нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | Расположения | нет | нет |
> | locations / administratorazureasyncoperation | нет | нет |
> | locations / administratoroperationresults | нет | нет |
> | locations / azureasyncoperation | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / performancetiers | нет | нет |
> | locations / privateendpointconnectionazureasyncoperation | нет | нет |
> | locations / privateendpointconnectionoperationresults | нет | нет |
> | locations / privateendpointconnectionproxyazureasyncoperation | нет | нет |
> | locations / privateendpointconnectionproxyoperationresults | нет | нет |
> | locations / recommendedactionsessionsazureasyncoperation | нет | нет |
> | locations / recommendedactionsessionsoperationresults | нет | нет |
> | locations / securityalertpoliciesazureasyncoperation | нет | нет |
> | locations / securityalertpoliciesoperationresults | нет | нет |
> | locations / serverkeyazureasyncoperation | нет | нет |
> | locations / serverkeyoperationresults | нет | нет |
> | Операции | нет | нет |
> | servergroups | нет | нет |
> | servers | Да | Да |
> | servers / advisors | нет | нет |
> | servers / keys | нет | нет |
> | servers / privateendpointconnectionproxies | нет | нет |
> | servers / privateendpointconnections | нет | нет |
> | servers / privatelinkresources | нет | нет |
> | servers / querytexts | нет | нет |
> | servers / recoverableservers | нет | нет |
> | servers / topquerystatistics | нет | нет |
> | servers / virtualnetworkrules | нет | нет |
> | servers / waitstatistics | нет | нет |
> | serversv2 | Да | Да |
> | singleservers | Да | Да |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | artifactsources | Да | Да |
> | operationresults | нет | нет |
> | Операции | нет | нет |
> | rollouts | Да | Да |
> | servicetopologies | Да | Да |
> | servicetopologies / services | Да | Да |
> | servicetopologies / services / serviceunits | Да | Да |
> | steps | Да | Да |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Да | Да |
> | applicationgroups / applications | нет | нет |
> | applicationgroups / desktops | нет | нет |
> | applicationgroups / startmenuitems | нет | нет |
> | hostpools | Да | Да |
> | hostpools / sessionhosts | нет | нет |
> | hostpools / sessionhosts / usersessions | нет | нет |
> | hostpools / usersessions | нет | нет |
> | Операции | нет | нет |
> | workspaces | Да | Да |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | checkprovisioningservicenameavailability | нет | нет |
> | elasticpools | нет | нет |
> | elasticpools / iothubtenants | нет | нет |
> | iothubs | Да | Да |
> | iothubs / eventgridfilters | нет | нет |
> | operationresults | нет | нет |
> | Операции | нет | нет |
> | provisioningservices | Да | Да |
> | usages | нет | нет |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | pipelines | Да | Да |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | controllers | Да | Да |
> | controllers / listconnectiondetails | нет | нет |
> | Расположения | нет | нет |
> | locations / checkcontainerhostmapping | нет | нет |
> | locations / operationresults | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | labcenters | нет | нет |
> | labs | Да | нет |
> | labs / environments | Да | Да |
> | labs / servicerunners | Да | Да |
> | labs / virtualmachines | Да | нет |
> | Расположения | нет | нет |
> | locations / operations | нет | нет |
> | Операции | нет | нет |
> | schedules | Да | Да |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | нет | нет |
> | Расположения | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | нет | нет |
> | databaseaccounts | Да | Да |
> | Расположения | нет | нет |
> | locations / deletevirtualnetworkorsubnets | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operationsstatus | нет | нет |
> | operationresults | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | нет | нет |
> | domains | Да | Да |
> | domains / domainownershipidentifiers | нет | нет |
> | generatessorequest | нет | нет |
> | listdomainrecommendations | нет | нет |
> | Операции | нет | нет |
> | topleveldomains | нет | нет |
> | validatedomainregistrationinformation | нет | нет |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / operationresults | нет | нет |
> | Операции | нет | нет |
> | services; | Да | Да |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | domains | Да | Да |
> | eventSubscriptions | Нет — нельзя перемещать независимо, но автоматически перемещается вместе с подписанным ресурсом. | Нет — нельзя перемещать независимо, но автоматически перемещается вместе с подписанным ресурсом. |
> | domains / topics | нет | нет |
> | eventsubscriptions | Нет — нельзя перемещать независимо, но автоматически перемещается вместе с подписанным ресурсом. | Нет — нельзя перемещать независимо, но автоматически перемещается вместе с подписанным ресурсом. |
> | extensiontopics | нет | нет |
> | Расположения | нет | нет |
> | locations / eventsubscriptions | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operationsstatus | нет | нет |
> | locations / topictypes | нет | нет |
> | operationresults | нет | нет |
> | Операции | нет | нет |
> | operationsstatus | нет | нет |
> | partnernamespaces | Да | Да |
> | partnernamespaces / eventchannels | нет | нет |
> | partnerregistrations | нет | нет |
> | partnertopics | Да | Да |
> | partnertopics / eventsubscriptions | нет | нет |
> | systemtopics | Да | Да |
> | systemtopics / eventsubscriptions | нет | нет |
> | topics | Да | Да |
> | topictypes | нет | нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | нет | нет |
> | checknameavailability | нет | нет |
> | checknamespaceavailability | нет | нет |
> | clusters | Да | Да |
> | Расположения | нет | нет |
> | locations / deletevirtualnetworkorsubnets | нет | нет |
> | пространства имен | Да | Да |
> | namespaces / authorizationrules | нет | нет |
> | namespaces / disasterrecoveryconfigs | нет | нет |
> | namespaces / disasterrecoveryconfigs / checknameavailability | нет | нет |
> | namespaces / eventhubs | нет | нет |
> | namespaces / eventhubs / authorizationrules | нет | нет |
> | namespaces / eventhubs / consumergroups | нет | нет |
> | namespaces / networkrulesets | нет | нет |
> | Операции | нет | нет |
> | sku | нет | нет |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | нет | нет |
> | Расположения | нет | нет |
> | locations / operations | нет | нет |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | пространства имен | Да | Да |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | featureproviders | нет | нет |
> | features | нет | нет |
> | Операции | нет | нет |
> | providers | нет | нет |
> | subscriptionfeatureregistrations | нет | нет |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | нет | нет |
> | automanagedvmconfigurationprofiles | нет | нет |
> | guestconfigurationassignments | нет | нет |
> | Операции | нет | нет |
> | software | нет | нет |
> | softwareupdateprofile | нет | нет |
> | softwareupdates | нет | нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | hanainstances | нет | нет |
> | Расположения | нет | нет |
> | locations / operations | нет | нет |
> | locations / operationsstatus | нет | нет |
> | Операции | нет | нет |
> | sapmonitors | Да | Да |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | нет | нет |
> | Расположения | нет | нет |
> | Операции | нет | нет |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | clusters | Да | Да |
> | clusters / applications | нет | нет |
> | clusters / operationresults | нет | нет |
> | Расположения | нет | нет |
> | locations / azureasyncoperations | нет | нет |
> | locations / billingspecs | нет | нет |
> | locations / capabilities | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / usages | нет | нет |
> | locations / validatecreaterequest | нет | нет |
> | Операции | нет | нет |

> [!IMPORTANT]
> Кластеры HDInsight можно переместить в новую подписку или группу ресурсов. Однако перемещение между подписками недоступно для сетевых ресурсов, связанных с кластером HDInsight (таких как виртуальная сеть, сетевой адаптер или подсистема балансировки нагрузки). Также невозможно переместить в новую группу ресурсов сетевой адаптер, который подключен к виртуальной машине кластера.
>
> При перемещении кластера HDInsight в новую подписку сначала необходимо переместить другие ресурсы (такие как учетная запись хранения). А затем переместить сам кластер HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | Расположения | нет | нет |
> | locations / operationresults | нет | нет |
> | Операции | нет | нет |
> | services; | Да | Да |
> | services / privateendpointconnections | нет | нет |
> | services / privatelinkresources | нет | нет |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | machines | Да | Да |
> | machines / extensions | нет | нет |
> | Операции | нет | нет |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | datamanagers | Да | Да |
> | Операции | нет | нет |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | components | нет | нет |
> | Расположения | нет | нет |
> | networkscopes | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | jobs | Да | Да |
> | Расположения | нет | нет |
> | locations / operationresults | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | actiongroups | Да | Да |
> | activitylogalerts | нет | нет |
> | alertrules | Да | Да |
> | autoscalesettings | Да | Да |
> | baseline | нет | нет |
> | calculatebaseline | нет | нет |
> | components | Да | Да |
> | components / events | нет | нет |
> | components / linkedstorageaccounts | нет | нет |
> | components / metrics | нет | нет |
> | components / pricingplans | нет | нет |
> | components / query | нет | нет |
> | datacollectionrules | нет | нет |
> | diagnosticsettings | нет | нет |
> | diagnosticsettingscategories | нет | нет |
> | eventcategories | нет | нет |
> | eventtypes | нет | нет |
> | extendeddiagnosticsettings | нет | нет |
> | guestdiagnosticsettings | нет | нет |
> | listmigrationdate | нет | нет |
> | Расположения | нет | нет |
> | locations / operationresults | нет | нет |
> | logdefinitions | нет | нет |
> | logprofiles | нет | нет |
> | журналы | нет | нет |
> | metricalerts | нет | нет |
> | metricbaselines | нет | нет |
> | metricbatch | нет | нет |
> | metricdefinitions | нет | нет |
> | metricnamespaces | нет | нет |
> | Метрики | нет | нет |
> | migratealertrules | нет | нет |
> | migratetonewpricingmodel | нет | нет |
> | myworkbooks | нет | нет |
> | notificationgroups | нет | нет |
> | Операции | нет | нет |
> | privatelinkscopeoperationstatuses | нет | нет |
> | privatelinkscopes | нет | нет |
> | privatelinkscopes / privateendpointconnectionproxies | нет | нет |
> | privatelinkscopes / privateendpointconnections | нет | нет |
> | privatelinkscopes / scopedresources | нет | нет |
> | rollbacktolegacypricingmodel | нет | нет |
> | scheduledqueryrules | Да | Да |
> | Топология | нет | нет |
> | транзакции | нет | нет |
> | vminsightsonboardingstatuses | нет | нет |
> | webtests | Да | Да |
> | workbooks | Да | Да |
> | workbooktemplates | Да | Да |

> [!IMPORTANT]
> Убедитесь, что переход на новую подписку не превышает [квоты для подписки](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | apptemplates | нет | нет |
> | checknameavailability | нет | нет |
> | checksubdomainavailability | нет | нет |
> | iotapps | Да | Да |
> | Операции | нет | нет |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Да | Да |
> | graph | Да | Да |
> | Операции | нет | нет |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | deletedvaults | нет | нет |
> | hsmpools | нет | нет |
> | Расположения | нет | нет |
> | locations / deletedvaults | нет | нет |
> | locations / deletevirtualnetworkorsubnets | нет | нет |
> | locations / operationresults | нет | нет |
> | Операции | нет | нет |
> | vaults | Да | Да |
> | vaults / accesspolicies | нет | нет |
> | vaults / eventgridfilters | нет | нет |
> | vaults / secrets | нет | нет |

> [!IMPORTANT]
> Хранилища ключей, используемые для шифрования дисков, невозможно переместить в группы ресурсов в той же подписке или между подписками.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Да | Да |
> | Расположения | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |
> | registeredsubscriptions | нет | нет |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | нет | нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | clusters | Да | Да |
> | clusters / attacheddatabaseconfigurations | нет | нет |
> | clusters / databases | нет | нет |
> | clusters / databases / dataconnections | нет | нет |
> | clusters / databases / eventhubconnections | нет | нет |
> | clusters / databases / principalassignments | нет | нет |
> | clusters / principalassignments | нет | нет |
> | Расположения | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / operationresults | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | labaccounts | нет | нет |
> | Расположения | нет | нет |
> | locations / operations | нет | нет |
> | Операции | нет | нет |
> | users | нет | нет |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | нет | нет |
> | integrationaccounts | Да | Да |
> | integrationserviceenvironments | Да | нет |
> | integrationserviceenvironments / managedapis | Да | нет |
> | isolatedenvironments | нет | нет |
> | Расположения | нет | нет |
> | locations / workflows | нет | нет |
> | Операции | нет | нет |
> | workflows | Да | Да |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | commitmentplans | нет | нет |
> | Расположения | нет | нет |
> | locations / operations | нет | нет |
> | locations / operationsstatus | нет | нет |
> | Операции | нет | нет |
> | webservices | Да | нет |
> | workspaces | Да | Да |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | нет | нет |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |
> | accounts / workspaces | нет | нет |
> | accounts / workspaces / projects | нет | нет |
> | teamaccounts | нет | нет |
> | teamaccounts / workspaces | нет | нет |
> | teamaccounts / workspaces / projects | нет | нет |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | нет | нет |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / computeoperationsstatus | нет | нет |
> | locations / quotas | нет | нет |
> | locations / updatequotas | нет | нет |
> | locations / usages | нет | нет |
> | locations / vmsizes | нет | нет |
> | locations / workspaceoperationsstatus | нет | нет |
> | Операции | нет | нет |
> | workspaces | нет | нет |
> | workspaces / computes | нет | нет |
> | workspaces / eventgridfilters | нет | нет |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | applyupdates | нет | нет |
> | configurationassignments | нет | нет |
> | maintenanceconfigurations | Да | Да |
> | updates | нет | нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | удостоверения; | нет | нет |
> | Операции | нет | нет |
> | userassignedidentities | нет | нет |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | managednetworks | нет | нет |
> | managednetworks / managednetworkgroups | нет | нет |
> | managednetworks / managednetworkpeeringpolicies | нет | нет |
> | уведомление | нет | нет |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | нет | нет |
> | Операции | нет | нет |
> | operationstatuses | нет | нет |
> | registrationassignments | нет | нет |
> | registrationdefinitions | нет | нет |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | getentities | нет | нет |
> | managementgroups | нет | нет |
> | managementgroups / settings | нет | нет |
> | operationresults | нет | нет |
> | operationresults / asyncoperation | нет | нет |
> | Операции | нет | нет |
> | ресурсов | нет | нет |
> | starttenantbackfill | нет | нет |
> | tenantbackfillstatus | нет | нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |
> | accounts / eventgridfilters | нет | нет |
> | accounts / privateatlases | Да | Да |
> | Операции | нет | нет |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | нет | нет |
> | offers | нет | нет |
> | offertypes | нет | нет |
> | offertypes / publishers | нет | нет |
> | offertypes / publishers / offers | нет | нет |
> | offertypes / publishers / offers / plans | нет | нет |
> | offertypes / publishers / offers / plans / agreements | нет | нет |
> | offertypes / publishers / offers / plans / configs | нет | нет |
> | offertypes / publishers / offers / plans / configs / importimage | нет | нет |
> | Операции | нет | нет |
> | privategalleryitems | нет | нет |
> | privatestoreclient | нет | нет |
> | privatestores | нет | нет |
> | privatestores / offers | нет | нет |
> | products | нет | нет |
> | publishers | нет | нет |
> | publishers / offers | нет | нет |
> | publishers / offers / amendments | нет | нет |
> | регистрация | нет | нет |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | classicdevservices | нет | нет |
> | listcommunicationpreference | нет | нет |
> | Операции | нет | нет |
> | updatecommunicationpreference | нет | нет |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | agreements | нет | нет |
> | offertypes | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | Расположения | нет | нет |
> | locations / checknameavailability | нет | нет |
> | mediaservices | Да | Да |
> | mediaservices / accountfilters | нет | нет |
> | mediaservices / assets | нет | нет |
> | mediaservices / assets / assetfilters | нет | нет |
> | mediaservices / contentkeypolicies | нет | нет |
> | mediaservices / eventgridfilters | нет | нет |
> | mediaservices / liveeventoperations | нет | нет |
> | mediaservices / liveevents | Да | Да |
> | mediaservices / liveevents / liveoutputs | нет | нет |
> | mediaservices / liveoutputoperations | нет | нет |
> | mediaservices / streamingendpointoperations | нет | нет |
> | mediaservices / streamingendpoints | Да | Да |
> | mediaservices / streaminglocators | нет | нет |
> | mediaservices / streamingpolicies | нет | нет |
> | mediaservices / transforms | нет | нет |
> | mediaservices / transforms / jobs | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | appclusters | нет | нет |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Да | Да |
> | Расположения | нет | нет |
> | locations / assessmentoptions | нет | нет |
> | locations / checknameavailability | нет | нет |
> | migrateprojects | Да | Да |
> | movecollections | нет | нет |
> | Операции | нет | нет |
> | projects | нет | нет |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | нет | нет |
> | Расположения | нет | нет |
> | locations / checknameavailability | нет | нет |
> | objectunderstandingaccounts | нет | нет |
> | Операции | нет | нет |
> | remoterenderingaccounts | Да | Да |
> | spatialanchorsaccounts | Да | Да |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | netappaccounts | нет | нет |
> | netappaccounts / backuppolicies | нет | нет |
> | netappaccounts / capacitypools | нет | нет |
> | netappaccounts / capacitypools / volumes | нет | нет |
> | netappaccounts / capacitypools / volumes / mounttargets | нет | нет |
> | netappaccounts / capacitypools / volumes / snapshots | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | нет | нет |
> | applicationgatewayavailableresponseheaders | нет | нет |
> | applicationgatewayavailableservervariables | нет | нет |
> | applicationgatewayavailablessloptions | нет | нет |
> | applicationgatewayavailablewafrulesets | нет | нет |
> | applicationgateways | нет | нет |
> | applicationgatewaywebapplicationfirewallpolicies | нет | нет |
> | applicationsecuritygroups | Да | Да |
> | azurefirewallfqdntags | нет | нет |
> | azurefirewalls | нет | нет |
> | bastionhosts | нет | нет |
> | bgpservicecommunities | нет | нет |
> | checkfrontdoornameavailability | нет | нет |
> | checktrafficmanagernameavailability | нет | нет |
> | connections | Да | Да |
> | ddoscustompolicies | Да | Да |
> | ddosprotectionplans | нет | нет |
> | dnsoperationresults | нет | нет |
> | dnsoperationstatuses | нет | нет |
> | dnszones | Да | Да |
> | dnszones / a | нет | нет |
> | dnszones / aaaa | нет | нет |
> | dnszones / all | нет | нет |
> | dnszones / caa | нет | нет |
> | dnszones / cname | нет | нет |
> | dnszones / mx | нет | нет |
> | dnszones / ns | нет | нет |
> | dnszones / ptr | нет | нет |
> | dnszones / recordsets | нет | нет |
> | dnszones / soa | нет | нет |
> | dnszones / srv | нет | нет |
> | dnszones / txt | нет | нет |
> | expressroutecircuits | нет | нет |
> | expressroutegateways | нет | нет |
> | expressrouteserviceproviders | нет | нет |
> | firewallpolicies | Да | Да |
> | frontdooroperationresults | нет | нет |
> | frontdoors | нет | нет |
> | frontdoorwebapplicationfirewallmanagedrulesets | нет | нет |
> | frontdoorwebapplicationfirewallpolicies | нет | нет |
> | getdnsresourcereference | нет | нет |
> | internalnotify | нет | нет |
> | ipgroups | Да | Да |
> | loadbalancers | Да — Базовый SKU<br>Нет — Стандартный SKU | Да — Базовый SKU<br>Нет — Стандартный SKU |
> | localnetworkgateways | Да | Да |
> | Расположения | нет | нет |
> | locations / autoapprovedprivatelinkservices | нет | нет |
> | locations / availabledelegations | нет | нет |
> | locations / availableprivateendpointtypes | нет | нет |
> | locations / availableservicealiases | нет | нет |
> | locations / baremetaltenants | нет | нет |
> | locations / batchnotifyprivateendpointsforresourcemove | нет | нет |
> | locations / batchvalidateprivateendpointsforresourcemove | нет | нет |
> | locations / checkacceleratednetworkingsupport | нет | нет |
> | locations / checkdnsnameavailability | нет | нет |
> | locations / checkprivatelinkservicevisibility | нет | нет |
> | locations / commitinternalazurenetworkmanagerconfiguration | нет | нет |
> | locations / effectiveresourceownership | нет | нет |
> | locations / nfvoperationresults | нет | нет |
> | locations / nfvoperations | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operations | нет | нет |
> | locations / servicetags | нет | нет |
> | locations / setresourceownership | нет | нет |
> | locations / supportedvirtualmachinesizes | нет | нет |
> | locations / usages | нет | нет |
> | locations / validateresourceownership | нет | нет |
> | locations / virtualnetworkavailableendpointservices | нет | нет |
> | natgateways | Да | Да |
> | networkexperimentprofiles | нет | нет |
> | networkintentpolicies | Да | Да |
> | networkinterfaces | Да | Да |
> | networkprofiles | нет | нет |
> | networksecuritygroups | Да | Да |
> | networkwatchers | Да | нет |
> | networkwatchers / connectionmonitors | Да | нет |
> | networkwatchers / flowlogs | Да | нет |
> | networkwatchers / pingmeshes | Да | нет |
> | Операции | нет | нет |
> | p2svpngateways | нет | нет |
> | privatednsoperationresults | нет | нет |
> | privatednsoperationstatuses | нет | нет |
> | privatednszones | Да | Да |
> | privatednszones / a | нет | нет |
> | privatednszones / aaaa | нет | нет |
> | privatednszones / all | нет | нет |
> | privatednszones / cname | нет | нет |
> | privatednszones / mx | нет | нет |
> | privatednszones / ptr | нет | нет |
> | privatednszones / soa | нет | нет |
> | privatednszones / srv | нет | нет |
> | privatednszones / txt | нет | нет |
> | privatednszones / virtualnetworklinks | Да | Да |
> | privatednszonesinternal | нет | нет |
> | privateendpointredirectmaps | нет | нет |
> | privateendpoints | Да | Да |
> | privatelinkservices | нет | нет |
> | publicipaddresses | Да — Базовый SKU<br>Нет — Стандартный SKU | Да — Базовый SKU<br>Нет — Стандартный SKU |
> | publicipprefixes | Да | Да |
> | routefilters | нет | нет |
> | routetables | Да | Да |
> | securitypartnerproviders | Да | Да |
> | serviceendpointpolicies | Да | Да |
> | trafficmanagergeographichierarchies | нет | нет |
> | trafficmanagerprofiles | Да | Да |
> | trafficmanagerprofiles / heatmaps | нет | нет |
> | trafficmanagerusermetricskeys | нет | нет |
> | virtualhubs | нет | нет |
> | virtualnetworkgateways | Да | Да |
> | virtualnetworks | Да | Да |
> | virtualnetworktaps | нет | нет |
> | virtualrouters | Да | Да |
> | virtualwans | нет | нет |
> | vpngateways (Virtual WAN) | нет | нет |
> | vpnserverconfigurations | нет | нет |
> | vpnsites (Virtual WAN) | нет | нет |

> [!IMPORTANT]
> См. [руководство по перемещению сети](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | checknamespaceavailability | нет | нет |
> | пространства имен | Да | Да |
> | namespaces / notificationhubs | Да | Да |
> | operationresults | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Да | Да |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | hypervsites | Да | Да |
> | importsites | Да | Да |
> | Операции | нет | нет |
> | serversites | Да | Да |
> | vmwaresites | Да | Да |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | clusters | нет | нет |
> | deletedworkspaces | нет | нет |
> | linktargets | нет | нет |
> | Расположения | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |
> | storageinsightconfigs | нет | нет |
> | workspaces | Да | Да |
> | workspaces / datasources | нет | нет |
> | workspaces / linkedservices | нет | нет |
> | workspaces / linkedstorageaccounts | нет | нет |
> | workspaces / metadata | нет | нет |
> | workspaces / query | нет | нет |
> | workspaces / scopedprivatelinkproxies | нет | нет |

> [!IMPORTANT]
> Убедитесь, что переход на новую подписку не превышает [квоты для подписки](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Рабочие области, имеющие связанную учетную запись автоматизации, нельзя перемещать. Прежде чем начать операцию перемещения, не забудьте удалить связь с учетными записями службы автоматизации.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | managementassociations | нет | нет |
> | managementconfigurations | Да | Да |
> | Операции | нет | нет |
> | solutions | Да | Да |
> | узел "Представления" | Да | Да |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | нет | нет |
> | legacypeerings | нет | нет |
> | Операции | нет | нет |
> | peerasns | нет | нет |
> | peeringlocations | нет | нет |
> | peerings | Да | Да |
> | peeringservicecountries | нет | нет |
> | peeringservicelocations | нет | нет |
> | peeringserviceproviders | нет | нет |
> | peeringservices | нет | нет |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | нет | нет |
> | Операции | нет | нет |
> | policyevents | нет | нет |
> | policystates | нет | нет |
> | policytrackedresources | нет | нет |
> | remediations | нет | нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | consoles | нет | нет |
> | dashboards | Да | Да |
> | Расположения | нет | нет |
> | locations / consoles | нет | нет |
> | locations / usersettings | нет | нет |
> | Операции | нет | нет |
> | usersettings | нет | нет |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | rootresources | нет | нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / checknameavailability | нет | нет |
> | workspacecollections | Да | Да |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | capacities | Да | Да |
> | Расположения | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |
> | checknameavailability | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | availableaccounts | нет | нет |
> | providerregistrations | нет | нет |
> | providerregistrations / resourcetyperegistrations | нет | нет |
> | rollouts | нет | нет |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |
> | workspaces | нет | нет |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | нет | нет |
> | Расположения | нет | нет |
> | locations / allocatedstamp | нет | нет |
> | locations / allocatestamp | нет | нет |
> | locations / backupaadproperties | нет | нет |
> | locations / backupcrossregionrestore | нет | нет |
> | locations / backupcrrjob | нет | нет |
> | locations / backupcrrjobs | нет | нет |
> | locations / backupcrroperationresults | нет | нет |
> | locations / backupcrroperationsstatus | нет | нет |
> | locations / backupprevalidateprotection | нет | нет |
> | locations / backupstatus | нет | нет |
> | locations / backupvalidatefeatures | нет | нет |
> | locations / checknameavailability | нет | нет |
> | Операции | нет | нет |
> | replicationeligibilityresults | нет | нет |
> | vaults | Да | Да |

> [!IMPORTANT]
> Ознакомьтесь с разделом [Руководство по перемещению служб восстановления](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operationsstatus | нет | нет |
> | openshiftclusters | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | пространства имен | Да | Да |
> | namespaces / authorizationrules | нет | нет |
> | namespaces / hybridconnections | нет | нет |
> | namespaces / hybridconnections / authorizationrules | нет | нет |
> | namespaces / privateendpointconnections | нет | нет |
> | namespaces / wcfrelays | нет | нет |
> | namespaces / wcfrelays / authorizationrules | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | нет | нет |
> | Запросы | Да | Да |
> | resourcechangedetails | нет | нет |
> | resourcechanges | нет | нет |
> | ресурсов | нет | нет |
> | resourceshistory | нет | нет |
> | subscriptionsstatus | нет | нет |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | нет | нет |
> | childavailabilitystatuses | нет | нет |
> | childresources | нет | нет |
> | emergingissues | нет | нет |
> | события | нет | нет |
> | метаданные | нет | нет |
> | Уведомления | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkpolicycompliance | нет | нет |
> | checkresourcename | нет | нет |
> | deployments | нет | нет |
> | deployments / operations | нет | нет |
> | deploymentscripts | нет | нет |
> | deploymentscripts / logs | нет | нет |
> | ссылки | нет | нет |
> | Расположения | нет | нет |
> | locations / deploymentscriptoperationresults | нет | нет |
> | notifyresourcejobs | нет | нет |
> | operationresults | нет | нет |
> | Операции | нет | нет |
> | providers | нет | нет |
> | resourcegroups | нет | нет |
> | ресурсов | нет | нет |
> | subscriptions | нет | нет |
> | subscriptions / locations | нет | нет |
> | subscriptions / operationresults | нет | нет |
> | subscriptions / providers | нет | нет |
> | subscriptions / resourcegroups | нет | нет |
> | subscriptions / resourcegroups / resources | нет | нет |
> | subscriptions / resources | нет | нет |
> | subscriptions / tagnames | нет | нет |
> | subscriptions / tagnames / tagvalues | нет | нет |
> | tags | нет | нет |
> | templatespecs | нет | нет |
> | templatespecs / versions | нет | нет |
> | tenants | нет | нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | Да | нет |
> | checkmoderneligibility | нет | нет |
> | checknameavailability | нет | нет |
> | operationresults | нет | нет |
> | Операции | нет | нет |
> | saasresources | нет | нет |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | checkservicenameavailability | нет | нет |
> | Операции | нет | нет |
> | resourcehealthmetadata | нет | нет |
> | searchservices | Да | Да |

> [!IMPORTANT]
> Вы не можете за одну операцию переместить ресурсы поиска, размещенные в разных регионах. Переместите их в отдельных операциях.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | нет | нет |
> | advancedthreatprotectionsettings | нет | нет |
> | оповещения | нет | нет |
> | allowedconnections | нет | нет |
> | applicationwhitelistings | нет | нет |
> | assessmentmetadata | нет | нет |
> | assessments | нет | нет |
> | autodismissalertsrules | нет | нет |
> | automations | Да | Да |
> | autoprovisioningsettings | нет | нет |
> | complianceresults | нет | нет |
> | compliances | нет | нет |
> | datacollectionagents | нет | нет |
> | devicesecuritygroups | нет | нет |
> | discoveredsecuritysolutions | нет | нет |
> | externalsecuritysolutions | нет | нет |
> | informationprotectionpolicies | нет | нет |
> | iotsecuritysolutions | Да | Да |
> | iotsecuritysolutions / analyticsmodels | нет | нет |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | нет | нет |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | нет | нет |
> | jitnetworkaccesspolicies | нет | нет |
> | Расположения | нет | нет |
> | locations / alerts | нет | нет |
> | locations / allowedconnections | нет | нет |
> | locations / applicationwhitelistings | нет | нет |
> | locations / discoveredsecuritysolutions | нет | нет |
> | locations / externalsecuritysolutions | нет | нет |
> | locations / jitnetworkaccesspolicies | нет | нет |
> | locations / securitysolutions | нет | нет |
> | locations / securitysolutionsreferencedata | нет | нет |
> | locations / tasks | нет | нет |
> | locations / topologies | нет | нет |
> | Операции | нет | нет |
> | политики | нет | нет |
> | pricings | нет | нет |
> | regulatorycompliancestandards | нет | нет |
> | regulatorycompliancestandards / regulatorycompliancecontrols | нет | нет |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | нет | нет |
> | securitycontacts | нет | нет |
> | securitysolutions | нет | нет |
> | securitysolutionsreferencedata | нет | нет |
> | securitystatuses | нет | нет |
> | securitystatusessummaries | нет | нет |
> | servervulnerabilityassessments | нет | нет |
> | Параметры | нет | нет |
> | subassessments | нет | нет |
> | задачи | нет | нет |
> | topologies | нет | нет |
> | workspacesettings | нет | нет |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | aggregations | нет | нет |
> | alertrules | нет | нет |
> | alertruletemplates | нет | нет |
> | bookmarks | нет | нет |
> | cases | нет | нет |
> | dataconnectors | нет | нет |
> | dataconnectorscheckrequirements | нет | нет |
> | Сущности | нет | нет |
> | entityqueries | нет | нет |
> | incidents | нет | нет |
> | officeconsents | нет | нет |
> | Операции | нет | нет |
> | Параметры | нет | нет |
> | threatintelligence | нет | нет |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | consoleservices | нет | нет |
> | Расположения | нет | нет |
> | locations / consoleservices | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | gateways | нет | нет |
> | Узлы | нет | нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | checknamespaceavailability | нет | нет |
> | Расположения | нет | нет |
> | locations / deletevirtualnetworkorsubnets | нет | нет |
> | пространства имен | Да | Да |
> | namespaces / authorizationrules | нет | нет |
> | namespaces / disasterrecoveryconfigs | нет | нет |
> | namespaces / disasterrecoveryconfigs / checknameavailability | нет | нет |
> | namespaces / eventgridfilters | нет | нет |
> | namespaces / networkrulesets | нет | нет |
> | namespaces / queues | нет | нет |
> | namespaces / queues / authorizationrules | нет | нет |
> | namespaces / topics | нет | нет |
> | namespaces / topics / authorizationrules | нет | нет |
> | namespaces / topics / subscriptions | нет | нет |
> | namespaces / topics / subscriptions / rules | нет | нет |
> | Операции | нет | нет |
> | premiummessagingregions | нет | нет |
> | sku | нет | нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | нет | нет |
> | clusters | Да | Да |
> | clusters / applications | нет | нет |
> | containergroups | нет | нет |
> | containergroupsets | нет | нет |
> | edgeclusters | нет | нет |
> | Расположения | нет | нет |
> | locations / clusterversions | нет | нет |
> | locations / environments | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operations | нет | нет |
> | managedclusters | нет | нет |
> | networks | нет | нет |
> | Операции | нет | нет |
> | secretstores | нет | нет |
> | volumes. | нет | нет |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | Да | Да |
> | containergroups | нет | нет |
> | gateways | Да | Да |
> | Расположения | нет | нет |
> | locations / applicationoperations | нет | нет |
> | locations / gatewayoperations | нет | нет |
> | locations / networkoperations | нет | нет |
> | locations / secretoperations | нет | нет |
> | locations / volumeoperations | нет | нет |
> | networks | Да | Да |
> | Операции | нет | нет |
> | секретные коды | Да | Да |
> | volumes. | Да | Да |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | providerregistrations | нет | нет |
> | providerregistrations / resourcetyperegistrations | нет | нет |
> | rollouts | нет | нет |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operationstatuses | нет | нет |
> | locations / usages | нет | нет |
> | Операции | нет | нет |
> | signalr | Да | Да |
> | signalr / eventgridfilters | нет | нет |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | нет | нет |
> | веб-масштабированием; | нет | нет |
> | jitrequests | нет | нет |
> | Расположения | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | instancepools | нет | нет |
> | Расположения | Да | Да |
> | Расположения | нет | нет |
> | locations / administratorazureasyncoperation | нет | нет |
> | locations / administratoroperationresults | нет | нет |
> | locations / auditingsettingsazureasyncoperation | нет | нет |
> | locations / auditingsettingsoperationresults | нет | нет |
> | locations / capabilities | нет | нет |
> | locations / databaseazureasyncoperation | нет | нет |
> | locations / databaseoperationresults | нет | нет |
> | locations / databaserestoreazureasyncoperation | нет | нет |
> | locations / deletevirtualnetworkorsubnets | нет | нет |
> | locations / deletevirtualnetworkorsubnetsazureasyncoperation | нет | нет |
> | locations / deletevirtualnetworkorsubnetsoperationresults | нет | нет |
> | locations / dnsaliasasyncoperation | нет | нет |
> | locations / dnsaliasoperationresults | нет | нет |
> | locations / elasticpoolazureasyncoperation | нет | нет |
> | locations / elasticpooloperationresults | нет | нет |
> | locations / encryptionprotectorazureasyncoperation | нет | нет |
> | locations / encryptionprotectoroperationresults | нет | нет |
> | locations / extendedauditingsettingsazureasyncoperation | нет | нет |
> | locations / extendedauditingsettingsoperationresults | нет | нет |
> | locations / failovergroupazureasyncoperation | нет | нет |
> | locations / failovergroupoperationresults | нет | нет |
> | locations / firewallrulesazureasyncoperation | нет | нет |
> | locations / firewallrulesoperationresults | нет | нет |
> | locations / instancefailovergroupazureasyncoperation | нет | нет |
> | locations / instancefailovergroupoperationresults | нет | нет |
> | locations / instancefailovergroups | нет | нет |
> | locations / instancepoolazureasyncoperation | нет | нет |
> | locations / instancepooloperationresults | нет | нет |
> | locations / jobagentazureasyncoperation | нет | нет |
> | locations / jobagentoperationresults | нет | нет |
> | locations / longtermretentionbackupazureasyncoperation | нет | нет |
> | locations / longtermretentionbackupoperationresults | нет | нет |
> | locations / longtermretentionbackups | нет | нет |
> | locations / longtermretentionmanagedinstancebackupazureasyncoperation | нет | нет |
> | locations / longtermretentionmanagedinstancebackupoperationresults | нет | нет |
> | locations / longtermretentionmanagedinstancebackups | нет | нет |
> | locations / longtermretentionmanagedinstances | нет | нет |
> | locations / longtermretentionpolicyazureasyncoperation | нет | нет |
> | locations / longtermretentionpolicyoperationresults | нет | нет |
> | locations / longtermretentionservers | нет | нет |
> | locations / manageddatabaseazureasyncoperation | нет | нет |
> | locations / manageddatabasecompleterestoreazureasyncoperation | нет | нет |
> | locations / manageddatabasecompleterestoreoperationresults | нет | нет |
> | locations / manageddatabaseoperationresults | нет | нет |
> | locations / manageddatabaserestoreazureasyncoperation | нет | нет |
> | locations / manageddatabaserestoreoperationresults | нет | нет |
> | locations / managedinstanceazureasyncoperation | нет | нет |
> | locations / managedinstanceencryptionprotectorazureasyncoperation | нет | нет |
> | locations / managedinstanceencryptionprotectoroperationresults | нет | нет |
> | locations / managedinstancekeyazureasyncoperation | нет | нет |
> | locations / managedinstancekeyoperationresults | нет | нет |
> | locations / managedinstancelongtermretentionpolicyazureasyncoperation | нет | нет |
> | locations / managedinstancelongtermretentionpolicyoperationresults | нет | нет |
> | locations / managedinstanceoperationresults | нет | нет |
> | locations / managedinstancetdecertazureasyncoperation | нет | нет |
> | locations / managedinstancetdecertoperationresults | нет | нет |
> | locations / managedserversecurityalertpoliciesazureasyncoperation | нет | нет |
> | locations / managedserversecurityalertpoliciesoperationresults | нет | нет |
> | locations / managedshorttermretentionpolicyazureasyncoperation | нет | нет |
> | locations / managedshorttermretentionpolicyoperationresults | нет | нет |
> | locations / notifyazureasyncoperation | нет | нет |
> | locations / privateendpointconnectionazureasyncoperation | нет | нет |
> | locations / privateendpointconnectionoperationresults | нет | нет |
> | locations / privateendpointconnectionproxyazureasyncoperation | нет | нет |
> | locations / privateendpointconnectionproxyoperationresults | нет | нет |
> | locations / securityalertpoliciesazureasyncoperation | нет | нет |
> | locations / securityalertpoliciesoperationresults | нет | нет |
> | locations / serveradministratorazureasyncoperation | нет | нет |
> | locations / serveradministratoroperationresults | нет | нет |
> | locations / serverazureasyncoperation | нет | нет |
> | locations / serverkeyazureasyncoperation | нет | нет |
> | locations / serverkeyoperationresults | нет | нет |
> | locations / serveroperationresults | нет | нет |
> | locations / shorttermretentionpolicyazureasyncoperation | нет | нет |
> | locations / shorttermretentionpolicyoperationresults | нет | нет |
> | locations / syncagentoperationresults | нет | нет |
> | locations / syncdatabaseids | нет | нет |
> | locations / syncgroupoperationresults | нет | нет |
> | locations / syncmemberoperationresults | нет | нет |
> | locations / tdecertazureasyncoperation | нет | нет |
> | locations / tdecertoperationresults | нет | нет |
> | locations / usages | нет | нет |
> | locations / virtualclusterazureasyncoperation | нет | нет |
> | locations / virtualclusteroperationresults | нет | нет |
> | locations / virtualnetworkrulesazureasyncoperation | нет | нет |
> | locations / virtualnetworkrulesoperationresults | нет | нет |
> | locations / vulnerabilityassessmentscanazureasyncoperation | нет | нет |
> | locations / vulnerabilityassessmentscanoperationresults | нет | нет |
> | managedinstances | нет | нет |
> | managedinstances / administrators | нет | нет |
> | managedinstances / databases | нет | нет |
> | managedinstances / databases / backuplongtermretentionpolicies | нет | нет |
> | managedinstances / databases / vulnerabilityassessments | нет | нет |
> | managedinstances / metricdefinitions | нет | нет |
> | managedinstances / metrics | нет | нет |
> | managedinstances / recoverabledatabases | нет | нет |
> | managedinstances / tdecertificates | нет | нет |
> | managedinstances / vulnerabilityassessments | нет | нет |
> | Операции | нет | нет |
> | servers | Да | Да |
> | servers / administratoroperationresults | нет | нет |
> | servers / administrators | нет | нет |
> | servers / advisors | нет | нет |
> | servers / aggregateddatabasemetrics | нет | нет |
> | servers / auditingpolicies | нет | нет |
> | servers / auditingsettings | нет | нет |
> | servers / automatictuning | нет | нет |
> | servers / communicationlinks | нет | нет |
> | servers / connectionpolicies | нет | нет |
> | servers / databases | Да | Да |
> | servers / databases / advisors | нет | нет |
> | servers / databases / auditingpolicies | нет | нет |
> | servers / databases / auditingsettings | нет | нет |
> | servers / databases / auditrecords | нет | нет |
> | servers / databases / automatictuning | нет | нет |
> | servers / databases / backuplongtermretentionpolicies | нет | нет |
> | servers / databases / backupshorttermretentionpolicies | нет | нет |
> | servers / databases / connectionpolicies | нет | нет |
> | servers / databases / datamaskingpolicies | нет | нет |
> | servers / databases / datamaskingpolicies / rules | нет | нет |
> | servers / databases / extensions | нет | нет |
> | servers / databases / geobackuppolicies | нет | нет |
> | servers / databases / metricdefinitions | нет | нет |
> | servers / databases / metrics | нет | нет |
> | servers / databases / recommendedsensitivitylabels | нет | нет |
> | servers / databases / securityalertpolicies | нет | нет |
> | servers / databases / syncgroups | нет | нет |
> | servers / databases / syncgroups / syncmembers | нет | нет |
> | servers / databases / topqueries | нет | нет |
> | servers / databases / topqueries / querytext | нет | нет |
> | servers / databases / transparentdataencryption | нет | нет |
> | servers / databases / vulnerabilityassessment | нет | нет |
> | servers / databases / vulnerabilityassessments | нет | нет |
> | servers / databases / vulnerabilityassessmentscans | нет | нет |
> | servers / databases / vulnerabilityassessmentsettings | нет | нет |
> | servers / databases / workloadgroups | нет | нет |
> | servers / databasesecuritypolicies | нет | нет |
> | servers / disasterrecoveryconfiguration | нет | нет |
> | servers / dnsaliases | нет | нет |
> | servers / elasticpoolestimates | нет | нет |
> | servers / elasticpools | Да | Да |
> | servers / elasticpools / advisors | нет | нет |
> | servers / elasticpools / metricdefinitions | нет | нет |
> | servers / elasticpools / metrics | нет | нет |
> | servers / encryptionprotector | нет | нет |
> | servers / extendedauditingsettings | нет | нет |
> | servers / failovergroups | нет | нет |
> | servers / import | нет | нет |
> | servers / importexportoperationresults | нет | нет |
> | servers / jobaccounts | Да | Да |
> | servers / jobagents | Да | Да |
> | servers / jobagents / jobs | нет | нет |
> | servers / jobagents / jobs / executions | нет | нет |
> | servers / jobagents / jobs / steps | нет | нет |
> | servers / keys | нет | нет |
> | servers / operationresults | нет | нет |
> | servers / recommendedelasticpools | нет | нет |
> | servers / recoverabledatabases | нет | нет |
> | servers / restorabledroppeddatabases | нет | нет |
> | servers / securityalertpolicies | нет | нет |
> | servers / serviceobjectives | нет | нет |
> | servers / syncagents | нет | нет |
> | servers / tdecertificates | нет | нет |
> | servers / usages | нет | нет |
> | servers / virtualnetworkrules | нет | нет |
> | servers / vulnerabilityassessments | нет | нет |
> | virtualclusters | Да | Да |

> [!IMPORTANT]
> База данных и сервер должны быть в одной группе ресурсов. При перемещении сервера SQL Server все его базы данных также перемещаются. Такое поведение также характерно для баз данных SQL Azure и баз данных хранилища данных SQL Azure.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / availabilitygrouplisteneroperationresults | нет | нет |
> | locations / operationtypes | нет | нет |
> | locations / sqlvirtualmachinegroupoperationresults | нет | нет |
> | locations / sqlvirtualmachineoperationresults | нет | нет |
> | Операции | нет | нет |
> | sqlvirtualmachinegroups | Да | Да |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | нет | нет |
> | sqlvirtualmachines | Да | Да |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | dwvm | нет | нет |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | Расположения | нет | нет |
> | locations / asyncoperations | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / deletevirtualnetworkorsubnets | нет | нет |
> | locations / usages | нет | нет |
> | Операции | нет | нет |
> | storageaccounts | Да | Да |
> | storageaccounts / blobservices | нет | нет |
> | storageaccounts / fileservices | нет | нет |
> | storageaccounts / listaccountsas | нет | нет |
> | storageaccounts / listservicesas | нет | нет |
> | storageaccounts / queueservices | нет | нет |
> | storageaccounts / services | нет | нет |
> | storageaccounts / services / metricdefinitions | нет | нет |
> | storageaccounts / tableservices | нет | нет |
> | usages | нет | нет |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | caches | нет | нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / workflows | нет | нет |
> | Операции | нет | нет |
> | storagesyncservices | Да | Да |
> | storagesyncservices / registeredservers | нет | нет |
> | storagesyncservices / syncgroups | нет | нет |
> | storagesyncservices / syncgroups / cloudendpoints | нет | нет |
> | storagesyncservices / syncgroups / serverendpoints | нет | нет |
> | storagesyncservices / workflows | нет | нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | нет | нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | нет | нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | managers | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | clusters | нет | нет |
> | Расположения | нет | нет |
> | locations / quotas | нет | нет |
> | Операции | нет | нет |
> | streamingjobs | Да | Да |

> [!IMPORTANT]
> Задания Stream Analytics в состоянии выполнения нельзя переместить.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | environments | нет | нет |
> | environments / eventsources | нет | нет |
> | instances | нет | нет |
> | instances / environments | нет | нет |
> | instances / environments / eventsources | нет | нет |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | cancel | нет | нет |
> | createsubscription | нет | нет |
> | enable | нет | нет |
> | operationresults | нет | нет |
> | Операции | нет | нет |
> | переименовать | нет | нет |
> | subscriptiondefinitions | нет | нет |
> | subscriptionoperations | нет | нет |
> | subscriptions | нет | нет |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | operationresults | нет | нет |
> | Операции | нет | нет |
> | operationsstatus | нет | нет |
> | services; | нет | нет |
> | services / problemclassifications | нет | нет |
> | supporttickets | нет | нет |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | Операции | нет | нет |
> | workspaces | Да | Да |
> | workspaces / bigdatapools | Да | Да |
> | workspaces / operationresults | нет | нет |
> | workspaces / operationstatuses | нет | нет |
> | workspaces / sqlpools | Да | Да |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | providerregistrations | нет | нет |
> | ресурсов | нет | нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | environments | Да | Да |
> | environments / accesspolicies | нет | нет |
> | environments / eventsources | Да | Да |
> | environments / referencedatasets | Да | Да |
> | Операции | нет | нет |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | stores | Да | Да |
> | stores / accesspolicies | нет | нет |
> | stores / services | нет | нет |
> | stores / services / tokens | нет | нет |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | imagetemplates | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | account | нет | нет |
> | account / extension | нет | нет |
> | account / project | нет | нет |
> | checknameavailability | нет | нет |
> | Операции | нет | нет |

> [!IMPORTANT]
> Чтобы изменить подписку на Azure DevOps, см. статью [Изменить подписку Azure, используемую для выставления счетов](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | arczones | нет | нет |
> | Расположения | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |
> | resourcepools | нет | нет |
> | vcenters | нет | нет |
> | virtualmachines | нет | нет |
> | virtualmachinetemplates | нет | нет |
> | virtualnetworks | нет | нет |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | нет | нет |
> | dedicatedcloudservices | нет | нет |
> | Расположения | нет | нет |
> | locations / availabilities | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / privateclouds | нет | нет |
> | locations / privateclouds / resourcepools | нет | нет |
> | locations / privateclouds / virtualmachinetemplates | нет | нет |
> | locations / privateclouds / virtualnetworks | нет | нет |
> | locations / usages | нет | нет |
> | Операции | нет | нет |
> | virtualmachines | нет | нет |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | устройства | нет | нет |
> | Расположения | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |
> | vnfs | нет | нет |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |
> | Операции | нет | нет |
> | Планы | нет | нет |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | availablestacks | нет | нет |
> | billingmeters | нет | нет |
> | certificates | нет | Да |
> | checknameavailability | нет | нет |
> | connectiongateways | Да | Да |
> | connections | Да | Да |
> | customapis | Да | Да |
> | deletedsites | нет | нет |
> | deploymentlocations | нет | нет |
> | georegions | нет | нет |
> | hostingenvironments | нет | нет |
> | hostingenvironments / eventgridfilters | нет | нет |
> | hostingenvironments / multirolepools | нет | нет |
> | hostingenvironments / workerpools | нет | нет |
> | ishostingenvironmentnameavailable | нет | нет |
> | ishostnameavailable | нет | нет |
> | isusernameavailable | нет | нет |
> | kubeenvironments | Да | Да |
> | listsitesassignedtohostname | нет | нет |
> | Расположения | нет | нет |
> | locations / apioperations | нет | нет |
> | locations / connectiongatewayinstallations | нет | нет |
> | locations / deletedsites | нет | нет |
> | locations / deletevirtualnetworkorsubnets | нет | нет |
> | locations / extractapidefinitionfromwsdl | нет | нет |
> | locations / getnetworkpolicies | нет | нет |
> | locations / listwsdlinterfaces | нет | нет |
> | locations / managedapis | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operations | нет | нет |
> | locations / runtimes | нет | нет |
> | Операции | нет | нет |
> | publishingusers | нет | нет |
> | к просмотру фильмов | нет | нет |
> | resourcehealthmetadata | нет | нет |
> | runtimes | нет | нет |
> | serverfarms | Да | Да |
> | serverfarms / eventgridfilters | нет | нет |
> | sites | Да | Да |
> | sites / eventgridfilters | нет | нет |
> | sites / hostnamebindings | нет | нет |
> | sites / networkconfig | нет | нет |
> | sites / premieraddons | Да | Да |
> | sites / slots | Да | Да |
> | sites / slots / eventgridfilters | нет | нет |
> | sites / slots / hostnamebindings | нет | нет |
> | sites / slots / networkconfig | нет | нет |
> | sourcecontrols | нет | нет |
> | staticsites | нет | нет |
> | validate | нет | нет |
> | verifyhostingenvironmentvnet | нет | нет |

> [!IMPORTANT]
> См. статью [Руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / operationstatuses | нет | нет |
> | multipleactivationkeys | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | deviceservices | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | components | нет | нет |
> | componentssummary | нет | нет |
> | monitorinstances | нет | нет |
> | monitorinstancessummary | нет | нет |
> | monitors | нет | нет |
> | notificationsettings | нет | нет |
> | Операции | нет | нет |

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.

## <a name="next-steps"></a>Дальнейшие действия
Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](move-resource-group-and-subscription.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
