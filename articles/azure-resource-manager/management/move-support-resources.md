---
title: Поддержка операции перемещения типом ресурсов
description: Список типов ресурсов Azure, подлежащих перемещению в новую группу ресурсов или подписку.
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 7b7f6f05c9cae80cffb64245a3fc8a6b0890d577
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539233"
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
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
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
> - [Microsoft. Хибриднетворк](#microsofthybridnetwork)
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
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. Поверплатформ](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
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
> - [Microsoft. Ворклоадбуилдер](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | domainservices | Нет | Нет |
> | domainservices / oucontainer | Нет | Нет |
> | Расположения | нет | Нет |
> | locations / operationresults | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | Нет | Нет |
> | diagnosticsettingscategories | Нет | Нет |
> | Операции | нет | Нет |
> | privatelinkforazuread | Да | Да |
> | tenants | Да | Да |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | operationresults | нет | Нет |
> | Операции | нет | Нет |
> | supportproviders | Нет | Нет |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | нет | Нет |
> | addsservices | нет | Нет |
> | agents | нет | Нет |
> | anonymousapiusers | нет | Нет |
> | настройка | нет | Нет |
> | журналы | нет | Нет |
> | Операции | нет | Нет |
> | reports | нет | Нет |
> | servicehealthmetrics | нет | Нет |
> | services; | Нет | Нет |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | конфигурации | нет | Нет |
> | generaterecommendations | нет | Нет |
> | метаданные | нет | Нет |
> | Операции | нет | Нет |
> | к просмотру фильмов | нет | Нет |
> | suppressions | Нет | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | actionrules | Да | Да |
> | оповещения | нет | Нет |
> | alertslist | нет | Нет |
> | alertsmetadata | нет | Нет |
> | alertssummary | нет | Нет |
> | alertssummarylist | нет | Нет |
> | Операции | Нет | Нет |
> | smartdetectoralertrules | Да | Да |
> | smartgroups | Нет | Нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | Нет |
> | locations / checknameavailability | нет | Нет |
> | locations / operationresults | нет | Нет |
> | locations / operationstatuses | нет | Нет |
> | Операции | Нет | Нет |
> | servers | Да | Да |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | нет | Нет |
> | checknameavailability | нет | Нет |
> | checkservicenameavailability | нет | Нет |
> | Операции | нет | Нет |
> | reportfeedback | Нет | Нет |
> | служба | Да | Да |
> | validateservicename | Нет | Нет |

> [!IMPORTANT]
> Невозможно переместить службу управления API, для которой настроено значение "SKU потребления".

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | Нет |
> | configurationstores | Да | Да |
> | configurationstores / eventgridfilters | нет | Нет |
> | Расположения | нет | Нет |
> | locations / operationsstatus | нет | Нет |
> | Операции | Нет | Нет |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | Нет |
> | locations / checknameavailability | нет | Нет |
> | locations / operationresults | нет | Нет |
> | locations / operationstatus | нет | Нет |
> | Операции | Нет | Нет |
> | spring | Да | Да |
> | Пружина/приложения | нет | нет |
> | Пружина/приложения/развертывания | нет | нет |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | apiapps | Нет | Нет |
> | appidentities | Нет | Нет |
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
> | checkaccess | нет | Нет |
> | classicadministrators | нет | Нет |
> | dataaliases | нет | Нет |
> | denyassignments | нет | Нет |
> | elevateaccess | нет | Нет |
> | findorphanroleassignments | нет | Нет |
> | locks | нет | Нет |
> | Операции | нет | Нет |
> | operationstatus | нет | Нет |
> | разрешения | нет | Нет |
> | policyassignments | нет | Нет |
> | policydefinitions | нет | Нет |
> | policysetdefinitions | нет | Нет |
> | привателинкассоЦиатионс | Нет | Нет |
> | provideroperations | Нет | Нет |
> | ресаурцеманажементпривателинкс | Нет | Нет |
> | roleassignments | нет | нет |
> | roleassignmentsusagemetrics | нет | нет |
> | roledefinitions | нет | нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Да | Да |
> | automationaccounts / configurations | Да | Да |
> | automationaccounts / jobs | нет | Нет |
> | automationaccounts / privateendpointconnectionproxies | нет | Нет |
> | automationaccounts / privateendpointconnections | Нет | Нет |
> | automationaccounts / privatelinkresources | Нет | нет |
> | automationaccounts / runbooks | Да | Да |
> | automationaccounts / softwareupdateconfigurations | Нет | нет |
> | automationaccounts / webhooks | Нет | Нет |
> | Операции | Нет | нет |

> [!IMPORTANT]
> Модули Runbook должны находиться в той же группе ресурсов, что и учетная запись службы автоматизации.
>
> Дополнительные сведения см. [в статье Перемещение учетной записи службы автоматизации Azure в другую подписку](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | Нет |
> | locations / checkquotaavailability | нет | Нет |
> | расположения и чекктриалаваилабилити | нет | Нет |
> | Операции | нет | Нет |
> | privateclouds | Да | Да |
> | privateclouds / clusters | нет | нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Да | Да |
> | b2ctenants | нет | Нет |
> | checknameavailability | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Нет | нет |
> | hybriddatamanagers | Нет | Нет |
> | Операции | Нет | Нет |
> | postgresinstances | нет | Нет |
> | sqlinstances | нет | Нет |
> | sqlmanagedinstances | Нет | Нет |
> | sqlserverinstances | нет | Нет |
> | sqlserverregistrations | Да | Да |
> | sqlserverregistrations / sqlservers | Нет | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | нет | Нет |
> | Операции | Нет | Нет |
> | registrations | Да | Да |
> | registrations / customersubscriptions | нет | нет |
> | registrations / products | нет | нет |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | clusters | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Да | Да |
> | Расположения | нет | Нет |
> | locations / accountoperationresults | нет | Нет |
> | locations / checknameavailability | нет | Нет |
> | locations / quotas | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Нет | нет |
> | billingaccounts / agreements | Нет | Нет |
> | billingaccounts / billingpermissions | Нет | Нет |
> | billingaccounts / billingprofiles | нет | Нет |
> | billingaccounts / billingprofiles / availablebalance | нет | Нет |
> | billingaccounts / billingprofiles / billingpermissions | нет | Нет |
> | billingaccounts / billingprofiles / billingroleassignments | нет | Нет |
> | billingaccounts / billingprofiles / billingroledefinitions | нет | Нет |
> | billingaccounts / billingprofiles / billingsubscriptions | нет | Нет |
> | billingaccounts / billingprofiles / createbillingroleassignment | нет | Нет |
> | billingaccounts / billingprofiles / customers | нет | Нет |
> | billingaccounts / billingprofiles / instructions | нет | Нет |
> | billingaccounts / billingprofiles / invoices | нет | Нет |
> | billingaccounts / billingprofiles / invoices / pricesheet | нет | Нет |
> | billingaccounts / billingprofiles / invoices / transactions | нет | Нет |
> | billingaccounts / billingprofiles / invoicesections | нет | Нет |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | нет | Нет |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | нет | Нет |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | нет | Нет |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | нет | Нет |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | нет | Нет |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | нет | Нет |
> | billingaccounts / billingprofiles / invoicesections / products | нет | Нет |
> | billingaccounts / billingprofiles / invoicesections / products / transfer | нет | Нет |
> | billingaccounts / billingprofiles / invoicesections / products / updateautorenew | нет | Нет |
> | billingaccounts / billingprofiles / invoicesections / transactions | нет | Нет |
> | billingaccounts / billingprofiles / invoicesections / transfers | нет | Нет |
> | billingaccounts / billingprofiles / patchoperations | нет | Нет |
> | billingaccounts / billingprofiles / paymentmethods | нет | Нет |
> | billingaccounts / billingprofiles / policies | нет | Нет |
> | billingaccounts / billingprofiles / pricesheet | нет | Нет |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | нет | Нет |
> | billingaccounts / billingprofiles / products | нет | Нет |
> | billingaccounts / billingprofiles / transactions | нет | Нет |
> | billingaccounts / billingroleassignments | нет | Нет |
> | billingaccounts / billingroledefinitions | нет | Нет |
> | billingaccounts / billingsubscriptions | нет | Нет |
> | billingaccounts / billingsubscriptions / invoices | нет | Нет |
> | billingaccounts / createbillingroleassignment | нет | Нет |
> | billingaccounts / createinvoicesectionoperations | нет | Нет |
> | billingaccounts / customers | нет | Нет |
> | billingaccounts / customers / billingpermissions | нет | Нет |
> | billingaccounts / customers / billingsubscriptions | нет | Нет |
> | billingaccounts / customers / initiatetransfer | нет | Нет |
> | billingaccounts / customers / policies | нет | Нет |
> | billingaccounts / customers / products | нет | Нет |
> | billingaccounts / customers / transactions | нет | Нет |
> | billingaccounts / customers / transfers | нет | Нет |
> | billingaccounts / departments | нет | Нет |
> | billingaccounts / enrollmentaccounts | нет | Нет |
> | billingaccounts / invoices | нет | Нет |
> | billingaccounts / invoicesections | нет | Нет |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | нет | Нет |
> | billingaccounts / invoicesections / billingsubscriptions | нет | Нет |
> | billingaccounts / invoicesections / billingsubscriptions / transfer | нет | Нет |
> | billingaccounts / invoicesections / elevate | нет | Нет |
> | billingaccounts / invoicesections / initiatetransfer | нет | Нет |
> | billingaccounts / invoicesections / patchoperations | нет | Нет |
> | billingaccounts / invoicesections / productmoveoperations | нет | Нет |
> | billingaccounts / invoicesections / products | нет | Нет |
> | billingaccounts / invoicesections / products / transfer | нет | Нет |
> | billingaccounts / invoicesections / products / updateautorenew | нет | Нет |
> | billingaccounts / invoicesections / producttransfersresults | нет | Нет |
> | billingaccounts / invoicesections / transactions | нет | Нет |
> | billingaccounts / invoicesections / transfers | нет | Нет |
> | billingaccounts / lineofcredit | нет | Нет |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | нет | Нет |
> | billingaccounts / operationresults | нет | Нет |
> | billingaccounts / patchoperations | нет | Нет |
> | billingaccounts / paymentmethods | нет | Нет |
> | billingaccounts / products | нет | Нет |
> | billingaccounts / transactions | нет | Нет |
> | billingperiods | нет | Нет |
> | billingpermissions | нет | Нет |
> | billingproperty | нет | Нет |
> | billingroleassignments | нет | Нет |
> | billingroledefinitions | нет | нет |
> | createbillingroleassignment | нет | Нет |
> | departments | нет | Нет |
> | enrollmentaccounts | нет | Нет |
> | invoices | нет | Нет |
> | operationresults | нет | Нет |
> | Операции | нет | Нет |
> | operationstatus | нет | Нет |
> | transfers | нет | Нет |
> | transfers / accepttransfer | нет | Нет |
> | transfers / declinetransfer | нет | Нет |
> | transfers / operationstatus | нет | Нет |
> | transfers / validatetransfer | нет | Нет |
> | validateaddress | нет | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | Нет | нет |
> | mapapis | Нет | Нет |
> | Операции | Нет | Нет |
> | updatecommunicationpreference | нет | Нет |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | biztalk | Нет | нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Нет | нет |
> | cordamembers | Нет | Нет |
> | Расположения | Нет | Нет |
> | locations / blockchainmemberoperationresults | нет | Нет |
> | locations / checknameavailability | нет | Нет |
> | locations / listconsortiums | нет | Нет |
> | locations / watcheroperationresults | нет | Нет |
> | Операции | нет | Нет |
> | watchers | нет | Нет |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | Нет | нет |
> | tokenservices | Нет | Нет |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Нет | нет |
> | blueprintassignments / assignmentoperations | Нет | Нет |
> | blueprintassignments / operations | Нет | Нет |
> | blueprints | нет | Нет |
> | blueprints / artifacts | нет | Нет |
> | blueprints / versions | нет | Нет |
> | blueprints / versions / artifacts | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | botservices | Да | Да |
> | botservices / channels | Нет | Нет |
> | botservices / connections | Нет | Нет |
> | checknameavailability | Нет | Нет |
> | listauthserviceproviders | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | нет |
> | Расположения | Нет | Нет |
> | locations / operationresults | Нет | Нет |
> | locations / operationsstatus | нет | Нет |
> | Операции | нет | Нет |
> | redis | Да | Да |
> | Redis/евентгридфилтерс | нет | Нет |
> | redis / privatelinkresources | нет | Нет |
> | redisenterprise | Нет | Нет |

> [!IMPORTANT]
> Если экземпляр кэша Azure для Redis настроен с помощью виртуальной сети, его нельзя переместить в другую подписку. См. [ограничения перемещения сети](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | appliedreservations | нет | Нет |
> | calculateexchange | Нет | Нет |
> | calculateprice | Нет | Нет |
> | calculatepurchaseprice | нет | Нет |
> | catalogs | нет | Нет |
> | checkoffers | нет | Нет |
> | checkpurchasestatus | нет | Нет |
> | checkscopes | нет | Нет |
> | commercialreservationorders | нет | Нет |
> | обмен валюты | нет | Нет |
> | listbenefits | нет | Нет |
> | Операции | нет | Нет |
> | placepurchaseorder | нет | Нет |
> | reservationorders | нет | Нет |
> | reservationorders / availablescopes | нет | Нет |
> | reservationorders / calculaterefund | нет | Нет |
> | reservationorders / merge | нет | Нет |
> | reservationorders / reservations | нет | Нет |
> | reservationorders / reservations / availablescopes | нет | Нет |
> | reservationorders / reservations / revisions | нет | Нет |
> | reservationorders / return | нет | Нет |
> | reservationorders / split | нет | Нет |
> | reservationorders / swap | нет | Нет |
> | reservations | нет | Нет |
> | ресурсов | нет | Нет |
> | validatereservationorder | нет | Нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Нет | нет |
> | cdnwebapplicationfirewallpolicies | Да | Да |
> | checknameavailability | Нет | Нет |
> | checkresourceusage | нет | Нет |
> | edgenodes | Нет | Нет |
> | operationresults | нет | Нет |
> | operationresults / profileresults | нет | Нет |
> | operationresults / profileresults / endpointresults | нет | Нет |
> | operationresults / profileresults / endpointresults / customdomainresults | нет | Нет |
> | operationresults / profileresults / endpointresults / origingroupresults | нет | Нет |
> | operationresults / profileresults / endpointresults / originresults | нет | Нет |
> | Операции | нет | Нет |
> | профили | Да | Да |
> | profiles / endpoints | Да | Да |
> | profiles / endpoints / customdomains | нет | Нет |
> | profiles / endpoints / origingroups | Нет | Нет |
> | profiles / endpoints / origins | Нет | Нет |
> | validateprobe | нет | Нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | certificateorders | Да | Да |
> | certificateorders / certificates | Нет | Нет |
> | Операции | Нет | Нет |
> | validatecertificateregistrationinformation | Нет | Нет |

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
> | capabilities | Нет | нет |
> | checkdomainnameavailability | Нет | Нет |
> | domainnames | Да | Нет |
> | domainnames / capabilities | нет | Нет |
> | domainnames / internalloadbalancers | нет | Нет |
> | domainnames / servicecertificates | нет | Нет |
> | domainnames / slots | нет | Нет |
> | domainnames / slots / roles | нет | Нет |
> | domainnames / slots / roles / metricdefinitions | нет | Нет |
> | domainnames / slots / roles / metrics | нет | Нет |
> | movesubscriptionresources | нет | Нет |
> | operatingsystemfamilies | нет | Нет |
> | operatingsystems | нет | Нет |
> | Операции | нет | Нет |
> | operationstatuses | нет | Нет |
> | quotas | нет | Нет |
> | resourcetypes | нет | Нет |
> | validatesubscriptionmoveavailability | нет | Нет |
> | virtualmachines | Да | Нет |
> | virtualmachines / diagnosticsettings | нет | Нет |
> | virtualmachines / metricdefinitions | нет | Нет |
> | virtualmachines / metrics | нет | Нет |

> [!IMPORTANT]
> См. [Руководство по перемещению классического развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | нет | Нет |

> [!IMPORTANT]
> См. [Руководство по перемещению классического развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | capabilities | нет | Нет |
> | expressroutecrossconnections | Нет | Нет |
> | expressroutecrossconnections / peerings | Нет | Нет |
> | gatewaysupporteddevices | нет | Нет |
> | networksecuritygroups | нет | Нет |
> | Операции | нет | Нет |
> | quotas | нет | Нет |
> | reservedips | нет | Нет |
> | virtualnetworks | нет | Нет |
> | virtualnetworks / remotevirtualnetworkpeeringproxies | нет | Нет |
> | virtualnetworks / virtualnetworkpeerings | нет | Нет |

> [!IMPORTANT]
> См. [Руководство по перемещению классического развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | capabilities | нет | Нет |
> | checkstorageaccountavailability | Нет | Нет |
> | disks | Нет | Нет |
> | images | нет | Нет |
> | Операции | нет | Нет |
> | osimages | нет | Нет |
> | osplatformimages | нет | Нет |
> | publicimages | нет | Нет |
> | quotas | нет | Нет |
> | storageaccounts | Да | Нет |
> | storageaccounts / blobservices | нет | Нет |
> | storageaccounts / fileservices | нет | Нет |
> | storageaccounts / metricdefinitions | нет | Нет |
> | storageaccounts / metrics | нет | Нет |
> | storageaccounts / queueservices | нет | Нет |
> | storageaccounts / services | нет | Нет |
> | storageaccounts / services / diagnosticsettings | нет | Нет |
> | storageaccounts / services / metricdefinitions | нет | Нет |
> | storageaccounts / services / metrics | нет | Нет |
> | storageaccounts / tableservices | нет | Нет |
> | storageaccounts / vmimages | нет | Нет |
> | vmimages | нет | Нет |

> [!IMPORTANT]
> См. [Руководство по перемещению классического развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | нет | Нет |

> [!IMPORTANT]
> См. [Руководство по перемещению классического развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |
> | checkdomainavailability | Нет | Нет |
> | Расположения | Нет | Нет |
> | locations / checkskuavailability | нет | Нет |
> | locations / deletevirtualnetworkorsubnets | Нет | Нет |
> | locations / operationresults | Нет | Нет |
> | Операции | нет | нет |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | нет | Нет |
> | ratecard | нет | Нет |
> | usageaggregates | нет | Нет |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Да | Да |
> | diskaccesses | нет | Нет |
> | diskencryptionsets | нет | Нет |
> | disks | Да | Да |
> | galleries | Нет | Нет |
> | galleries / images | Нет | Нет |
> | galleries / images / versions | нет | нет |
> | hostgroups | нет | нет |
> | hostgroups / hosts | Нет | Нет |
> | images | Да | Да |
> | Расположения | нет | нет |
> | locations / artifactpublishers | нет | нет |
> | locations / capsoperations | нет | нет |
> | locations / diskoperations | нет | нет |
> | locations / loganalytics | Нет | Нет |
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
> | sharedvmimages / versions | Нет | Нет |
> | snapshots | Да | Да |
> | sshpublickeys | нет | нет |
> | virtualmachines | Да | Да |
> | virtualmachines / extensions | Да | Да |
> | virtualmachines / metricdefinitions | нет | нет |
> | virtualmachines / runcommands | Нет | Нет |
> | virtualmachinescalesets | Да | Да |
> | virtualmachinescalesets / extensions | Нет | Нет |
> | virtualmachinescalesets / networkinterfaces | Нет | Нет |
> | virtualmachinescalesets / publicipaddresses | нет | нет |
> | virtualmachinescalesets / virtualmachines | нет | нет |
> | virtualmachinescalesets / virtualmachines / networkinterfaces | Нет | Нет |

> [!IMPORTANT]
> См. [Руководство по перемещению виртуальных машин](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Нет | нет |
> | balances | Нет | нет |
> | budgets | Нет | нет |
> | charges | Нет | Нет |
> | costtags | Нет | Нет |
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

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | containergroups | нет | Нет |
> | Расположения | нет | Нет |
> | locations / cachedimages | нет | Нет |
> | locations / capabilities | нет | Нет |
> | locations / deletevirtualnetworkorsubnets | Нет | Нет |
> | locations / operations | нет | нет |
> | locations / usages | Нет | Нет |
> | Операции | Нет | нет |
> | serviceassociationlinks | Нет | нет |

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
> | Операции | Нет | Нет |
> | registries | Да | Да |
> | registries / agentpools | Да | Да |
> | registries / agentpools / listqueuestatus | Нет | нет |
> | registries / builds | Нет | нет |
> | registries / builds / cancel | Нет | нет |
> | registries / builds / getloglink | Нет | нет |
> | registries / buildtasks | Да | Да |
> | registries / buildtasks / listsourcerepositoryproperties | Нет | Нет |
> | registries / buildtasks / steps | Нет | Нет |
> | registries / buildtasks / steps / listbuildarguments | Нет | нет |
> | registries / eventgridfilters | Нет | нет |
> | registries / exportpipelines | Нет | нет |
> | registries / generatecredentials | Нет | нет |
> | registries / getbuildsourceuploadurl | Нет | Нет |
> | registries / getcredentials | Нет | нет |
> | registries / importimage | Нет | нет |
> | registries / importpipelines | Нет | нет |
> | registries / listbuildsourceuploadurl | Нет | нет |
> | registries / listcredentials | Нет | нет |
> | registries / listpolicies | Нет | нет |
> | registries / listusages | Нет | нет |
> | registries / pipelineruns | Нет | нет |
> | registries / privateendpointconnectionproxies | Нет | нет |
> | registries / privateendpointconnectionproxies / validate | Нет | нет |
> | registries / privateendpointconnections | Нет | нет |
> | registries / privatelinkresources | Нет | нет |
> | registries / queuebuild | Нет | нет |
> | registries / regeneratecredential | Нет | нет |
> | registries / regeneratecredentials | Нет | нет |
> | registries / replications | Да | Да |
> | registries / runs | Нет | нет |
> | registries / runs / cancel | Нет | нет |
> | registries / runs / listlogsasurl | Нет | нет |
> | registries / schedulerun | Нет | нет |
> | registries / scopemaps | Нет | нет |
> | registries / taskruns | Нет | нет |
> | registries / taskruns / listdetails | Нет | Нет |
> | registries / tasks | Да | Да |
> | registries / tasks / listdetails | Нет | нет |
> | registries / tokens | Нет | нет |
> | registries / updatepolicies | Нет | нет |
> | registries / webhooks | Да | Да |
> | registries / webhooks / getcallbackconfig | Нет | нет |
> | registries / webhooks / listevents | Нет | нет |
> | registries / webhooks / ping | Нет | Нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | containerservices | нет | нет |
> | Расположения | нет | нет |
> | locations / openshiftclusters | Нет | Нет |
> | locations / operationresults | нет | нет |
> | locations / operations | нет | нет |
> | locations / orchestrators | нет | нет |
> | managedclusters | Нет | Нет |
> | openshiftmanagedclusters | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | нет | нет |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | Нет |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | оповещения | Нет | нет |
> | billingaccounts | Нет | Нет |
> | budgets | Нет | Нет |
> | cloudconnectors | нет | Нет |
> | соединители | Да | Да |
> | departments | нет | нет |
> | dimensions | Нет | Нет |
> | enrollmentaccounts | Нет | нет |
> | exports | Нет | нет |
> | externalbillingaccounts | Нет | нет |
> | externalbillingaccounts / alerts | Нет | нет |
> | externalbillingaccounts / dimensions | Нет | Нет |
> | externalbillingaccounts / forecast | Нет | нет |
> | externalbillingaccounts / query | Нет | нет |
> | externalsubscriptions | Нет | нет |
> | externalsubscriptions / alerts | Нет | нет |
> | externalsubscriptions / dimensions | Нет | нет |
> | externalsubscriptions / forecast | Нет | нет |
> | externalsubscriptions / query | Нет | нет |
> | прогноз | Нет | нет |
> | Операции | Нет | нет |
> | query | Нет | нет |
> | регистрация | Нет | нет |
> | reportconfigs | Нет | нет |
> | reports | Нет | нет |
> | Параметры | Нет | нет |
> | showbackrules | Нет | нет |
> | узел "Представления" | Нет | нет |

## <a name="microsoftcostmanagementexports"></a>Microsoft.CostManagementExports

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | нет | нет |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | hubs | нет | Нет |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | Нет | нет |
> | requests | Нет | Нет |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | associations | Нет | Нет |
> | Расположения | Нет | Нет |
> | locations / operationstatuses | нет | Нет |
> | Операции | Нет | Нет |
> | resourceproviders | Да | Да |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | jobs | Нет | Нет |
> | Расположения | нет | нет |
> | locations / availableskus | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / regionconfiguration | Нет | Нет |
> | locations / validateaddress | Нет | Нет |
> | locations / validateinputs | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | аваилаблескус | нет | нет |
> | databoxedgedevices | Да | Да |
> | databoxedgedevices / checknameavailability | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | Нет |
> | locations / getnetworkpolicies | Нет | Нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |
> | workspaces | нет | нет |
> | workspaces / dbworkspaces | Нет | Нет |
> | workspaces / virtualnetworkpeerings | Нет | нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | catalogs | Да | Да |
> | checknameavailability | нет | нет |
> | datacatalogs | нет | нет |
> | Расположения | нет | нет |
> | locations / jobs | нет | нет |
> | locations / operationresults | Нет | Нет |
> | Операции | Нет | Нет |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | нет | нет |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Пакеты | нет | Нет |
> | Планы | нет | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynameavailability | Нет | Нет |
> | checkdatafactorynameavailability | нет | Нет |
> | datafactories | Да | Да |
> | datafactories / diagnosticsettings | нет | нет |
> | datafactories / metricdefinitions | нет | нет |
> | datafactoryschema | Нет | Нет |
> | factories | Да | Да |
> | factories / integrationruntimes | Нет | нет |
> | Расположения | Нет | Нет |
> | locations / configurefactoryrepo | Нет | нет |
> | locations / getfeaturevalue | Нет | нет |
> | Операции | Нет | нет |

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
> | accounts / datalakestoreaccounts | нет | Нет |
> | accounts / storageaccounts | нет | Нет |
> | accounts / storageaccounts / containers | Нет | Нет |
> | accounts / storageaccounts / containers / listsastokens | нет | нет |
> | Расположения | Нет | Нет |
> | locations / capability | Нет | Нет |
> | locations / checknameavailability | Нет | нет |
> | locations / operationresults | Нет | нет |
> | locations / usages | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |
> | accounts / eventgridfilters | нет | нет |
> | accounts / firewallrules | нет | нет |
> | Расположения | нет | нет |
> | locations / capability | нет | нет |
> | locations / checknameavailability | Нет | Нет |
> | locations / deletevirtualnetworkorsubnets | Нет | Нет |
> | locations / operationresults | Нет | нет |
> | locations / usages | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |
> | services; | Нет | Нет |
> | services / projects | Нет | нет |
> | slots | Нет | нет |

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
> | accounts / shares | Нет | Нет |
> | accounts / shares / datasets | нет | нет |
> | accounts / shares / invitations | нет | нет |
> | accounts / shares / providersharesubscriptions | нет | нет |
> | accounts / shares / synchronizationsettings | Нет | Нет |
> | accounts / sharesubscriptions | Нет | Нет |
> | accounts / sharesubscriptions / consumersourcedatasets | Нет | нет |
> | accounts / sharesubscriptions / datasetmappings | Нет | нет |
> | accounts / sharesubscriptions / triggers | Нет | нет |
> | listinvitations | Нет | нет |
> | Расположения | Нет | нет |
> | locations / consumerinvitations | Нет | нет |
> | locations / operationresults | Нет | нет |
> | locations / rejectinvitation | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | Расположения | нет | нет |
> | locations / azureasyncoperation | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / performancetiers | нет | нет |
> | locations / privateendpointconnectionazureasyncoperation | Нет | Нет |
> | locations / privateendpointconnectionoperationresults | Нет | нет |
> | locations / privateendpointconnectionproxyazureasyncoperation | Нет | нет |
> | locations / privateendpointconnectionproxyoperationresults | Нет | нет |
> | locations / recommendedactionsessionsazureasyncoperation | Нет | нет |
> | locations / recommendedactionsessionsoperationresults | Нет | нет |
> | locations / securityalertpoliciesazureasyncoperation | Нет | нет |
> | locations / securityalertpoliciesoperationresults | Нет | нет |
> | locations / serverkeyazureasyncoperation | Нет | нет |
> | locations / serverkeyoperationresults | Нет | нет |
> | Операции | Нет | нет |
> | servers | Да | Да |
> | servers / advisors | Нет | нет |
> | servers / privateendpointconnectionproxies | Нет | нет |
> | servers / privateendpointconnections | Нет | нет |
> | servers / privatelinkresources | Нет | нет |
> | servers / querytexts | Нет | нет |
> | servers / recoverableservers | Нет | Нет |
> | servers / topquerystatistics | Нет | нет |
> | servers / virtualnetworkrules | Нет | нет |
> | servers / waitstatistics | Нет | нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | Расположения | нет | нет |
> | locations / administratorazureasyncoperation | нет | нет |
> | locations / administratoroperationresults | нет | нет |
> | locations / azureasyncoperation | нет | нет |
> | locations / operationresults | Нет | Нет |
> | locations / performancetiers | Нет | нет |
> | locations / privateendpointconnectionazureasyncoperation | Нет | нет |
> | locations / privateendpointconnectionoperationresults | Нет | нет |
> | locations / privateendpointconnectionproxyazureasyncoperation | Нет | нет |
> | locations / privateendpointconnectionproxyoperationresults | Нет | нет |
> | locations / recommendedactionsessionsazureasyncoperation | Нет | нет |
> | locations / recommendedactionsessionsoperationresults | Нет | нет |
> | locations / securityalertpoliciesazureasyncoperation | Нет | нет |
> | locations / securityalertpoliciesoperationresults | Нет | нет |
> | locations / serverkeyazureasyncoperation | Нет | нет |
> | locations / serverkeyoperationresults | Нет | нет |
> | Операции | Нет | нет |
> | servers | Да | Да |
> | servers / advisors | Нет | нет |
> | servers / keys | Нет | нет |
> | servers / privateendpointconnectionproxies | Нет | нет |
> | servers / privateendpointconnections | Нет | нет |
> | servers / privatelinkresources | Нет | нет |
> | servers / querytexts | Нет | Нет |
> | servers / recoverableservers | Нет | нет |
> | servers / topquerystatistics | Нет | нет |
> | servers / virtualnetworkrules | Нет | нет |
> | servers / waitstatistics | Нет | нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | Расположения | нет | нет |
> | locations / administratorazureasyncoperation | нет | нет |
> | locations / administratoroperationresults | нет | нет |
> | locations / azureasyncoperation | нет | нет |
> | locations / operationresults | Нет | Нет |
> | locations / performancetiers | Нет | нет |
> | locations / privateendpointconnectionazureasyncoperation | Нет | нет |
> | locations / privateendpointconnectionoperationresults | Нет | нет |
> | locations / privateendpointconnectionproxyazureasyncoperation | Нет | нет |
> | locations / privateendpointconnectionproxyoperationresults | Нет | нет |
> | locations / recommendedactionsessionsazureasyncoperation | Нет | нет |
> | locations / recommendedactionsessionsoperationresults | Нет | нет |
> | locations / securityalertpoliciesazureasyncoperation | Нет | нет |
> | locations / securityalertpoliciesoperationresults | Нет | нет |
> | locations / serverkeyazureasyncoperation | Нет | нет |
> | locations / serverkeyoperationresults | Нет | нет |
> | Операции | Нет | нет |
> | servergroups | Нет | нет |
> | servers | Да | Да |
> | servers / advisors | Нет | нет |
> | servers / keys | Нет | нет |
> | servers / privateendpointconnectionproxies | Нет | нет |
> | servers / privateendpointconnections | Нет | нет |
> | servers / privatelinkresources | Нет | нет |
> | servers / querytexts | Нет | Нет |
> | servers / recoverableservers | Нет | нет |
> | servers / topquerystatistics | Нет | нет |
> | servers / virtualnetworkrules | Нет | нет |
> | servers / waitstatistics | Нет | нет |
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
> | applicationgroups / applications | Нет | Нет |
> | applicationgroups / desktops | Нет | Нет |
> | applicationgroups / startmenuitems | Нет | Нет |
> | hostpools | Да | Да |
> | hostpools / sessionhosts | Нет | Нет |
> | hostpools / sessionhosts / usersessions | Нет | Нет |
> | hostpools / usersessions | Нет | нет |
> | Операции | Нет | нет |
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
> | iothubs / eventgridfilters | Нет | Нет |
> | iothubs/SecuritySettings | Нет | нет |
> | operationresults | Нет | нет |
> | Операции | Нет | нет |
> | provisioningservices | Да | Да |
> | usages | Нет | Нет |

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
> | controllers / listconnectiondetails | нет | Нет |
> | Расположения | Нет | Нет |
> | locations / checkcontainerhostmapping | Нет | Нет |
> | locations / operationresults | Нет | Нет |
> | Операции | Нет | Нет |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | labcenters | нет | нет |
> | labs | Да | нет |
> | labs / environments | Да | Да |
> | labs / servicerunners | Да | Да |
> | labs / virtualmachines | Да | Нет |
> | Расположения | Нет | нет |
> | locations / operations | Нет | Нет |
> | Операции | Нет | Нет |
> | schedules | Да | Да |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | нет | нет |
> | дигиталтвинсинстанцес/оператионресултс | нет | нет |
> | Расположения | нет | нет |
> | Операции | Нет | Нет |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | нет | нет |
> | databaseaccounts | Да | Да |
> | Расположения | нет | нет |
> | locations / deletevirtualnetworkorsubnets | Нет | Нет |
> | locations / operationresults | Нет | нет |
> | locations / operationsstatus | Нет | Нет |
> | operationresults | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | нет | нет |
> | domains | Да | Да |
> | domains / domainownershipidentifiers | нет | нет |
> | generatessorequest | Нет | Нет |
> | listdomainrecommendations | Нет | нет |
> | Операции | Нет | Нет |
> | topleveldomains | Нет | нет |
> | validatedomainregistrationinformation | Нет | нет |

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
> | domains / topics | нет | нет |
> | eventsubscriptions | Нет — нельзя перемещать независимо, но автоматически перемещается вместе с подписанным ресурсом. | Нет — нельзя перемещать независимо, но автоматически перемещается вместе с подписанным ресурсом. |
> | extensiontopics | Нет | Нет |
> | Расположения | Нет | Нет |
> | locations / eventsubscriptions | Нет | Нет |
> | locations / operationresults | Нет | нет |
> | locations / operationsstatus | Нет | Нет |
> | locations / topictypes | Нет | нет |
> | operationresults | Нет | нет |
> | Операции | Нет | нет |
> | operationsstatus | Нет | нет |
> | partnernamespaces | Да | Да |
> | partnernamespaces / eventchannels | Нет | нет |
> | partnerregistrations | Нет | нет |
> | partnertopics | Да | Да |
> | partnertopics / eventsubscriptions | Нет | нет |
> | systemtopics | Да | Да |
> | systemtopics / eventsubscriptions | Нет | нет |
> | topics | Да | Да |
> | topictypes | Нет | Нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | Нет | Нет |
> | checknameavailability | нет | нет |
> | checknamespaceavailability | Нет | Нет |
> | clusters | Да | Да |
> | Расположения | Нет | Нет |
> | locations / deletevirtualnetworkorsubnets | Нет | нет |
> | пространства имен | Да | Да |
> | namespaces / authorizationrules | Нет | нет |
> | namespaces / disasterrecoveryconfigs | Нет | Нет |
> | namespaces / disasterrecoveryconfigs / checknameavailability | Нет | нет |
> | namespaces / eventhubs | Нет | нет |
> | namespaces / eventhubs / authorizationrules | Нет | Нет |
> | namespaces / eventhubs / consumergroups | Нет | нет |
> | namespaces / networkrulesets | Нет | нет |
> | Операции | Нет | нет |
> | sku | Нет | нет |

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
> | featureproviders | нет | Нет |
> | features | нет | Нет |
> | Операции | Нет | Нет |
> | providers | Нет | Нет |
> | subscriptionfeatureregistrations | Нет | Нет |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | нет | Нет |
> | automanagedvmconfigurationprofiles | нет | Нет |
> | guestconfigurationassignments | Нет | Нет |
> | Операции | нет | нет |
> | software | Нет | Нет |
> | softwareupdateprofile | Нет | нет |
> | softwareupdates | Нет | нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | hanainstances | нет | нет |
> | Расположения | нет | нет |
> | locations / operations | нет | нет |
> | locations / operationsstatus | нет | нет |
> | Операции | Нет | Нет |
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
> | locations / azureasyncoperations | Нет | Нет |
> | locations / billingspecs | Нет | Нет |
> | locations / capabilities | Нет | нет |
> | locations / operationresults | Нет | нет |
> | locations / usages | Нет | нет |
> | locations / validatecreaterequest | Нет | нет |
> | Операции | Нет | нет |

> [!IMPORTANT]
> Кластеры HDInsight можно переместить в новую подписку или группу ресурсов. Однако перемещение между подписками недоступно для сетевых ресурсов, связанных с кластером HDInsight (таких как виртуальная сеть, сетевой адаптер или подсистема балансировки нагрузки). Также невозможно переместить в новую группу ресурсов сетевой адаптер, который подключен к виртуальной машине кластера.
>
> При перемещении кластера HDInsight в новую подписку сначала необходимо переместить другие ресурсы (такие как учетная запись хранения). А затем переместить сам кластер HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | нет |
> | Расположения | Нет | нет |
> | locations / operationresults | Нет | Нет |
> | Операции | Нет | Нет |
> | services; | Да | Да |
> | services / privateendpointconnections | Нет | нет |
> | services / privatelinkresources | Нет | нет |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / operationresults | Нет | Нет |
> | locations / operationstatus | нет | нет |
> | machines | Да | Да |
> | machines / extensions | Да | Да |
> | Операции | Нет | Нет |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | datamanagers | Да | Да |
> | Операции | Нет | Нет |

## <a name="microsofthybridnetwork"></a>Microsoft. Хибриднетворк

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | устройства | нет | нет |
> | Расположения | Нет | Нет |
> | locations / operationstatuses | Нет | нет |
> | Операции | Нет | нет |
> | vnfs | Нет | нет |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | components | Нет | Нет |
> | Расположения | Нет | Нет |
> | networkscopes | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | jobs | Да | Да |
> | Расположения | Нет | Нет |
> | locations / operationresults | Нет | нет |
> | Операции | Нет | Нет |

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
> | компоненты и метаданные | нет | нет |
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
> | privatelinkscopes | Нет | Нет |
> | privatelinkscopes / privateendpointconnectionproxies | нет | нет |
> | privatelinkscopes / privateendpointconnections | нет | нет |
> | privatelinkscopes / scopedresources | нет | нет |
> | rollbacktolegacypricingmodel | Нет | Нет |
> | scheduledqueryrules | Да | Да |
> | Топология | Нет | Нет |
> | транзакции | Нет | Нет |
> | vminsightsonboardingstatuses | Нет | Нет |
> | webtests | Да | Да |
> | веб-тесты и жеттестресултфиле | нет | Нет |
> | workbooks | Да | Да |
> | workbooktemplates | Да | Да |

> [!IMPORTANT]
> Убедитесь, что переход на новую подписку не превышает [квоты для подписки](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | apptemplates | Нет | Нет |
> | checknameavailability | Нет | Нет |
> | checksubdomainavailability | Нет | нет |
> | iotapps | Да | Да |
> | Операции | Нет | Нет |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Да | Да |
> | graph | Да | Да |
> | Операции | Нет | нет |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | deletedvaults | нет | нет |
> | hsmpools | Нет | Нет |
> | Расположения | нет | нет |
> | locations / deletedvaults | нет | нет |
> | locations / deletevirtualnetworkorsubnets | нет | нет |
> | locations / operationresults | Нет | Нет |
> | манажедхсмс | Нет | Нет |
> | Операции | Нет | Нет |
> | vaults | Да | Да |
> | vaults / accesspolicies | нет | Нет |
> | vaults / eventgridfilters | нет | Нет |
> | vaults / secrets | нет | Нет |

> [!IMPORTANT]
> Хранилища ключей, используемые для шифрования дисков, невозможно переместить в группы ресурсов в той же подписке или между подписками.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Да | Да |
> | Расположения | Нет | Нет |
> | locations / operationstatuses | Нет | Нет |
> | Операции | нет | Нет |
> | registeredsubscriptions | нет | Нет |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Нет | нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | clusters | Да | Да |
> | clusters / attacheddatabaseconfigurations | нет | нет |
> | clusters / databases | нет | нет |
> | clusters / databases / dataconnections | нет | нет |
> | clusters / databases / eventhubconnections | Нет | Нет |
> | clusters / databases / principalassignments | Нет | нет |
> | clusters / principalassignments | Нет | нет |
> | Расположения | Нет | нет |
> | locations / checknameavailability | Нет | нет |
> | locations / operationresults | Нет | нет |
> | Операции | Нет | Нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | labaccounts | Нет | Нет |
> | Расположения | Нет | Нет |
> | locations / operations | нет | Нет |
> | Операции | Нет | Нет |
> | users | нет | нет |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | Нет |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | нет | нет |
> | integrationaccounts | Да | Да |
> | integrationserviceenvironments | Да | Нет |
> | integrationserviceenvironments / managedapis | Да | нет |
> | isolatedenvironments | Нет | нет |
> | Расположения | Нет | нет |
> | locations / workflows | Нет | нет |
> | Операции | Нет | нет |
> | workflows | Да | Да |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Нет | Нет |
> | Расположения | Нет | нет |
> | locations / operations | Нет | Нет |
> | locations / operationsstatus | Нет | Нет |
> | Операции | нет | Нет |
> | webservices | Да | Нет |
> | workspaces | Да | Да |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Нет | нет |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | нет |
> | accounts / workspaces | Нет | Нет |
> | accounts / workspaces / projects | Нет | Нет |
> | teamaccounts | нет | Нет |
> | teamaccounts / workspaces | Нет | Нет |
> | teamaccounts / workspaces / projects | нет | нет |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | Нет |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | нет |
> | locations / computeoperationsstatus | Нет | нет |
> | locations / quotas | Нет | нет |
> | locations / updatequotas | Нет | нет |
> | locations / usages | Нет | нет |
> | locations / vmsizes | Нет | нет |
> | locations / workspaceoperationsstatus | Нет | Нет |
> | Операции | Нет | Нет |
> | workspaces | нет | Нет |
> | workspaces / computes | нет | Нет |
> | workspaces / eventgridfilters | Нет | Нет |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | applyupdates | Нет | Нет |
> | configurationassignments | нет | Нет |
> | maintenanceconfigurations | Да | Да |
> | updates | нет | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | удостоверения; | нет | Нет |
> | Операции | нет | Нет |
> | userassignedidentities | нет | Нет |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | managednetworks | Нет | Нет |
> | managednetworks / managednetworkgroups | нет | Нет |
> | managednetworks / managednetworkpeeringpolicies | нет | Нет |
> | уведомление | нет | Нет |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | нет | Нет |
> | Операции | Нет | Нет |
> | operationstatuses | нет | Нет |
> | registrationassignments | нет | Нет |
> | registrationdefinitions | нет | Нет |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | нет |
> | getentities | Нет | нет |
> | managementgroups | Нет | нет |
> | managementgroups / settings | Нет | нет |
> | operationresults | Нет | нет |
> | operationresults / asyncoperation | Нет | Нет |
> | Операции | Нет | Нет |
> | ресурсов | Нет | Нет |
> | starttenantbackfill | нет | Нет |
> | tenantbackfillstatus | Нет | Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |
> | accounts / eventgridfilters | нет | Нет |
> | accounts / privateatlases | Да | Да |
> | Операции | нет | Нет |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | Нет | нет |
> | offers | Нет | нет |
> | offertypes | Нет | нет |
> | offertypes / publishers | Нет | нет |
> | offertypes / publishers / offers | Нет | нет |
> | offertypes / publishers / offers / plans | Нет | нет |
> | offertypes / publishers / offers / plans / agreements | Нет | нет |
> | offertypes / publishers / offers / plans / configs | Нет | нет |
> | offertypes / publishers / offers / plans / configs / importimage | Нет | нет |
> | Операции | Нет | нет |
> | privategalleryitems | Нет | нет |
> | privatestoreclient | Нет | нет |
> | privatestores | Нет | нет |
> | privatestores / offers | Нет | нет |
> | products | Нет | Нет |
> | publishers | Нет | Нет |
> | publishers / offers | нет | Нет |
> | publishers / offers / amendments | нет | Нет |
> | регистрация | нет | Нет |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Нет | Нет |
> | listcommunicationpreference | нет | Нет |
> | Операции | нет | Нет |
> | updatecommunicationpreference | нет | Нет |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | agreements | нет | Нет |
> | offertypes | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | нет |
> | Расположения | Нет | нет |
> | locations / checknameavailability | Нет | нет |
> | mediaservices | Да | Да |
> | mediaservices / accountfilters | Нет | нет |
> | mediaservices / assets | Нет | Нет |
> | mediaservices / assets / assetfilters | Нет | нет |
> | mediaservices / contentkeypolicies | Нет | нет |
> | mediaservices / eventgridfilters | Нет | нет |
> | mediaservices / liveeventoperations | Нет | Нет |
> | mediaservices / liveevents | Да | Да |
> | mediaservices / liveevents / liveoutputs | Нет | нет |
> | mediaservices / liveoutputoperations | Нет | нет |
> | mediaservices / streamingendpointoperations | Нет | нет |
> | mediaservices / streamingendpoints | Да | Да |
> | mediaservices / streaminglocators | Нет | Нет |
> | mediaservices / streamingpolicies | Нет | Нет |
> | mediaservices / transforms | нет | Нет |
> | mediaservices / transforms / jobs | Нет | Нет |
> | Операции | Нет | Нет |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | appclusters | нет | Нет |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Нет | нет |
> | Расположения | Нет | нет |
> | locations / assessmentoptions | Нет | нет |
> | locations / checknameavailability | Нет | Нет |
> | migrateprojects | Нет | Нет |
> | movecollections | нет | Нет |
> | Операции | нет | Нет |
> | projects | нет | Нет |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Нет | Нет |
> | Расположения | Нет | Нет |
> | locations / checknameavailability | Нет | Нет |
> | objectunderstandingaccounts | Нет | Нет |
> | Операции | нет | Нет |
> | remoterenderingaccounts | Да | Да |
> | spatialanchorsaccounts | Да | Да |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Нет | нет |
> | netappaccounts / backuppolicies | Нет | нет |
> | netappaccounts / capacitypools | Нет | Нет |
> | netappaccounts / capacitypools / volumes | Нет | Нет |
> | netappaccounts / capacitypools / volumes / mounttargets | нет | Нет |
> | netappaccounts / capacitypools / volumes / snapshots | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | Нет | нет |
> | applicationgatewayavailableresponseheaders | Нет | нет |
> | applicationgatewayavailableservervariables | Нет | Нет |
> | applicationgatewayavailablessloptions | Нет | нет |
> | applicationgatewayavailablewafrulesets | Нет | нет |
> | applicationgateways | Нет | нет |
> | applicationgatewaywebapplicationfirewallpolicies | Нет | нет |
> | applicationsecuritygroups | Да | Да |
> | azurefirewallfqdntags | Нет | нет |
> | azurefirewalls | Нет | Нет |
> | bastionhosts | Нет | Нет |
> | bgpservicecommunities | Нет | нет |
> | checkfrontdoornameavailability | Нет | нет |
> | checktrafficmanagernameavailability | Нет | нет |
> | connections | Да | Да |
> | ddoscustompolicies | Да | Да |
> | ddosprotectionplans | Нет | нет |
> | dnsoperationresults | Нет | нет |
> | dnsoperationstatuses | Нет | нет |
> | dnszones | Да | Да |
> | dnszones / a | Нет | нет |
> | dnszones / aaaa | Нет | нет |
> | dnszones / all | Нет | нет |
> | dnszones / caa | Нет | нет |
> | dnszones / cname | Нет | нет |
> | dnszones / mx | Нет | нет |
> | dnszones / ns | Нет | нет |
> | dnszones / ptr | Нет | нет |
> | dnszones / recordsets | Нет | нет |
> | dnszones / soa | Нет | нет |
> | dnszones / srv | Нет | Нет |
> | dnszones / txt | Нет | нет |
> | expressroutecircuits | Нет | нет |
> | expressroutegateways | Нет | нет |
> | expressrouteserviceproviders | Нет | нет |
> | firewallpolicies | Да | Да |
> | frontdooroperationresults | Нет | нет |
> | frontdoors | Нет | Нет |
> | фронтдурс/фронтендендпоинтс | Нет | Нет |
> | frontdoorwebapplicationfirewallmanagedrulesets | Нет | Нет |
> | frontdoorwebapplicationfirewallpolicies | Нет | Нет |
> | getdnsresourcereference | нет | нет |
> | internalnotify | нет | нет |
> | ипаллокатионс | Да | Да |
> | ipgroups | Да | Да |
> | loadbalancers | Да — Базовый SKU<br>Нет — Стандартный SKU | Да — Базовый SKU<br>Нет — Стандартный SKU |
> | localnetworkgateways | Да | Да |
> | Расположения | Нет | нет |
> | locations / autoapprovedprivatelinkservices | Нет | нет |
> | locations / availabledelegations | Нет | нет |
> | locations / availableprivateendpointtypes | Нет | нет |
> | locations / availableservicealiases | Нет | нет |
> | locations / baremetaltenants | Нет | нет |
> | locations / batchnotifyprivateendpointsforresourcemove | Нет | нет |
> | locations / batchvalidateprivateendpointsforresourcemove | Нет | нет |
> | locations / checkacceleratednetworkingsupport | Нет | нет |
> | locations / checkdnsnameavailability | Нет | нет |
> | locations / checkprivatelinkservicevisibility | Нет | нет |
> | locations / commitinternalazurenetworkmanagerconfiguration | Нет | нет |
> | locations / effectiveresourceownership | Нет | нет |
> | locations / nfvoperationresults | Нет | нет |
> | locations / nfvoperations | Нет | нет |
> | locations / operationresults | Нет | нет |
> | locations / operations | Нет | Нет |
> | locations / servicetags | Нет | нет |
> | locations / setresourceownership | Нет | Нет |
> | locations / supportedvirtualmachinesizes | Нет | Нет |
> | locations / usages | Нет | нет |
> | locations / validateresourceownership | Нет | Нет |
> | locations / virtualnetworkavailableendpointservices | Нет | Нет |
> | natgateways | Нет | Нет |
> | networkexperimentprofiles | Нет | Нет |
> | networkintentpolicies | Да | Да |
> | networkinterfaces | Да | Да |
> | networkprofiles | Нет | нет |
> | networksecuritygroups | Да | Да |
> | networkwatchers | Да | нет |
> | networkwatchers / connectionmonitors | Да | Нет |
> | networkwatchers / flowlogs | Да | нет |
> | networkwatchers / pingmeshes | Да | нет |
> | Операции | Нет | нет |
> | p2svpngateways | Нет | нет |
> | privatednsoperationresults | Нет | нет |
> | privatednsoperationstatuses | Нет | нет |
> | privatednszones | Да | Да |
> | privatednszones / a | Нет | нет |
> | privatednszones / aaaa | Нет | нет |
> | privatednszones / all | Нет | Нет |
> | privatednszones / cname | Нет | нет |
> | privatednszones / mx | Нет | нет |
> | privatednszones / ptr | Нет | Нет |
> | privatednszones / soa | Нет | нет |
> | privatednszones / srv | Нет | Нет |
> | privatednszones / txt | Нет | Нет |
> | privatednszones / virtualnetworklinks | Да | Да |
> | privatednszonesinternal | нет | нет |
> | privateendpointredirectmaps | Нет | Нет |
> | privateendpoints | Да | Да |
> | privatelinkservices | Нет | Нет |
> | publicipaddresses | Да — Базовый SKU<br>Нет — Стандартный SKU | Да — Базовый SKU<br>Нет — Стандартный SKU |
> | publicipprefixes | Да | Да |
> | routefilters | Нет | нет |
> | routetables | Да | Да |
> | securitypartnerproviders | Да | Да |
> | serviceendpointpolicies | Да | Да |
> | trafficmanagergeographichierarchies | Нет | нет |
> | trafficmanagerprofiles | Да | Да |
> | trafficmanagerprofiles / heatmaps | Нет | нет |
> | trafficmanagerusermetricskeys | Нет | нет |
> | virtualhubs | Нет | нет |
> | virtualnetworkgateways | Да | Да |
> | virtualnetworks | Да | Да |
> | virtualnetworktaps | Нет | Нет |
> | virtualrouters | Да | Да |
> | virtualwans | нет | нет |
> | vpngateways (Virtual WAN) | Нет | Нет |
> | vpnserverconfigurations | Нет | Нет |
> | vpnsites (Virtual WAN) | нет | нет |

> [!IMPORTANT]
> См. [руководство по перемещению сети](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | Нет |
> | checknamespaceavailability | Нет | Нет |
> | пространства имен | Да | Да |
> | namespaces / notificationhubs | Да | Да |
> | operationresults | Нет | Нет |
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
> | hypervsites | нет | Нет |
> | importsites | нет | Нет |
> | Операции | нет | Нет |
> | serversites | нет | Нет |
> | vmwaresites | нет | Нет |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | clusters | Нет | Нет |
> | deletedworkspaces | Нет | нет |
> | linktargets | Нет | нет |
> | Расположения | Нет | нет |
> | locations / operationstatuses | Нет | нет |
> | Операции | Нет | нет |
> | storageinsightconfigs | Нет | нет |
> | workspaces | Да | Да |
> | workspaces / datasources | Нет | Нет |
> | workspaces / linkedservices | Нет | Нет |
> | workspaces / linkedstorageaccounts | нет | Нет |
> | workspaces / metadata | Нет | Нет |
> | workspaces / query | нет | Нет |
> | workspaces / scopedprivatelinkproxies | Нет | Нет |

> [!IMPORTANT]
> Убедитесь, что переход на новую подписку не превышает [квоты для подписки](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Рабочие области, имеющие связанную учетную запись автоматизации, нельзя перемещать. Прежде чем начать операцию перемещения, не забудьте удалить связь с учетными записями службы автоматизации.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | managementassociations | нет | Нет |
> | managementconfigurations | Да | Да |
> | Операции | нет | Нет |
> | solutions | Да | Да |
> | узел "Представления" | Да | Да |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | Нет | нет |
> | legacypeerings | Нет | нет |
> | Операции | Нет | нет |
> | peerasns | Нет | Нет |
> | peeringlocations | Нет | Нет |
> | peerings | нет | Нет |
> | peeringservicecountries | нет | Нет |
> | peeringservicelocations | нет | Нет |
> | peeringserviceproviders | нет | Нет |
> | peeringservices | нет | Нет |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | Нет | Нет |
> | Операции | нет | Нет |
> | policyevents | Нет | Нет |
> | policystates | нет | Нет |
> | policytrackedresources | нет | Нет |
> | remediations | нет | Нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | consoles | Нет | Нет |
> | dashboards | Да | Да |
> | Расположения | нет | Нет |
> | locations / consoles | Нет | Нет |
> | locations / usersettings | нет | нет |
> | Операции | нет | нет |
> | usersettings | Нет | Нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | Нет |
> | locations / checknameavailability | нет | нет |
> | workspacecollections | Да | Да |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | capacities | Да | Да |
> | Расположения | нет | Нет |
> | locations / checknameavailability | Нет | Нет |
> | locations / operationresults | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftpowerplatform"></a>Microsoft. Поверплатформ

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | нет | нет |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |
> | checknameavailability | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Нет | Нет |
> | providerregistrations | нет | нет |
> | providerregistrations / resourcetyperegistrations | нет | нет |
> | rollouts | нет | нет |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | Нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |
> | workspaces | нет | нет |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | нет | Нет |
> | Расположения | нет | Нет |
> | locations / allocatedstamp | нет | Нет |
> | locations / allocatestamp | нет | Нет |
> | locations / backupaadproperties | нет | Нет |
> | locations / backupcrossregionrestore | нет | Нет |
> | locations / backupcrrjob | нет | Нет |
> | locations / backupcrrjobs | нет | Нет |
> | locations / backupcrroperationresults | нет | Нет |
> | locations / backupcrroperationsstatus | нет | Нет |
> | locations / backupprevalidateprotection | нет | Нет |
> | locations / backupstatus | нет | Нет |
> | locations / backupvalidatefeatures | Нет | Нет |
> | locations / checknameavailability | Нет | Нет |
> | Операции | Нет | нет |
> | replicationeligibilityresults | Нет | нет |
> | vaults | Да | Да |

> [!IMPORTANT]
> Ознакомьтесь с разделом [Руководство по перемещению служб восстановления](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | Нет |
> | locations / operationresults | Нет | Нет |
> | locations / operationsstatus | нет | нет |
> | openshiftclusters | Нет | Нет |
> | Операции | нет | нет |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | Нет |
> | пространства имен | Да | Да |
> | namespaces / authorizationrules | нет | Нет |
> | namespaces / hybridconnections | нет | Нет |
> | namespaces / hybridconnections / authorizationrules | нет | Нет |
> | namespaces / privateendpointconnections | Нет | Нет |
> | namespaces / wcfrelays | нет | нет |
> | namespaces / wcfrelays / authorizationrules | Нет | Нет |
> | Операции | нет | нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | нет | Нет |
> | Запросы | Да | Да |
> | resourcechangedetails | нет | Нет |
> | resourcechanges | Нет | Нет |
> | ресурсов | нет | нет |
> | resourceshistory | нет | нет |
> | subscriptionsstatus | нет | нет |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | нет | Нет |
> | childavailabilitystatuses | нет | Нет |
> | childresources | нет | Нет |
> | emergingissues | нет | Нет |
> | события | Нет | Нет |
> | метаданные | нет | нет |
> | Уведомления | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | калкулатетемплатехаш | нет | Нет |
> | checkpolicycompliance | нет | Нет |
> | checkresourcename | нет | Нет |
> | deployments | нет | Нет |
> | deployments / operations | нет | Нет |
> | deploymentscripts | нет | Нет |
> | deploymentscripts / logs | нет | Нет |
> | ссылки | нет | Нет |
> | Расположения | нет | Нет |
> | locations / deploymentscriptoperationresults | нет | Нет |
> | notifyresourcejobs | нет | Нет |
> | operationresults | нет | Нет |
> | Операции | нет | Нет |
> | providers | нет | Нет |
> | resourcegroups | нет | Нет |
> | ресурсов | нет | Нет |
> | subscriptions | нет | Нет |
> | subscriptions / locations | нет | Нет |
> | subscriptions / operationresults | нет | Нет |
> | subscriptions / providers | нет | Нет |
> | subscriptions / resourcegroups | нет | Нет |
> | subscriptions / resourcegroups / resources | нет | Нет |
> | subscriptions / resources | нет | Нет |
> | subscriptions / tagnames | нет | нет |
> | subscriptions / tagnames / tagvalues | Нет | Нет |
> | tags | Нет | нет |
> | templatespecs | нет | нет |
> | templatespecs / versions | нет | нет |
> | tenants | нет | нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | Да | Нет |
> | checkmoderneligibility | Нет | Нет |
> | checknameavailability | нет | нет |
> | operationresults | нет | нет |
> | Операции | нет | нет |
> | saasresources | нет | нет |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | Нет |
> | checkservicenameavailability | Нет | Нет |
> | Операции | нет | Нет |
> | resourcehealthmetadata | нет | Нет |
> | searchservices | Да | Да |

> [!IMPORTANT]
> Вы не можете за одну операцию переместить ресурсы поиска, размещенные в разных регионах. Переместите их в отдельных операциях.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | нет | Нет |
> | advancedthreatprotectionsettings | нет | Нет |
> | оповещения | нет | Нет |
> | allowedconnections | Нет | Нет |
> | applicationwhitelistings | нет | Нет |
> | assessmentmetadata | нет | Нет |
> | assessments | нет | Нет |
> | autodismissalertsrules | нет | Нет |
> | automations | Да | Да |
> | autoprovisioningsettings | нет | Нет |
> | complianceresults | нет | Нет |
> | compliances | нет | Нет |
> | datacollectionagents | Нет | Нет |
> | devicesecuritygroups | нет | Нет |
> | discoveredsecuritysolutions | нет | Нет |
> | externalsecuritysolutions | нет | Нет |
> | informationprotectionpolicies | нет | Нет |
> | iotsecuritysolutions | Да | Да |
> | iotsecuritysolutions / analyticsmodels | нет | Нет |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | нет | Нет |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | нет | Нет |
> | jitnetworkaccesspolicies | нет | Нет |
> | Расположения | нет | Нет |
> | locations / alerts | нет | Нет |
> | locations / allowedconnections | нет | Нет |
> | locations / applicationwhitelistings | нет | Нет |
> | locations / discoveredsecuritysolutions | нет | Нет |
> | locations / externalsecuritysolutions | нет | Нет |
> | locations / jitnetworkaccesspolicies | нет | Нет |
> | locations / securitysolutions | нет | Нет |
> | locations / securitysolutionsreferencedata | нет | Нет |
> | locations / tasks | нет | Нет |
> | locations / topologies | нет | Нет |
> | Операции | нет | Нет |
> | политики | нет | Нет |
> | pricings | нет | Нет |
> | regulatorycompliancestandards | нет | Нет |
> | regulatorycompliancestandards / regulatorycompliancecontrols | нет | Нет |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | нет | Нет |
> | securitycontacts | нет | Нет |
> | securitysolutions | нет | Нет |
> | securitysolutionsreferencedata | нет | Нет |
> | securitystatuses | нет | Нет |
> | securitystatusessummaries | нет | Нет |
> | servervulnerabilityassessments | нет | Нет |
> | Параметры | Нет | Нет |
> | subassessments | нет | нет |
> | задачи | нет | нет |
> | topologies | нет | нет |
> | workspacesettings | нет | нет |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | aggregations | нет | Нет |
> | alertrules | нет | Нет |
> | alertruletemplates | нет | Нет |
> | аутоматионрулес | нет | Нет |
> | bookmarks | нет | Нет |
> | cases | нет | Нет |
> | dataconnectors | нет | Нет |
> | dataconnectorscheckrequirements | нет | Нет |
> | Сущности | нет | Нет |
> | entityqueries | Нет | Нет |
> | incidents | нет | нет |
> | officeconsents | нет | нет |
> | Операции | нет | нет |
> | Параметры | нет | нет |
> | threatintelligence | Нет | Нет |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | consoleservices | нет | нет |
> | Расположения | Нет | Нет |
> | locations / consoleservices | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | gateways | нет | нет |
> | Узлы | Нет | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | Нет |
> | checknamespaceavailability | нет | Нет |
> | Расположения | нет | Нет |
> | locations / deletevirtualnetworkorsubnets | нет | Нет |
> | пространства имен | Да | Да |
> | namespaces / authorizationrules | нет | Нет |
> | namespaces / disasterrecoveryconfigs | нет | Нет |
> | namespaces / disasterrecoveryconfigs / checknameavailability | нет | Нет |
> | namespaces / eventgridfilters | нет | Нет |
> | namespaces / networkrulesets | нет | Нет |
> | namespaces / queues | нет | Нет |
> | namespaces / queues / authorizationrules | нет | Нет |
> | namespaces / topics | нет | Нет |
> | namespaces / topics / authorizationrules | Нет | Нет |
> | namespaces / topics / subscriptions | нет | нет |
> | namespaces / topics / subscriptions / rules | Нет | Нет |
> | Операции | нет | нет |
> | premiummessagingregions | нет | нет |
> | sku | нет | нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | нет | Нет |
> | clusters | Да | Да |
> | clusters / applications | нет | Нет |
> | containergroups | нет | Нет |
> | containergroupsets | нет | Нет |
> | edgeclusters | нет | Нет |
> | Расположения | нет | Нет |
> | locations / clusterversions | нет | Нет |
> | locations / environments | нет | Нет |
> | locations / operationresults | нет | Нет |
> | locations / operations | Нет | Нет |
> | managedclusters | Нет | Нет |
> | networks | нет | нет |
> | Операции | Нет | Нет |
> | secretstores | нет | нет |
> | volumes. | нет | нет |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | Да | Да |
> | containergroups | нет | Нет |
> | gateways | Да | Да |
> | Расположения | Нет | Нет |
> | locations / applicationoperations | нет | Нет |
> | locations / gatewayoperations | Нет | Нет |
> | locations / networkoperations | Нет | Нет |
> | locations / secretoperations | Нет | Нет |
> | locations / volumeoperations | нет | нет |
> | networks | Да | Да |
> | Операции | нет | нет |
> | секретные коды | Да | Да |
> | volumes. | Да | Да |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | rollouts | нет | нет |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | Нет |
> | locations / checknameavailability | нет | Нет |
> | locations / operationresults | Нет | Нет |
> | locations / operationstatuses | нет | Нет |
> | locations / usages | Нет | Нет |
> | Операции | нет | нет |
> | signalr | Да | Да |
> | signalr / eventgridfilters | Нет | Нет |

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
> | applicationdefinitions | нет | Нет |
> | веб-масштабированием; | нет | Нет |
> | jitrequests | Нет | Нет |
> | Расположения | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | Нет | Нет |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | Нет |
> | instancepools | нет | Нет |
> | Расположения | Да | Да |
> | locations / administratorazureasyncoperation | нет | Нет |
> | locations / administratoroperationresults | нет | Нет |
> | locations / auditingsettingsazureasyncoperation | нет | Нет |
> | locations / auditingsettingsoperationresults | нет | Нет |
> | locations / capabilities | нет | Нет |
> | locations / databaseazureasyncoperation | нет | Нет |
> | locations / databaseoperationresults | нет | Нет |
> | locations / databaserestoreazureasyncoperation | нет | Нет |
> | locations / deletevirtualnetworkorsubnets | нет | Нет |
> | locations / deletevirtualnetworkorsubnetsazureasyncoperation | нет | Нет |
> | locations / deletevirtualnetworkorsubnetsoperationresults | нет | Нет |
> | locations / dnsaliasasyncoperation | нет | Нет |
> | locations / dnsaliasoperationresults | нет | Нет |
> | locations / elasticpoolazureasyncoperation | нет | Нет |
> | locations / elasticpooloperationresults | нет | Нет |
> | locations / encryptionprotectorazureasyncoperation | нет | Нет |
> | locations / encryptionprotectoroperationresults | нет | Нет |
> | locations / extendedauditingsettingsazureasyncoperation | нет | Нет |
> | locations / extendedauditingsettingsoperationresults | нет | Нет |
> | locations / failovergroupazureasyncoperation | нет | Нет |
> | locations / failovergroupoperationresults | нет | Нет |
> | locations / firewallrulesazureasyncoperation | нет | Нет |
> | locations / firewallrulesoperationresults | нет | Нет |
> | locations / instancefailovergroupazureasyncoperation | нет | Нет |
> | locations / instancefailovergroupoperationresults | нет | Нет |
> | locations / instancefailovergroups | нет | Нет |
> | locations / instancepoolazureasyncoperation | нет | Нет |
> | locations / instancepooloperationresults | нет | Нет |
> | locations / jobagentazureasyncoperation | нет | Нет |
> | locations / jobagentoperationresults | нет | Нет |
> | locations / longtermretentionbackupazureasyncoperation | нет | Нет |
> | locations / longtermretentionbackupoperationresults | нет | Нет |
> | locations / longtermretentionbackups | нет | Нет |
> | locations / longtermretentionmanagedinstancebackupazureasyncoperation | нет | Нет |
> | locations / longtermretentionmanagedinstancebackupoperationresults | нет | Нет |
> | locations / longtermretentionmanagedinstancebackups | нет | Нет |
> | locations / longtermretentionmanagedinstances | нет | Нет |
> | locations / longtermretentionpolicyazureasyncoperation | нет | Нет |
> | locations / longtermretentionpolicyoperationresults | нет | Нет |
> | locations / longtermretentionservers | нет | Нет |
> | locations / manageddatabaseazureasyncoperation | нет | Нет |
> | locations / manageddatabasecompleterestoreazureasyncoperation | нет | Нет |
> | locations / manageddatabasecompleterestoreoperationresults | нет | Нет |
> | locations / manageddatabaseoperationresults | нет | Нет |
> | locations / manageddatabaserestoreazureasyncoperation | нет | Нет |
> | locations / manageddatabaserestoreoperationresults | нет | Нет |
> | locations / managedinstanceazureasyncoperation | нет | Нет |
> | locations / managedinstanceencryptionprotectorazureasyncoperation | нет | Нет |
> | locations / managedinstanceencryptionprotectoroperationresults | нет | Нет |
> | locations / managedinstancekeyazureasyncoperation | нет | Нет |
> | locations / managedinstancekeyoperationresults | нет | Нет |
> | locations / managedinstancelongtermretentionpolicyazureasyncoperation | нет | Нет |
> | locations / managedinstancelongtermretentionpolicyoperationresults | нет | Нет |
> | locations / managedinstanceoperationresults | нет | Нет |
> | locations / managedinstancetdecertazureasyncoperation | нет | Нет |
> | locations / managedinstancetdecertoperationresults | нет | Нет |
> | locations / managedserversecurityalertpoliciesazureasyncoperation | нет | Нет |
> | locations / managedserversecurityalertpoliciesoperationresults | нет | Нет |
> | locations / managedshorttermretentionpolicyazureasyncoperation | нет | Нет |
> | locations / managedshorttermretentionpolicyoperationresults | нет | Нет |
> | locations / notifyazureasyncoperation | нет | Нет |
> | locations / privateendpointconnectionazureasyncoperation | нет | Нет |
> | locations / privateendpointconnectionoperationresults | нет | Нет |
> | locations / privateendpointconnectionproxyazureasyncoperation | нет | Нет |
> | locations / privateendpointconnectionproxyoperationresults | нет | Нет |
> | locations / securityalertpoliciesazureasyncoperation | нет | Нет |
> | locations / securityalertpoliciesoperationresults | нет | Нет |
> | locations / serveradministratorazureasyncoperation | нет | Нет |
> | locations / serveradministratoroperationresults | нет | нет |
> | locations / serverazureasyncoperation | нет | Нет |
> | locations / serverkeyazureasyncoperation | нет | Нет |
> | locations / serverkeyoperationresults | нет | Нет |
> | locations / serveroperationresults | нет | Нет |
> | locations / shorttermretentionpolicyazureasyncoperation | нет | Нет |
> | locations / shorttermretentionpolicyoperationresults | нет | Нет |
> | locations / syncagentoperationresults | нет | Нет |
> | locations / syncdatabaseids | нет | Нет |
> | locations / syncgroupoperationresults | нет | Нет |
> | locations / syncmemberoperationresults | нет | Нет |
> | locations / tdecertazureasyncoperation | нет | Нет |
> | locations / tdecertoperationresults | нет | Нет |
> | locations / usages | нет | Нет |
> | locations / virtualclusterazureasyncoperation | нет | Нет |
> | locations / virtualclusteroperationresults | нет | Нет |
> | locations / virtualnetworkrulesazureasyncoperation | нет | Нет |
> | locations / virtualnetworkrulesoperationresults | нет | Нет |
> | locations / vulnerabilityassessmentscanazureasyncoperation | нет | Нет |
> | locations / vulnerabilityassessmentscanoperationresults | нет | Нет |
> | managedinstances | нет | Нет |
> | managedinstances / administrators | нет | Нет |
> | managedinstances / databases | нет | Нет |
> | managedinstances / databases / backuplongtermretentionpolicies | нет | Нет |
> | managedinstances / databases / vulnerabilityassessments | нет | Нет |
> | managedinstances / metricdefinitions | нет | Нет |
> | managedinstances / metrics | нет | Нет |
> | managedinstances / recoverabledatabases | нет | Нет |
> | managedinstances / tdecertificates | Нет | Нет |
> | managedinstances / vulnerabilityassessments | нет | Нет |
> | Операции | нет | Нет |
> | servers | Да | Да |
> | servers / administratoroperationresults | нет | Нет |
> | servers / administrators | нет | Нет |
> | servers / advisors | нет | Нет |
> | servers / aggregateddatabasemetrics | нет | Нет |
> | servers / auditingpolicies | нет | Нет |
> | servers / auditingsettings | нет | Нет |
> | servers / automatictuning | Нет | Нет |
> | servers / communicationlinks | нет | Нет |
> | servers / connectionpolicies | нет | Нет |
> | servers / databases | Да | Да |
> | servers / databases / advisors | нет | Нет |
> | servers / databases / auditingpolicies | нет | Нет |
> | servers / databases / auditingsettings | нет | Нет |
> | servers / databases / auditrecords | нет | Нет |
> | servers / databases / automatictuning | нет | Нет |
> | servers / databases / backuplongtermretentionpolicies | нет | Нет |
> | servers / databases / backupshorttermretentionpolicies | нет | Нет |
> | servers / databases / connectionpolicies | нет | Нет |
> | servers / databases / datamaskingpolicies | нет | Нет |
> | servers / databases / datamaskingpolicies / rules | нет | Нет |
> | servers / databases / extensions | нет | Нет |
> | servers / databases / geobackuppolicies | нет | Нет |
> | servers / databases / metricdefinitions | нет | Нет |
> | servers / databases / metrics | нет | Нет |
> | servers / databases / recommendedsensitivitylabels | нет | Нет |
> | servers / databases / securityalertpolicies | нет | Нет |
> | servers / databases / syncgroups | нет | Нет |
> | servers / databases / syncgroups / syncmembers | нет | Нет |
> | servers / databases / topqueries | нет | Нет |
> | servers / databases / topqueries / querytext | нет | Нет |
> | servers / databases / transparentdataencryption | нет | Нет |
> | servers / databases / vulnerabilityassessment | нет | Нет |
> | servers / databases / vulnerabilityassessments | нет | Нет |
> | servers / databases / vulnerabilityassessmentscans | нет | Нет |
> | servers / databases / vulnerabilityassessmentsettings | нет | Нет |
> | servers / databases / workloadgroups | нет | Нет |
> | servers / databasesecuritypolicies | нет | Нет |
> | servers / disasterrecoveryconfiguration | Нет | Нет |
> | servers / dnsaliases | нет | Нет |
> | servers / elasticpoolestimates | нет | Нет |
> | servers / elasticpools | Да | Да |
> | servers / elasticpools / advisors | нет | Нет |
> | servers / elasticpools / metricdefinitions | нет | Нет |
> | servers / elasticpools / metrics | нет | Нет |
> | servers / encryptionprotector | нет | Нет |
> | servers / extendedauditingsettings | нет | Нет |
> | servers / failovergroups | Нет | Нет |
> | servers / import | Нет | Нет |
> | servers / importexportoperationresults | нет | Нет |
> | servers / jobaccounts | Да | Да |
> | servers / jobagents | Да | Да |
> | servers / jobagents / jobs | нет | Нет |
> | servers / jobagents / jobs / executions | нет | Нет |
> | servers / jobagents / jobs / steps | нет | Нет |
> | servers / keys | нет | Нет |
> | servers / operationresults | нет | Нет |
> | servers / recommendedelasticpools | нет | Нет |
> | servers / recoverabledatabases | нет | Нет |
> | servers / restorabledroppeddatabases | нет | Нет |
> | servers / securityalertpolicies | нет | Нет |
> | servers / serviceobjectives | нет | Нет |
> | servers / syncagents | нет | Нет |
> | servers / tdecertificates | нет | Нет |
> | servers / usages | Нет | Нет |
> | servers / virtualnetworkrules | Нет | Нет |
> | servers / vulnerabilityassessments | Нет | Нет |
> | virtualclusters | Да | Да |

> [!IMPORTANT]
> База данных и сервер должны быть в одной группе ресурсов. При перемещении сервера SQL Server все его базы данных также перемещаются. Это поведение применяется к базам данных SQL Azure и Azure синапсе Analytics.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | Нет |
> | locations / availabilitygrouplisteneroperationresults | нет | Нет |
> | locations / operationtypes | нет | Нет |
> | locations / sqlvirtualmachinegroupoperationresults | Нет | Нет |
> | locations / sqlvirtualmachineoperationresults | нет | Нет |
> | Операции | Нет | Нет |
> | sqlvirtualmachinegroups | Да | Да |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | нет | нет |
> | sqlvirtualmachines | Да | Да |

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
> | Операции | Нет | Нет |
> | storageaccounts | Да | Да |
> | storageaccounts / blobservices | нет | нет |
> | storageaccounts / fileservices | нет | нет |
> | storageaccounts / listaccountsas | нет | нет |
> | storageaccounts / listservicesas | нет | нет |
> | storageaccounts / queueservices | нет | нет |
> | storageaccounts / services | нет | нет |
> | storageaccounts / services / metricdefinitions | нет | нет |
> | storageaccounts / tableservices | нет | нет |
> | usages | Нет | Нет |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | caches | Нет | Нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operations | Нет | Нет |
> | locations / workflows | нет | нет |
> | Операции | нет | нет |
> | storagesyncservices | Да | Да |
> | storagesyncservices / registeredservers | нет | нет |
> | storagesyncservices / syncgroups | нет | нет |
> | storagesyncservices / syncgroups / cloudendpoints | Нет | Нет |
> | storagesyncservices / syncgroups / serverendpoints | Нет | нет |
> | storagesyncservices / workflows | Нет | Нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Нет | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Нет | нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | managers | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | clusters | нет | нет |
> | Расположения | Нет | Нет |
> | locations / quotas | Нет | Нет |
> | Операции | Нет | Нет |
> | streamingjobs | Да | Да |

> [!IMPORTANT]
> Задания Stream Analytics в состоянии выполнения нельзя переместить.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | environments | нет | нет |
> | environments / eventsources | нет | нет |
> | instances | Нет | Нет |
> | instances / environments | Нет | нет |
> | instances / environments / eventsources | Нет | нет |

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
> | subscriptiondefinitions | Нет | Нет |
> | subscriptionoperations | Нет | нет |
> | subscriptions | Нет | нет |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | operationresults | нет | нет |
> | Операции | нет | нет |
> | operationsstatus | нет | нет |
> | services; | Нет | Нет |
> | services / problemclassifications | Нет | нет |
> | supporttickets | Нет | нет |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | Нет |
> | Операции | нет | нет |
> | workspaces | Да | Да |
> | workspaces / bigdatapools | Да | Да |
> | workspaces / operationresults | Нет | Нет |
> | workspaces / operationstatuses | Нет | нет |
> | workspaces / sqlpools | Да | Да |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | environments | Да | Да |
> | environments / accesspolicies | Нет | нет |
> | environments / eventsources | Да | Да |
> | environments / referencedatasets | Да | Да |
> | Операции | Нет | нет |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | stores | Да | Да |
> | stores / accesspolicies | Нет | нет |
> | stores / services | Нет | нет |
> | stores / services / tokens | Нет | нет |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Нет | нет |
> | имажетемплатес/рунаутпутс | Нет | нет |
> | Расположения | Нет | Нет |
> | locations / operations | Нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | account | Нет | нет |
> | account / extension | Нет | нет |
> | account / project | Нет | Нет |
> | checknameavailability | Нет | Нет |
> | Операции | нет | нет |

> [!IMPORTANT]
> Чтобы изменить подписку на Azure DevOps, см. статью [Изменить подписку Azure, используемую для выставления счетов](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | arczones | Нет | нет |
> | Расположения | Нет | нет |
> | locations / operationstatuses | Нет | нет |
> | Операции | Нет | нет |
> | resourcepools | Нет | нет |
> | vcenters | Нет | нет |
> | virtualmachines | Нет | Нет |
> | virtualmachinetemplates | Нет | Нет |
> | virtualnetworks | нет | Нет |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Нет | нет |
> | dedicatedcloudservices | Нет | нет |
> | Расположения | Нет | нет |
> | locations / availabilities | Нет | нет |
> | locations / operationresults | Нет | нет |
> | locations / privateclouds | Нет | нет |
> | locations / privateclouds / resourcepools | Нет | нет |
> | locations / privateclouds / virtualmachinetemplates | Нет | нет |
> | locations / privateclouds / virtualnetworks | Нет | нет |
> | locations / usages | Нет | Нет |
> | Операции | Нет | Нет |
> | virtualmachines | нет | Нет |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | устройства | Нет | нет |
> | Расположения | Нет | нет |
> | locations / operationstatuses | Нет | Нет |
> | Операции | Нет | Нет |
> | vnfs | нет | Нет |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |
> | Операции | Нет | Нет |
> | Планы | нет | Нет |
> | registeredsubscriptions | нет | Нет |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | availablestacks | Нет | Нет |
> | billingmeters | Нет | Нет |
> | certificates | Нет | Да |
> | checknameavailability | Нет | нет |
> | connectiongateways | Да | Да |
> | connections | Да | Да |
> | customapis | Да | Да |
> | deletedsites | Нет | нет |
> | deploymentlocations | Нет | нет |
> | georegions | Нет | нет |
> | hostingenvironments | Нет | нет |
> | hostingenvironments / eventgridfilters | Нет | нет |
> | hostingenvironments / multirolepools | Нет | нет |
> | hostingenvironments / workerpools | Нет | Нет |
> | ishostingenvironmentnameavailable | Нет | нет |
> | ishostnameavailable | Нет | нет |
> | isusernameavailable | Нет | нет |
> | kubeenvironments | Да | Да |
> | listsitesassignedtohostname | Нет | нет |
> | Расположения | Нет | нет |
> | locations / apioperations | Нет | нет |
> | locations / connectiongatewayinstallations | Нет | нет |
> | locations / deletedsites | нет | нет |
> | locations / deletevirtualnetworkorsubnets | Нет | нет |
> | locations / extractapidefinitionfromwsdl | Нет | нет |
> | locations / getnetworkpolicies | Нет | нет |
> | locations / listwsdlinterfaces | Нет | нет |
> | locations / managedapis | Нет | нет |
> | locations / operationresults | Нет | нет |
> | locations / operations | Нет | нет |
> | locations / runtimes | Нет | нет |
> | Операции | Нет | нет |
> | publishingusers | Нет | Нет |
> | к просмотру фильмов | Нет | нет |
> | resourcehealthmetadata | Нет | Нет |
> | runtimes | Нет | нет |
> | serverfarms | Да | Да |
> | serverfarms / eventgridfilters | Нет | нет |
> | sites | Да | Да |
> | sites / eventgridfilters | Нет | Нет |
> | sites / hostnamebindings | Нет | нет |
> | sites / networkconfig | Нет | нет |
> | sites / premieraddons | Да | Да |
> | sites / slots | Да | Да |
> | sites / slots / eventgridfilters | Нет | нет |
> | sites / slots / hostnamebindings | Нет | нет |
> | sites / slots / networkconfig | Нет | нет |
> | sourcecontrols | Нет | Нет |
> | staticsites | Нет | Нет |
> | validate | нет | нет |
> | verifyhostingenvironmentvnet | нет | нет |

> [!IMPORTANT]
> См. статью [Руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | Нет |
> | locations / operationstatuses | Нет | Нет |
> | multipleactivationkeys | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | deviceservices | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftworkloadbuilder"></a>Microsoft. Ворклоадбуилдер

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | нет |
> | locations / operationstatuses | Нет | нет |
> | Операции | Нет | нет |
> | рабочие нагрузки | Нет | Нет |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | components | Нет | Нет |
> | componentssummary | Нет | Нет |
> | monitorinstances | Нет | Нет |
> | monitorinstancessummary | Нет | Нет |
> | monitors | Нет | Нет |
> | notificationsettings | Нет | Нет |
> | Операции | Нет | Нет |

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.

## <a name="next-steps"></a>Дальнейшие действия
Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](move-resource-group-and-subscription.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
