---
title: Поддержка операции перемещения типом ресурсов
description: Список типов ресурсов Azure, подлежащих перемещению в новую группу ресурсов или подписку.
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 7f241e12200101e2f8f9efa7cf31e4483b2d4229
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044538"
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
> - [NNTP. APPSERVICE](#microsoftappservice)
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
> - [MICROSOFT. БИЗТАЛКСЕРВИЦЕС](#microsoftbiztalkservices)
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
> - [Microsoft. Коннектедкаче](#microsoftconnectedcache)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [NNTP. контентмодератор](#microsoftcontentmoderator)
> - [NNTP. кортанааналитикс](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CostManagementExports](#microsoftcostmanagementexports)
> - [NNTP. кустомеринсигхтс](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [NNTP. СОЕДИНЕНИЕ](#microsoftdataconnect)
> - [NNTP. DATAEXCHANGE](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [NNTP. DATALAKE](#microsoftdatalake)
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
> - [NNTP. GENOMICS](#microsoftgenomics)
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
> - [NNTP. локатионбаседсервицес](#microsoftlocationbasedservices)
> - [NNTP. ФАЙЛ LOCATIONSERVICES](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [NNTP. мачинелеарнингкомпуте](#microsoftmachinelearningcompute)
> - [NNTP. мачинелеарнинжекспериментатион](#microsoftmachinelearningexperimentation)
> - [NNTP. мачинелеарнингмоделманажемент](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [NNTP. манажеднетворк](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [NNTP. MICROSERVICES4SPRING](#microsoftmicroservices4spring)
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
> - [NNTP. серверманажемент](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [NNTP. Обслуживание](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [NNTP. сторажекаче](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [NNTP. сторажесинкдев](#microsoftstoragesyncdev)
> - [NNTP. сторажесинЦинт](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [NNTP. стреаманалитиксексплорер](#microsoftstreamanalyticsexplorer)
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
> | domainservices / oucontainer | Нет | нет |
> | Расположения | Нет | нет |
> | locations / operationresults | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | азуреадметрикс | Нет | Нет |
> | diagnosticsettings | Нет | нет |
> | diagnosticsettingscategories | Нет | нет |
> | Операции | Нет | Нет |
> | privatelinkforazuread | Нет | Нет |
> | tenants | нет | Нет |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | operationresults | Нет | Нет |
> | Операции | Нет | Нет |
> | supportproviders | нет | Нет |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Нет | нет |
> | addsservices | Нет | нет |
> | agents | Нет | нет |
> | anonymousapiusers | Нет | нет |
> | настройка | Нет | нет |
> | журналы | Нет | нет |
> | Операции | Нет | нет |
> | reports | Нет | Нет |
> | servicehealthmetrics | Нет | Нет |
> | services; | нет | Нет |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | конфигурации | Нет | нет |
> | generaterecommendations | Нет | нет |
> | метаданные | Нет | нет |
> | Операции | Нет | Нет |
> | к просмотру фильмов | Нет | Нет |
> | suppressions | Нет | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | actionrules | Нет | нет |
> | оповещения | Нет | нет |
> | alertslist | Нет | нет |
> | alertsmetadata | Нет | нет |
> | alertssummary | Нет | Нет |
> | alertssummarylist | Нет | нет |
> | Операции | Нет | Нет |
> | smartdetectoralertrules | Нет | Нет |
> | smartgroups | нет | Нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | нет |
> | locations / checknameavailability | Нет | нет |
> | locations / operationresults | Нет | Нет |
> | locations / operationstatuses | Нет | Нет |
> | Операции | Нет | Нет |
> | servers | нет | Нет |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | Нет | нет |
> | checknameavailability | Нет | нет |
> | checkservicenameavailability | Нет | Нет |
> | Операции | Нет | нет |
> | reportfeedback | Нет | Нет |
> | служба | Да | Да |
> | validateservicename | нет | нет |

> [!IMPORTANT]
> Невозможно переместить службу управления API, для которой настроено значение "SKU потребления".

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | нет |
> | configurationstores | Нет | нет |
> | configurationstores / eventgridfilters | Нет | нет |
> | Расположения | Нет | Нет |
> | locations / operationsstatus | Нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | нет |
> | locations / checknameavailability | Нет | нет |
> | locations / operationresults | Нет | Нет |
> | locations / operationstatus | Нет | Нет |
> | Операции | Нет | Нет |
> | spring | нет | Нет |
> | Пружина/приложения | нет | Нет |
> | Пружина/приложения/развертывания | нет | Нет |

## <a name="microsoftappservice"></a>NNTP. APPSERVICE

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | apiapps | Нет | Нет |
> | appidentities | нет | нет |
> | gateways | Нет | Нет |

> [!IMPORTANT]
> См. статью [Руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | attestationproviders | нет | Нет |
> | дефаултпровидерс | нет | Нет |
> | Расположения | нет | Нет |
> | расположения и defaultProvider | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkaccess | Нет | нет |
> | classicadministrators | Нет | нет |
> | dataaliases | Нет | нет |
> | denyassignments | Нет | нет |
> | elevateaccess | Нет | нет |
> | findorphanroleassignments | Нет | нет |
> | locks | Нет | нет |
> | Операции | Нет | нет |
> | operationstatus | Нет | Нет |
> | разрешения | Нет | Нет |
> | policyassignments | Нет | Нет |
> | policydefinitions | Нет | Нет |
> | policysetdefinitions | нет | Нет |
> | привателинкассоЦиатионс | нет | Нет |
> | provideroperations | нет | Нет |
> | ресаурцеманажементпривателинкс | нет | Нет |
> | roleassignments | Нет | Нет |
> | roleassignmentsusagemetrics | нет | Нет |
> | roledefinitions | нет | Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Да | Да |
> | automationaccounts / configurations | нет | нет |
> | automationaccounts / jobs | нет | нет |
> | automationaccounts / privateendpointconnectionproxies | нет | нет |
> | automationaccounts / privateendpointconnections | Нет | Нет |
> | automationaccounts / privatelinkresources | нет | нет |
> | automationaccounts / runbooks | Нет | Нет |
> | automationaccounts / softwareupdateconfigurations | Нет | Нет |
> | automationaccounts / webhooks | Нет | нет |
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
> | privateclouds | нет | Нет |
> | privateclouds / clusters | нет | Нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Да | Да |
> | b2ctenants | Нет | нет |
> | checknameavailability | Нет | Нет |
> | гуестусажес | Да | Да |
> | Операции | нет | Нет |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Да | Да |
> | hybriddatamanagers | Да | Да |
> | Операции | Нет | Нет |
> | postgresinstances | Да | Да |
> | sqlinstances | Да | Да |
> | sqlmanagedinstances | Да | Да |
> | sqlserverinstances | Да | Да |
> | sqlserverregistrations | Да | Да |
> | sqlserverregistrations / sqlservers | нет | нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | нет | Нет |
> | еджесубскриптионс | нет | Нет |
> | Операции | нет | Нет |
> | registrations | Нет | Нет |
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
> | Расположения | Нет | нет |
> | locations / accountoperationresults | Нет | Нет |
> | locations / checknameavailability | Нет | Нет |
> | locations / quotas | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Нет | нет |
> | billingaccounts / agreements | Нет | нет |
> | billingaccounts / billingpermissions | Нет | нет |
> | billingaccounts / billingprofiles | Нет | нет |
> | billingaccounts / billingprofiles / availablebalance | Нет | нет |
> | billingaccounts / billingprofiles / billingpermissions | Нет | нет |
> | billingaccounts / billingprofiles / billingroleassignments | Нет | нет |
> | billingaccounts / billingprofiles / billingroledefinitions | Нет | нет |
> | billingaccounts / billingprofiles / billingsubscriptions | Нет | нет |
> | billingaccounts / billingprofiles / createbillingroleassignment | Нет | нет |
> | billingaccounts / billingprofiles / customers | Нет | нет |
> | billingaccounts / billingprofiles / instructions | Нет | нет |
> | billingaccounts / billingprofiles / invoices | Нет | нет |
> | billingaccounts / billingprofiles / invoices / pricesheet | Нет | нет |
> | billingaccounts / billingprofiles / invoices / transactions | Нет | нет |
> | billingaccounts / billingprofiles / invoicesections | Нет | нет |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | Нет | нет |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | Нет | нет |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | Нет | нет |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | Нет | нет |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | Нет | нет |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | Нет | нет |
> | billingaccounts / billingprofiles / invoicesections / products | Нет | нет |
> | billingaccounts / billingprofiles / invoicesections / products / transfer | Нет | нет |
> | billingaccounts / billingprofiles / invoicesections / products / updateautorenew | Нет | нет |
> | billingaccounts / billingprofiles / invoicesections / transactions | Нет | нет |
> | billingaccounts / billingprofiles / invoicesections / transfers | Нет | нет |
> | billingaccounts / billingprofiles / patchoperations | Нет | нет |
> | billingaccounts / billingprofiles / paymentmethods | Нет | нет |
> | billingaccounts / billingprofiles / policies | Нет | нет |
> | billingaccounts / billingprofiles / pricesheet | Нет | нет |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | Нет | нет |
> | billingaccounts / billingprofiles / products | Нет | нет |
> | billingaccounts / billingprofiles / transactions | Нет | нет |
> | billingaccounts / billingroleassignments | Нет | нет |
> | billingaccounts / billingroledefinitions | Нет | нет |
> | billingaccounts / billingsubscriptions | Нет | нет |
> | billingaccounts / billingsubscriptions / invoices | Нет | нет |
> | billingaccounts / createbillingroleassignment | Нет | нет |
> | billingaccounts / createinvoicesectionoperations | Нет | нет |
> | billingaccounts / customers | Нет | нет |
> | billingaccounts / customers / billingpermissions | Нет | нет |
> | billingaccounts / customers / billingsubscriptions | Нет | нет |
> | billingaccounts / customers / initiatetransfer | Нет | нет |
> | billingaccounts / customers / policies | Нет | нет |
> | billingaccounts / customers / products | Нет | нет |
> | billingaccounts / customers / transactions | Нет | нет |
> | billingaccounts / customers / transfers | Нет | нет |
> | billingaccounts / departments | Нет | нет |
> | биллингаккаунтс/Departments/биллингпермиссионс | Нет | нет |
> | биллингаккаунтс/Departments/биллингролеассигнментс | Нет | нет |
> | биллингаккаунтс/Departments/биллингроледефинитионс | Нет | нет |
> | billingaccounts / enrollmentaccounts | Нет | нет |
> | биллингаккаунтс/енроллментаккаунтс/биллингпермиссионс | Нет | нет |
> | биллингаккаунтс/енроллментаккаунтс/биллингролеассигнментс | Нет | нет |
> | биллингаккаунтс/енроллментаккаунтс/биллингроледефинитионс | Нет | нет |
> | billingaccounts / invoices | Нет | нет |
> | биллингаккаунтс/счета/транзакции | Нет | нет |
> | billingaccounts / invoicesections | Нет | нет |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | Нет | нет |
> | billingaccounts / invoicesections / billingsubscriptions | Нет | нет |
> | billingaccounts / invoicesections / billingsubscriptions / transfer | Нет | нет |
> | billingaccounts / invoicesections / elevate | Нет | нет |
> | billingaccounts / invoicesections / initiatetransfer | Нет | нет |
> | billingaccounts / invoicesections / patchoperations | Нет | нет |
> | billingaccounts / invoicesections / productmoveoperations | Нет | нет |
> | billingaccounts / invoicesections / products | Нет | нет |
> | billingaccounts / invoicesections / products / transfer | нет | нет |
> | billingaccounts / invoicesections / products / updateautorenew | Нет | нет |
> | billingaccounts / invoicesections / producttransfersresults | Нет | нет |
> | billingaccounts / invoicesections / transactions | Нет | нет |
> | billingaccounts / invoicesections / transfers | Нет | нет |
> | billingaccounts / lineofcredit | Нет | нет |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | Нет | нет |
> | billingaccounts / operationresults | Нет | нет |
> | billingaccounts / patchoperations | Нет | нет |
> | billingaccounts / paymentmethods | Нет | нет |
> | billingaccounts / products | Нет | нет |
> | billingaccounts / transactions | Нет | нет |
> | billingperiods | Нет | нет |
> | billingpermissions | Нет | нет |
> | billingproperty | Нет | нет |
> | billingroleassignments | Нет | нет |
> | billingroledefinitions | Нет | нет |
> | createbillingroleassignment | Нет | нет |
> | departments | Нет | нет |
> | enrollmentaccounts | Нет | Нет |
> | invoices | Нет | Нет |
> | operationresults | нет | Нет |
> | Операции | нет | Нет |
> | operationstatus | нет | Нет |
> | transfers | нет | Нет |
> | transfers / accepttransfer | Нет | Нет |
> | transfers / declinetransfer | нет | нет |
> | transfers / operationstatus | Нет | Нет |
> | transfers / validatetransfer | Нет | нет |
> | validateaddress | Нет | нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | нет | нет |
> | mapapis | нет | нет |
> | Операции | нет | нет |
> | updatecommunicationpreference | нет | нет |

## <a name="microsoftbiztalkservices"></a>MICROSOFT. БИЗТАЛКСЕРВИЦЕС

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | biztalk | нет | Нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | нет | Нет |
> | cordamembers | нет | Нет |
> | Расположения | Нет | Нет |
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
> | Операции | нет | Нет |
> | tokenservices | Нет | Нет |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | нет | Нет |
> | blueprintassignments / assignmentoperations | нет | Нет |
> | blueprintassignments / operations | нет | Нет |
> | blueprints | нет | Нет |
> | blueprints / artifacts | нет | Нет |
> | blueprints / versions | Нет | Нет |
> | blueprints / versions / artifacts | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | botservices | нет | Нет |
> | botservices / channels | нет | Нет |
> | botservices / connections | Нет | Нет |
> | checknameavailability | нет | Нет |
> | listauthserviceproviders | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | нет |
> | Расположения | Нет | нет |
> | locations / operationresults | Нет | нет |
> | locations / operationsstatus | Нет | нет |
> | Операции | Нет | нет |
> | redis | Да | Да |
> | Redis/евентгридфилтерс | Нет | нет |
> | redis / privatelinkresources | Нет | нет |
> | redisenterprise | Да | Да |

> [!IMPORTANT]
> Если экземпляр кэша Azure для Redis настроен с помощью виртуальной сети, его нельзя переместить в другую подписку. См. [ограничения перемещения сети](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Нет | нет |
> | calculateexchange | Нет | нет |
> | calculateprice | Нет | нет |
> | calculatepurchaseprice | Нет | нет |
> | catalogs | Нет | нет |
> | checkoffers | Нет | нет |
> | checkpurchasestatus | Нет | нет |
> | checkscopes | Нет | нет |
> | commercialreservationorders | Нет | нет |
> | обмен валюты | Нет | нет |
> | listbenefits | Нет | нет |
> | Операции | Нет | нет |
> | placepurchaseorder | Нет | нет |
> | reservationorders | Нет | нет |
> | reservationorders / availablescopes | Нет | нет |
> | reservationorders / calculaterefund | Нет | Нет |
> | reservationorders / merge | Нет | Нет |
> | reservationorders / reservations | нет | Нет |
> | reservationorders / reservations / availablescopes | Нет | Нет |
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
> | cdnwebapplicationfirewallpolicies | Нет | Нет |
> | checknameavailability | Нет | Нет |
> | checkresourceusage | Нет | нет |
> | edgenodes | Нет | нет |
> | operationresults | Нет | нет |
> | operationresults / profileresults | Нет | нет |
> | operationresults / profileresults / endpointresults | Нет | Нет |
> | operationresults / profileresults / endpointresults / customdomainresults | Нет | Нет |
> | operationresults / profileresults / endpointresults / origingroupresults | Нет | Нет |
> | operationresults / profileresults / endpointresults / originresults | нет | Нет |
> | Операции | нет | Нет |
> | профили | Да | Да |
> | profiles / endpoints | Нет | Нет |
> | profiles / endpoints / customdomains | Нет | нет |
> | profiles / endpoints / origingroups | Нет | Нет |
> | profiles / endpoints / origins | Нет | Нет |
> | validateprobe | нет | Нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | certificateorders | Да | Да |
> | certificateorders / certificates | Нет | нет |
> | Операции | Нет | нет |
> | validatecertificateregistrationinformation | Нет | нет |

> [!IMPORTANT]
> См. статью [Руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | нет | Нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | capabilities | Нет | нет |
> | checkdomainnameavailability | Нет | нет |
> | domainnames | Нет | нет |
> | domainnames / capabilities | Нет | нет |
> | domainnames / internalloadbalancers | Нет | нет |
> | domainnames / servicecertificates | Нет | нет |
> | domainnames / slots | Нет | нет |
> | domainnames / slots / roles | Нет | Нет |
> | domainnames / slots / roles / metricdefinitions | Нет | нет |
> | domainnames / slots / roles / metrics | Нет | нет |
> | movesubscriptionresources | Нет | нет |
> | operatingsystemfamilies | Нет | Нет |
> | operatingsystems | Нет | Нет |
> | Операции | Нет | нет |
> | operationstatuses | Нет | Нет |
> | quotas | Нет | Нет |
> | resourcetypes | Нет | нет |
> | validatesubscriptionmoveavailability | Нет | нет |
> | virtualmachines | Нет | нет |
> | virtualmachines / diagnosticsettings | Нет | нет |
> | virtualmachines / metricdefinitions | Нет | нет |
> | virtualmachines / metrics | Нет | нет |

> [!IMPORTANT]
> См. [Руководство по перемещению классического развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Нет | нет |

> [!IMPORTANT]
> См. [Руководство по перемещению классического развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | capabilities | Нет | Нет |
> | expressroutecrossconnections | Нет | Нет |
> | expressroutecrossconnections / peerings | Нет | нет |
> | gatewaysupporteddevices | Нет | нет |
> | networksecuritygroups | Нет | нет |
> | Операции | Нет | нет |
> | quotas | Нет | нет |
> | reservedips | Нет | нет |
> | virtualnetworks | Нет | нет |
> | virtualnetworks / remotevirtualnetworkpeeringproxies | Нет | нет |
> | virtualnetworks / virtualnetworkpeerings | Нет | нет |

> [!IMPORTANT]
> См. [Руководство по перемещению классического развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | capabilities | Нет | нет |
> | checkstorageaccountavailability | Нет | нет |
> | disks | Нет | нет |
> | images | Нет | нет |
> | Операции | Нет | нет |
> | osimages | Нет | нет |
> | osplatformimages | Нет | нет |
> | publicimages | Нет | нет |
> | quotas | Нет | нет |
> | storageaccounts | Нет | нет |
> | storageaccounts / blobservices | Нет | нет |
> | storageaccounts / fileservices | Нет | Нет |
> | storageaccounts / metricdefinitions | Нет | Нет |
> | storageaccounts / metrics | Нет | нет |
> | storageaccounts / queueservices | Нет | Нет |
> | storageaccounts / services | Нет | Нет |
> | storageaccounts / services / diagnosticsettings | Нет | нет |
> | storageaccounts / services / metricdefinitions | Нет | Нет |
> | storageaccounts / services / metrics | Нет | Нет |
> | storageaccounts / tableservices | Нет | Нет |
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
> | accounts | нет | нет |
> | checkdomainavailability | нет | нет |
> | Расположения | нет | нет |
> | locations / checkskuavailability | Нет | Нет |
> | locations / deletevirtualnetworkorsubnets | Нет | Нет |
> | locations / operationresults | Нет | нет |
> | Операции | Нет | нет |

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
> | клаудсервицес | Нет | Нет |
> | diskaccesses | нет | Нет |
> | diskencryptionsets | нет | Нет |
> | disks | Да | Да |
> | galleries | нет | Нет |
> | galleries / images | нет | Нет |
> | galleries / images / versions | нет | Нет |
> | hostgroups | нет | Нет |
> | hostgroups / hosts | нет | Нет |
> | images | Да | Да |
> | Расположения | нет | Нет |
> | locations / artifactpublishers | нет | Нет |
> | locations / capsoperations | нет | Нет |
> | locations / diskoperations | нет | Нет |
> | locations / loganalytics | Нет | Нет |
> | locations / operations | нет | Нет |
> | locations / publishers | нет | Нет |
> | locations / runcommands | нет | Нет |
> | locations / usages | нет | Нет |
> | locations / virtualmachines | нет | Нет |
> | locations / vmsizes | Нет | Нет |
> | locations / vsmoperations | нет | Нет |
> | Операции | Нет | Нет |
> | proximityplacementgroups | Да | Да |
> | restorepointcollections | нет | Нет |
> | restorepointcollections / restorepoints | нет | Нет |
> | sharedvmextensions | Нет | Нет |
> | sharedvmimages | нет | Нет |
> | sharedvmimages / versions | нет | Нет |
> | snapshots | Да | Да |
> | sshpublickeys | нет | Нет |
> | свифтлетс | нет | Нет |
> | virtualmachines | Да | Да |
> | virtualmachines / extensions | Да | Да |
> | virtualmachines / metricdefinitions | Нет | нет |
> | virtualmachines / runcommands | Да | Да |
> | virtualmachinescalesets | Да | Да |
> | virtualmachinescalesets / extensions | Нет | нет |
> | virtualmachinescalesets / networkinterfaces | Нет | нет |
> | virtualmachinescalesets / publicipaddresses | Нет | нет |
> | virtualmachinescalesets / virtualmachines | Нет | нет |
> | virtualmachinescalesets / virtualmachines / networkinterfaces | Нет | нет |

> [!IMPORTANT]
> См. [Руководство по перемещению виртуальных машин](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconnectedcache"></a>Microsoft. Коннектедкаче

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | каченодес | нет | Нет |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Нет | нет |
> | balances | Нет | нет |
> | budgets | Нет | нет |
> | charges | Нет | нет |
> | costtags | Нет | нет |
> | credits | Нет | нет |
> | события | Нет | нет |
> | forecasts | Нет | нет |
> | lots | Нет | нет |
> | marketplaces | Нет | нет |
> | operationresults | Нет | нет |
> | Операции | Нет | Нет |
> | operationstatus | Нет | Нет |
> | pricesheets | нет | Нет |
> | products | Нет | Нет |
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
> | containergroups | нет | нет |
> | Расположения | нет | нет |
> | locations / cachedimages | нет | нет |
> | locations / capabilities | нет | нет |
> | locations / deletevirtualnetworkorsubnets | нет | нет |
> | locations / operations | нет | нет |
> | locations / usages | нет | нет |
> | Операции | Нет | Нет |
> | serviceassociationlinks | Нет | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | Нет |
> | Расположения | нет | Нет |
> | locations / authorize | нет | Нет |
> | locations / deletevirtualnetworkorsubnets | Нет | Нет |
> | locations / operationresults | нет | Нет |
> | locations / setupauth | нет | Нет |
> | Операции | нет | Нет |
> | registries | Да | Да |
> | registries / agentpools | нет | Нет |
> | registries / agentpools / listqueuestatus | нет | Нет |
> | registries / builds | нет | Нет |
> | registries / builds / cancel | нет | Нет |
> | registries / builds / getloglink | нет | Нет |
> | registries / buildtasks | нет | Нет |
> | registries / buildtasks / listsourcerepositoryproperties | нет | Нет |
> | registries / buildtasks / steps | нет | Нет |
> | registries / buildtasks / steps / listbuildarguments | нет | Нет |
> | registries / eventgridfilters | нет | Нет |
> | registries / exportpipelines | нет | Нет |
> | registries / generatecredentials | нет | Нет |
> | registries / getbuildsourceuploadurl | нет | Нет |
> | registries / getcredentials | нет | Нет |
> | registries / importimage | нет | Нет |
> | registries / importpipelines | нет | Нет |
> | registries / listbuildsourceuploadurl | нет | Нет |
> | registries / listcredentials | нет | Нет |
> | registries / listpolicies | Нет | Нет |
> | registries / listusages | нет | Нет |
> | registries / pipelineruns | нет | Нет |
> | registries / privateendpointconnectionproxies | нет | Нет |
> | registries / privateendpointconnectionproxies / validate | нет | Нет |
> | registries / privateendpointconnections | нет | Нет |
> | registries / privatelinkresources | нет | Нет |
> | registries / queuebuild | нет | Нет |
> | registries / regeneratecredential | Нет | Нет |
> | registries / regeneratecredentials | нет | Нет |
> | registries / replications | нет | Нет |
> | registries / runs | нет | Нет |
> | registries / runs / cancel | Нет | Нет |
> | registries / runs / listlogsasurl | нет | Нет |
> | registries / schedulerun | нет | Нет |
> | registries / scopemaps | нет | Нет |
> | registries / taskruns | Нет | Нет |
> | registries / taskruns / listdetails | нет | нет |
> | registries / tasks | нет | нет |
> | registries / tasks / listdetails | нет | нет |
> | registries / tokens | нет | нет |
> | registries / updatepolicies | нет | нет |
> | registries / webhooks | нет | нет |
> | registries / webhooks / getcallbackconfig | нет | нет |
> | registries / webhooks / listevents | нет | нет |
> | registries / webhooks / ping | нет | нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | containerservices | нет | нет |
> | Расположения | Нет | Нет |
> | locations / openshiftclusters | Нет | нет |
> | locations / operationresults | Нет | Нет |
> | locations / operations | Нет | Нет |
> | locations / orchestrators | нет | Нет |
> | managedclusters | нет | Нет |
> | openshiftmanagedclusters | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftcontentmoderator"></a>NNTP. контентмодератор

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | Нет | нет |

## <a name="microsoftcortanaanalytics"></a>NNTP. кортанааналитикс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | оповещения | нет | Нет |
> | billingaccounts | нет | Нет |
> | budgets | нет | Нет |
> | cloudconnectors | нет | Нет |
> | соединители | нет | Нет |
> | косталлокатионрулес | нет | Нет |
> | departments | нет | Нет |
> | dimensions | нет | Нет |
> | enrollmentaccounts | нет | Нет |
> | exports | нет | Нет |
> | externalbillingaccounts | нет | Нет |
> | externalbillingaccounts / alerts | нет | Нет |
> | externalbillingaccounts / dimensions | нет | Нет |
> | externalbillingaccounts / forecast | нет | Нет |
> | externalbillingaccounts / query | нет | Нет |
> | externalsubscriptions | нет | Нет |
> | externalsubscriptions / alerts | нет | Нет |
> | externalsubscriptions / dimensions | нет | Нет |
> | externalsubscriptions / forecast | Нет | Нет |
> | externalsubscriptions / query | нет | нет |
> | прогноз | Нет | Нет |
> | Операции | Нет | нет |
> | query | Нет | Нет |
> | регистрация | Нет | Нет |
> | reportconfigs | нет | Нет |
> | reports | нет | Нет |
> | Параметры | Нет | Нет |
> | showbackrules | нет | нет |
> | узел "Представления" | нет | нет |

## <a name="microsoftcostmanagementexports"></a>Microsoft.CostManagementExports

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | нет | Нет |

## <a name="microsoftcustomerinsights"></a>NNTP. кустомеринсигхтс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | hubs | Нет | Нет |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | Нет | нет |
> | requests | Нет | нет |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | associations | нет | нет |
> | Расположения | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | нет |
> | resourceproviders | Нет | Нет |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | jobs | нет | нет |
> | Расположения | нет | нет |
> | locations / availableskus | Нет | Нет |
> | locations / checknameavailability | Нет | нет |
> | locations / operationresults | Нет | нет |
> | locations / regionconfiguration | Нет | нет |
> | locations / validateaddress | Нет | нет |
> | locations / validateinputs | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | аваилаблескус | Нет | Нет |
> | databoxedgedevices | Да | Да |
> | databoxedgedevices / checknameavailability | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / getnetworkpolicies | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | Нет | Нет |
> | workspaces | Нет | нет |
> | workspaces / dbworkspaces | Нет | Нет |
> | workspaces / virtualnetworkpeerings | Нет | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | catalogs | Нет | Нет |
> | checknameavailability | Нет | Нет |
> | datacatalogs | нет | Нет |
> | Расположения | нет | Нет |
> | locations / jobs | Нет | Нет |
> | locations / operationresults | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftdataconnect"></a>NNTP. СОЕДИНЕНИЕ

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Нет | нет |

## <a name="microsoftdataexchange"></a>NNTP. DATAEXCHANGE

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Пакеты | нет | нет |
> | Планы | нет | нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynameavailability | Нет | Нет |
> | checkdatafactorynameavailability | нет | нет |
> | datafactories | Нет | Нет |
> | datafactories / diagnosticsettings | Нет | Нет |
> | datafactories / metricdefinitions | Нет | нет |
> | datafactoryschema | Нет | нет |
> | factories | Да | Да |
> | factories / integrationruntimes | Нет | нет |
> | Расположения | Нет | нет |
> | locations / configurefactoryrepo | Нет | нет |
> | locations / getfeaturevalue | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftdatalake"></a>NNTP. DATALAKE

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | нет | нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |
> | accounts / datalakestoreaccounts | нет | нет |
> | accounts / storageaccounts | нет | нет |
> | accounts / storageaccounts / containers | нет | нет |
> | accounts / storageaccounts / containers / listsastokens | нет | нет |
> | Расположения | нет | нет |
> | locations / capability | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / usages | нет | нет |
> | Операции | Нет | Нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |
> | accounts / eventgridfilters | нет | нет |
> | accounts / firewallrules | нет | нет |
> | Расположения | нет | нет |
> | locations / capability | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / deletevirtualnetworkorsubnets | нет | нет |
> | locations / operationresults | Нет | Нет |
> | locations / usages | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | Нет |
> | locations / checknameavailability | Нет | Нет |
> | locations / operationresults | Нет | нет |
> | locations / operationstatuses | Нет | нет |
> | Операции | Нет | нет |
> | services; | Нет | нет |
> | services / projects | Нет | нет |
> | slots | Нет | нет |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | backupvaults | нет | нет |
> | Расположения | нет | нет |
> | Операции | нет | нет |
> | ресаурцеоператионгатекиперс | нет | нет |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |
> | accounts / shares | нет | Нет |
> | accounts / shares / datasets | нет | Нет |
> | accounts / shares / invitations | Нет | Нет |
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
> | checknameavailability | нет | Нет |
> | Расположения | нет | Нет |
> | locations / azureasyncoperation | нет | Нет |
> | locations / operationresults | Нет | Нет |
> | locations / performancetiers | нет | Нет |
> | locations / privateendpointconnectionazureasyncoperation | нет | Нет |
> | locations / privateendpointconnectionoperationresults | нет | Нет |
> | locations / privateendpointconnectionproxyazureasyncoperation | нет | Нет |
> | locations / privateendpointconnectionproxyoperationresults | нет | Нет |
> | locations / recommendedactionsessionsazureasyncoperation | нет | Нет |
> | locations / recommendedactionsessionsoperationresults | нет | Нет |
> | locations / securityalertpoliciesazureasyncoperation | нет | Нет |
> | locations / securityalertpoliciesoperationresults | нет | Нет |
> | locations / serverkeyazureasyncoperation | Нет | Нет |
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
> | checknameavailability | нет | Нет |
> | Расположения | нет | Нет |
> | locations / administratorazureasyncoperation | нет | Нет |
> | locations / administratoroperationresults | нет | Нет |
> | locations / azureasyncoperation | нет | Нет |
> | locations / operationresults | Нет | Нет |
> | locations / performancetiers | нет | Нет |
> | locations / privateendpointconnectionazureasyncoperation | нет | Нет |
> | locations / privateendpointconnectionoperationresults | нет | Нет |
> | locations / privateendpointconnectionproxyazureasyncoperation | нет | Нет |
> | locations / privateendpointconnectionproxyoperationresults | нет | Нет |
> | locations / recommendedactionsessionsazureasyncoperation | нет | Нет |
> | locations / recommendedactionsessionsoperationresults | нет | Нет |
> | locations / securityalertpoliciesazureasyncoperation | нет | Нет |
> | locations / securityalertpoliciesoperationresults | нет | Нет |
> | locations / serverkeyazureasyncoperation | нет | Нет |
> | locations / serverkeyoperationresults | Нет | Нет |
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
> | checknameavailability | нет | Нет |
> | Расположения | нет | Нет |
> | locations / administratorazureasyncoperation | нет | Нет |
> | locations / administratoroperationresults | нет | Нет |
> | locations / azureasyncoperation | нет | Нет |
> | locations / operationresults | нет | Нет |
> | locations / performancetiers | Нет | Нет |
> | locations / privateendpointconnectionazureasyncoperation | нет | Нет |
> | locations / privateendpointconnectionoperationresults | нет | Нет |
> | locations / privateendpointconnectionproxyazureasyncoperation | нет | Нет |
> | locations / privateendpointconnectionproxyoperationresults | нет | Нет |
> | locations / recommendedactionsessionsazureasyncoperation | нет | Нет |
> | locations / recommendedactionsessionsoperationresults | нет | Нет |
> | locations / securityalertpoliciesazureasyncoperation | нет | Нет |
> | locations / securityalertpoliciesoperationresults | нет | Нет |
> | locations / serverkeyazureasyncoperation | нет | Нет |
> | locations / serverkeyoperationresults | нет | Нет |
> | Операции | Нет | Нет |
> | servergroups | Нет | Нет |
> | servers | Да | Да |
> | servers / advisors | Нет | Нет |
> | servers / keys | нет | нет |
> | servers / privateendpointconnectionproxies | нет | нет |
> | servers / privateendpointconnections | Нет | Нет |
> | servers / privatelinkresources | Нет | Нет |
> | servers / querytexts | Нет | Нет |
> | servers / recoverableservers | Нет | Нет |
> | servers / topquerystatistics | Нет | Нет |
> | servers / virtualnetworkrules | Нет | Нет |
> | servers / waitstatistics | Нет | Нет |
> | serversv2 | Да | Да |
> | singleservers | Да | Да |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | artifactsources | Нет | Нет |
> | operationresults | нет | нет |
> | Операции | нет | нет |
> | rollouts | нет | нет |
> | servicetopologies | нет | нет |
> | servicetopologies / services | Нет | Нет |
> | servicetopologies / services / serviceunits | Нет | Нет |
> | steps | Нет | нет |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | applicationgroups | нет | нет |
> | applicationgroups / applications | нет | нет |
> | applicationgroups / desktops | Нет | Нет |
> | applicationgroups / startmenuitems | нет | нет |
> | hostpools | нет | нет |
> | hostpools / sessionhosts | нет | нет |
> | hostpools / sessionhosts / usersessions | Нет | Нет |
> | hostpools / usersessions | нет | нет |
> | Операции | Нет | Нет |
> | workspaces | Нет | Нет |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | Нет |
> | checkprovisioningservicenameavailability | Нет | нет |
> | elasticpools | Да | Да |
> | elasticpools / iothubtenants | Да | Да |
> | iothubs | Да | Да |
> | iothubs / eventgridfilters | Нет | нет |
> | iothubs/SecuritySettings | Нет | Нет |
> | operationresults | Нет | Нет |
> | Операции | нет | Нет |
> | provisioningservices | Да | Да |
> | usages | Нет | Нет |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | pipelines | Нет | нет |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | controllers | нет | нет |
> | controllers / listconnectiondetails | Нет | Нет |
> | Расположения | Нет | Нет |
> | locations / checkcontainerhostmapping | Нет | нет |
> | locations / operationresults | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | labcenters | Нет | нет |
> | labs | Нет | Нет |
> | labs / environments | Нет | нет |
> | labs / servicerunners | Нет | нет |
> | labs / virtualmachines | Нет | нет |
> | Расположения | Нет | нет |
> | locations / operations | Нет | нет |
> | Операции | Нет | нет |
> | schedules | Нет | Нет |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Нет | Нет |
> | дигиталтвинсинстанцес/конечные точки | Нет | нет |
> | дигиталтвинсинстанцес/оператионресултс | Нет | нет |
> | Расположения | Нет | нет |
> | locations / checknameavailability | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Нет | Нет |
> | databaseaccounts | Да | Да |
> | Расположения | Нет | нет |
> | locations / deletevirtualnetworkorsubnets | Нет | нет |
> | locations / operationresults | Нет | Нет |
> | locations / operationsstatus | Нет | Нет |
> | operationresults | Нет | Нет |
> | Операции | Нет | Нет |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | Нет | Нет |
> | domains | Да | Да |
> | domains / domainownershipidentifiers | Нет | нет |
> | generatessorequest | Нет | нет |
> | listdomainrecommendations | Нет | нет |
> | Операции | Нет | нет |
> | topleveldomains | Нет | нет |
> | validatedomainregistrationinformation | Нет | нет |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / operationresults | Нет | Нет |
> | Операции | нет | нет |
> | services; | нет | нет |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | domains | нет | Нет |
> | domains / topics | Нет | Нет |
> | eventsubscriptions | Нет — нельзя перемещать независимо, но автоматически перемещается вместе с подписанным ресурсом. | Нет — нельзя перемещать независимо, но автоматически перемещается вместе с подписанным ресурсом. |
> | extensiontopics | Нет | Нет |
> | Расположения | нет | Нет |
> | locations / eventsubscriptions | Нет | Нет |
> | locations / operationresults | нет | нет |
> | locations / operationsstatus | нет | нет |
> | locations / topictypes | нет | нет |
> | operationresults | Нет | Нет |
> | Операции | нет | нет |
> | operationsstatus | нет | нет |
> | partnernamespaces | Нет | Нет |
> | partnernamespaces / eventchannels | нет | нет |
> | partnerregistrations | нет | нет |
> | partnertopics | нет | нет |
> | partnertopics / eventsubscriptions | нет | нет |
> | systemtopics | нет | нет |
> | systemtopics / eventsubscriptions | нет | нет |
> | topics | нет | нет |
> | topictypes | нет | нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | Нет | Нет |
> | checknameavailability | нет | нет |
> | checknamespaceavailability | нет | нет |
> | clusters | нет | нет |
> | Расположения | Нет | Нет |
> | locations / deletevirtualnetworkorsubnets | Нет | Нет |
> | пространства имен | Нет | Нет |
> | namespaces / authorizationrules | Нет | Нет |
> | namespaces / disasterrecoveryconfigs | нет | Нет |
> | namespaces / disasterrecoveryconfigs / checknameavailability | нет | Нет |
> | namespaces / eventhubs | нет | Нет |
> | namespaces / eventhubs / authorizationrules | нет | Нет |
> | namespaces / eventhubs / consumergroups | нет | Нет |
> | namespaces / networkrulesets | Нет | Нет |
> | Операции | нет | нет |
> | sku | Нет | Нет |

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
> | пространства имен | нет | Нет |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | featureproviders | Нет | Нет |
> | features | нет | Нет |
> | Операции | нет | Нет |
> | providers | нет | Нет |
> | subscriptionfeatureregistrations | нет | Нет |

## <a name="microsoftgenomics"></a>NNTP. GENOMICS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Нет | нет |
> | automanagedvmconfigurationprofiles | Нет | нет |
> | guestconfigurationassignments | Нет | Нет |
> | Операции | Нет | Нет |
> | software | Нет | Нет |
> | softwareupdateprofile | нет | Нет |
> | softwareupdates | нет | Нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | hanainstances | Нет | нет |
> | Расположения | Нет | нет |
> | locations / operations | Нет | нет |
> | locations / operationsstatus | Нет | нет |
> | Операции | Нет | нет |
> | sapmonitors | Нет | нет |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | Нет | Нет |
> | Расположения | Нет | Нет |
> | Операции | нет | нет |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | clusters | Да | Да |
> | clusters / applications | нет | Нет |
> | clusters / operationresults | Нет | Нет |
> | Расположения | нет | Нет |
> | locations / azureasyncoperations | нет | Нет |
> | locations / billingspecs | Нет | Нет |
> | locations / capabilities | Нет | Нет |
> | locations / operationresults | нет | нет |
> | locations / usages | нет | нет |
> | locations / validatecreaterequest | Нет | Нет |
> | Операции | Нет | Нет |

> [!IMPORTANT]
> Кластеры HDInsight можно переместить в новую подписку или группу ресурсов. Однако перемещение между подписками недоступно для сетевых ресурсов, связанных с кластером HDInsight (таких как виртуальная сеть, сетевой адаптер или подсистема балансировки нагрузки). Также невозможно переместить в новую группу ресурсов сетевой адаптер, который подключен к виртуальной машине кластера.
>
> При перемещении кластера HDInsight в новую подписку сначала необходимо переместить другие ресурсы (такие как учетная запись хранения). А затем переместить сам кластер HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | Нет |
> | Расположения | нет | Нет |
> | locations / operationresults | нет | Нет |
> | Операции | нет | Нет |
> | services; | Нет | Нет |
> | services / privateendpointconnections | Нет | Нет |
> | services / privatelinkresources | нет | нет |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | Нет |
> | locations / operationresults | Нет | Нет |
> | locations / operationstatus | Нет | Нет |
> | machines | нет | нет |
> | machines / extensions | Да | Да |
> | Операции | Нет | Нет |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | datamanagers | нет | Нет |
> | Операции | Нет | Нет |

## <a name="microsofthybridnetwork"></a>Microsoft. Хибриднетворк

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | устройства | Нет | нет |
> | Расположения | Нет | нет |
> | locations / operationstatuses | Нет | нет |
> | Операции | Нет | нет |
> | vnfs | Нет | нет |

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
> | Расположения | нет | Нет |
> | locations / operationresults | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | actiongroups | Нет | нет |
> | activitylogalerts | Нет | нет |
> | alertrules | Да | Да |
> | autoscalesettings | Да | Да |
> | baseline | Нет | нет |
> | calculatebaseline | Нет | нет |
> | components | Да | Да |
> | components / events | Нет | нет |
> | components / linkedstorageaccounts | Нет | нет |
> | компоненты и метаданные | Нет | нет |
> | components / metrics | Нет | нет |
> | components / pricingplans | Нет | нет |
> | components / query | Нет | нет |
> | datacollectionrules | Нет | нет |
> | diagnosticsettings | Нет | нет |
> | diagnosticsettingscategories | Нет | нет |
> | eventcategories | Нет | Нет |
> | eventtypes | Нет | нет |
> | extendeddiagnosticsettings | Нет | нет |
> | guestdiagnosticsettings | Нет | нет |
> | listmigrationdate | Нет | Нет |
> | Расположения | Нет | Нет |
> | locations / operationresults | Нет | Нет |
> | logdefinitions | Нет | Нет |
> | logprofiles | Нет | Нет |
> | журналы | нет | нет |
> | metricalerts | нет | нет |
> | metricbaselines | нет | нет |
> | metricbatch | Нет | Нет |
> | metricdefinitions | нет | нет |
> | metricnamespaces | Нет | Нет |
> | Метрики | Нет | Нет |
> | migratealertrules | Нет | Нет |
> | migratetonewpricingmodel | Нет | нет |
> | myworkbooks | Нет | Нет |
> | notificationgroups | Нет | Нет |
> | Операции | нет | Нет |
> | privatelinkscopeoperationstatuses | нет | Нет |
> | privatelinkscopes | нет | Нет |
> | privatelinkscopes / privateendpointconnectionproxies | нет | Нет |
> | privatelinkscopes / privateendpointconnections | нет | Нет |
> | privatelinkscopes / scopedresources | нет | Нет |
> | rollbacktolegacypricingmodel | нет | Нет |
> | scheduledqueryrules | Да | Да |
> | Топология | Нет | Нет |
> | транзакции | нет | Нет |
> | vminsightsonboardingstatuses | нет | Нет |
> | webtests | Да | Да |
> | веб-тесты и жеттестресултфиле | Нет | Нет |
> | workbooks | Нет | Нет |
> | workbooktemplates | Нет | нет |

> [!IMPORTANT]
> Убедитесь, что переход на новую подписку не превышает [квоты для подписки](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | apptemplates | нет | Нет |
> | checknameavailability | нет | Нет |
> | checksubdomainavailability | Нет | Нет |
> | iotapps | нет | нет |
> | Операции | Нет | Нет |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | graph | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | Нет |
> | deletedvaults | нет | Нет |
> | hsmpools | Да | Да |
> | Расположения | нет | Нет |
> | locations / deletedvaults | нет | Нет |
> | locations / deletevirtualnetworkorsubnets | нет | Нет |
> | locations / operationresults | Нет | Нет |
> | манажедхсмс | нет | нет |
> | Операции | нет | нет |
> | vaults | Да | Да |
> | vaults / accesspolicies | нет | нет |
> | vaults / eventgridfilters | нет | нет |
> | vaults / secrets | Нет | Нет |

> [!IMPORTANT]
> Хранилища ключей, используемые для шифрования дисков, невозможно переместить в группы ресурсов в той же подписке или между подписками.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Нет | Нет |
> | Расположения | нет | нет |
> | locations / operationstatuses | Нет | Нет |
> | Операции | Нет | нет |
> | registeredsubscriptions | Нет | Нет |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Нет | нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | clusters | нет | Нет |
> | clusters / attacheddatabaseconfigurations | нет | Нет |
> | clusters / databases | нет | Нет |
> | clusters / databases / dataconnections | Нет | Нет |
> | clusters / databases / eventhubconnections | Нет | Нет |
> | clusters / databases / principalassignments | нет | нет |
> | clusters / principalassignments | нет | нет |
> | Расположения | нет | нет |
> | locations / checknameavailability | нет | нет |
> | locations / operationresults | нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | labaccounts | Нет | Нет |
> | Расположения | нет | нет |
> | locations / operations | Нет | Нет |
> | Операции | Нет | нет |
> | users | Нет | нет |

## <a name="microsoftlocationbasedservices"></a>NNTP. локатионбаседсервицес

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftlocationservices"></a>NNTP. ФАЙЛ LOCATIONSERVICES

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | нет | Нет |
> | integrationaccounts | Да | Да |
> | integrationserviceenvironments | нет | нет |
> | integrationserviceenvironments / managedapis | Нет | Нет |
> | isolatedenvironments | Нет | нет |
> | Расположения | Нет | нет |
> | locations / workflows | Нет | нет |
> | Операции | Нет | нет |
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
> | webservices | Нет | Нет |
> | workspaces | Нет | нет |

## <a name="microsoftmachinelearningcompute"></a>NNTP. мачинелеарнингкомпуте

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Нет | Нет |

## <a name="microsoftmachinelearningexperimentation"></a>NNTP. мачинелеарнинжекспериментатион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |
> | accounts / workspaces | нет | нет |
> | accounts / workspaces / projects | нет | нет |
> | teamaccounts | нет | нет |
> | teamaccounts / workspaces | Нет | Нет |
> | teamaccounts / workspaces / projects | Нет | нет |

## <a name="microsoftmachinelearningmodelmanagement"></a>NNTP. мачинелеарнингмоделманажемент

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | Нет |
> | locations / computeoperationsstatus | нет | нет |
> | locations / quotas | нет | нет |
> | locations / updatequotas | нет | нет |
> | locations / usages | нет | нет |
> | locations / vmsizes | нет | нет |
> | locations / workspaceoperationsstatus | Нет | Нет |
> | Операции | Нет | нет |
> | workspaces | Нет | нет |
> | workspaces / computes | Нет | нет |
> | workspaces / eventgridfilters | Нет | нет |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | applyupdates | нет | нет |
> | configurationassignments | нет | нет |
> | maintenanceconfigurations | нет | нет |
> | updates | нет | нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | удостоверения; | Нет | Нет |
> | Операции | Нет | Нет |
> | userassignedidentities | нет | нет |

## <a name="microsoftmanagednetwork"></a>NNTP. манажеднетворк

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | managednetworks | нет | Нет |
> | managednetworks / managednetworkgroups | Нет | Нет |
> | managednetworks / managednetworkpeeringpolicies | нет | нет |
> | уведомление | нет | нет |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | нет | Нет |
> | Операции | нет | Нет |
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
> | operationresults / asyncoperation | Нет | нет |
> | Операции | Нет | нет |
> | ресурсов | Нет | нет |
> | starttenantbackfill | Нет | нет |
> | tenantbackfillstatus | Нет | Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | нет |
> | accounts / eventgridfilters | Нет | нет |
> | accounts / privateatlases | Нет | нет |
> | Операции | Нет | Нет |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | Нет | нет |
> | offers | Нет | нет |
> | offertypes | Нет | Нет |
> | offertypes / publishers | Нет | Нет |
> | offertypes / publishers / offers | нет | Нет |
> | offertypes / publishers / offers / plans | нет | Нет |
> | offertypes / publishers / offers / plans / agreements | нет | Нет |
> | offertypes / publishers / offers / plans / configs | Нет | Нет |
> | offertypes / publishers / offers / plans / configs / importimage | нет | Нет |
> | Операции | нет | Нет |
> | privategalleryitems | нет | Нет |
> | privatestoreclient | нет | Нет |
> | privatestores | нет | Нет |
> | privatestores / offers | нет | Нет |
> | products | Нет | Нет |
> | publishers | нет | Нет |
> | publishers / offers | нет | Нет |
> | publishers / offers / amendments | нет | Нет |
> | регистрация | Нет | Нет |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Нет | нет |
> | listcommunicationpreference | Нет | нет |
> | Операции | Нет | нет |
> | updatecommunicationpreference | Нет | Нет |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | agreements | Нет | Нет |
> | offertypes | Нет | Нет |
> | Операции | Нет | Нет |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | нет |
> | Расположения | Нет | Нет |
> | locations / checknameavailability | Нет | нет |
> | mediaservices | Нет | нет |
> | mediaservices / accountfilters | Нет | нет |
> | mediaservices / assets | Нет | Нет |
> | mediaservices / assets / assetfilters | Нет | Нет |
> | mediaservices / contentkeypolicies | нет | Нет |
> | mediaservices / eventgridfilters | нет | Нет |
> | mediaservices / liveeventoperations | нет | Нет |
> | mediaservices / liveevents | нет | Нет |
> | mediaservices / liveevents / liveoutputs | нет | Нет |
> | mediaservices / liveoutputoperations | Нет | Нет |
> | mediaservices / streamingendpointoperations | Нет | Нет |
> | mediaservices / streamingendpoints | Нет | Нет |
> | mediaservices / streaminglocators | нет | нет |
> | mediaservices / streamingpolicies | нет | нет |
> | mediaservices / transforms | нет | нет |
> | mediaservices / transforms / jobs | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftmicroservices4spring"></a>NNTP. MICROSERVICES4SPRING

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | appclusters | нет | Нет |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | нет | Нет |
> | Расположения | нет | Нет |
> | locations / assessmentoptions | нет | Нет |
> | locations / checknameavailability | нет | Нет |
> | migrateprojects | нет | Нет |
> | movecollections | нет | Нет |
> | Операции | нет | Нет |
> | projects | Нет | Нет |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Нет | нет |
> | Расположения | Нет | нет |
> | locations / checknameavailability | Нет | нет |
> | objectunderstandingaccounts | Нет | нет |
> | Операции | Нет | Нет |
> | remoterenderingaccounts | Нет | Нет |
> | spatialanchorsaccounts | Нет | нет |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | netappaccounts | нет | нет |
> | netappaccounts / backuppolicies | Нет | Нет |
> | netappaccounts / capacitypools | нет | нет |
> | netappaccounts / capacitypools / volumes | нет | нет |
> | netappaccounts / capacitypools / volumes / mounttargets | нет | нет |
> | netappaccounts / capacitypools / volumes / snapshots | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | нет | Нет |
> | applicationgatewayavailableresponseheaders | нет | Нет |
> | applicationgatewayavailableservervariables | нет | Нет |
> | applicationgatewayavailablessloptions | нет | Нет |
> | applicationgatewayavailablewafrulesets | нет | Нет |
> | applicationgateways | нет | Нет |
> | applicationgatewaywebapplicationfirewallpolicies | нет | Нет |
> | applicationsecuritygroups | нет | Нет |
> | azurefirewallfqdntags | нет | Нет |
> | azurefirewalls | Нет | Нет |
> | bastionhosts | нет | Нет |
> | bgpservicecommunities | нет | Нет |
> | checkfrontdoornameavailability | нет | Нет |
> | checktrafficmanagernameavailability | нет | Нет |
> | connections | нет | Нет |
> | ddoscustompolicies | нет | Нет |
> | ddosprotectionplans | Нет | Нет |
> | dnsoperationresults | Нет | Нет |
> | dnsoperationstatuses | Нет | Нет |
> | dnszones | Нет | нет |
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
> | dnszones / srv | Нет | нет |
> | dnszones / txt | Нет | нет |
> | expressroutecircuits | Нет | нет |
> | expressroutegateways | Нет | нет |
> | expressrouteserviceproviders | Нет | нет |
> | firewallpolicies | Нет | нет |
> | frontdooroperationresults | Нет | нет |
> | frontdoors | Нет | нет |
> | фронтдурс/фронтендендпоинтс | Нет | нет |
> | frontdoorwebapplicationfirewallmanagedrulesets | Нет | нет |
> | frontdoorwebapplicationfirewallpolicies | Нет | нет |
> | getdnsresourcereference | Нет | нет |
> | internalnotify | Нет | Нет |
> | ипаллокатионс | Нет | нет |
> | ipgroups | Нет | Нет |
> | loadbalancers | Да — Базовый SKU<br>Нет — Стандартный SKU | Да — Базовый SKU<br>Нет — Стандартный SKU |
> | localnetworkgateways | нет | Нет |
> | Расположения | Нет | Нет |
> | locations / autoapprovedprivatelinkservices | нет | Нет |
> | locations / availabledelegations | нет | Нет |
> | locations / availableprivateendpointtypes | нет | Нет |
> | locations / availableservicealiases | нет | Нет |
> | locations / baremetaltenants | нет | Нет |
> | locations / batchnotifyprivateendpointsforresourcemove | нет | Нет |
> | locations / batchvalidateprivateendpointsforresourcemove | нет | Нет |
> | locations / checkacceleratednetworkingsupport | нет | Нет |
> | locations / checkdnsnameavailability | Нет | Нет |
> | locations / checkprivatelinkservicevisibility | нет | Нет |
> | locations / commitinternalazurenetworkmanagerconfiguration | нет | Нет |
> | locations / effectiveresourceownership | нет | Нет |
> | locations / nfvoperationresults | нет | Нет |
> | locations / nfvoperations | нет | Нет |
> | locations / operationresults | нет | Нет |
> | locations / operations | нет | Нет |
> | locations / servicetags | нет | Нет |
> | locations / setresourceownership | нет | Нет |
> | locations / supportedvirtualmachinesizes | Нет | Нет |
> | locations / usages | нет | Нет |
> | locations / validateresourceownership | нет | Нет |
> | locations / virtualnetworkavailableendpointservices | Нет | Нет |
> | natgateways | нет | Нет |
> | networkexperimentprofiles | Нет | Нет |
> | networkintentpolicies | Нет | Нет |
> | networkinterfaces | Нет | нет |
> | networkprofiles | Нет | Нет |
> | networksecuritygroups | Нет | Нет |
> | networkwatchers | Да | Нет |
> | networkwatchers / connectionmonitors | Да | нет |
> | networkwatchers / flowlogs | Да | Нет |
> | networkwatchers / pingmeshes | Да | нет |
> | Операции | Нет | нет |
> | p2svpngateways | Нет | нет |
> | privatednsoperationresults | Нет | Нет |
> | privatednsoperationstatuses | Нет | Нет |
> | privatednszones | Нет | нет |
> | privatednszones / a | Нет | Нет |
> | privatednszones / aaaa | Нет | нет |
> | privatednszones / all | Нет | нет |
> | privatednszones / cname | Нет | нет |
> | privatednszones / mx | Нет | нет |
> | privatednszones / ptr | Нет | Нет |
> | privatednszones / soa | Нет | Нет |
> | privatednszones / srv | нет | нет |
> | privatednszones / txt | нет | нет |
> | privatednszones / virtualnetworklinks | Нет | Нет |
> | privatednszonesinternal | Нет | Нет |
> | privateendpointredirectmaps | нет | нет |
> | privateendpoints | нет | нет |
> | privatelinkservices | Нет | Нет |
> | publicipaddresses | Да — Базовый SKU<br>Нет — Стандартный SKU | Да — Базовый SKU<br>Нет — Стандартный SKU |
> | publicipprefixes | Нет | Нет |
> | routefilters | Нет | Нет |
> | routetables | Нет | Нет |
> | securitypartnerproviders | нет | нет |
> | serviceendpointpolicies | Нет | Нет |
> | trafficmanagergeographichierarchies | Нет | Нет |
> | trafficmanagerprofiles | Нет | Нет |
> | trafficmanagerprofiles / heatmaps | Нет | нет |
> | trafficmanagerusermetricskeys | Нет | нет |
> | virtualhubs | Нет | нет |
> | virtualnetworkgateways | Нет | нет |
> | virtualnetworks | Нет | нет |
> | virtualnetworktaps | Нет | нет |
> | virtualrouters | Нет | нет |
> | virtualwans | Нет | Нет |
> | vpngateways (Virtual WAN) | Нет | нет |
> | vpnserverconfigurations | Нет | нет |
> | vpnsites (Virtual WAN) | Нет | нет |

> [!IMPORTANT]
> См. [руководство по перемещению сети](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | Нет |
> | checknamespaceavailability | нет | Нет |
> | пространства имен | Нет | Нет |
> | namespaces / notificationhubs | Нет | Нет |
> | operationresults | нет | нет |
> | Операции | Нет | Нет |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Нет | Нет |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | hypervsites | Нет | Нет |
> | importsites | нет | Нет |
> | Операции | нет | Нет |
> | serversites | нет | Нет |
> | vmwaresites | нет | Нет |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | clusters | Да | Да |
> | deletedworkspaces | Нет | нет |
> | linktargets | Нет | нет |
> | Расположения | Нет | нет |
> | locations / operationstatuses | Нет | Нет |
> | Операции | Нет | Нет |
> | storageinsightconfigs | нет | Нет |
> | workspaces | Да | Да |
> | workspaces / datasources | нет | Нет |
> | workspaces / linkedservices | нет | Нет |
> | workspaces / linkedstorageaccounts | нет | Нет |
> | workspaces / metadata | нет | Нет |
> | workspaces / query | Нет | Нет |
> | workspaces / scopedprivatelinkproxies | нет | нет |

> [!IMPORTANT]
> Убедитесь, что переход на новую подписку не превышает [квоты для подписки](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Рабочие области, имеющие связанную учетную запись автоматизации, нельзя перемещать. Прежде чем начать операцию перемещения, не забудьте удалить связь с учетными записями службы автоматизации.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | managementassociations | нет | Нет |
> | managementconfigurations | нет | Нет |
> | Операции | нет | Нет |
> | solutions | нет | Нет |
> | узел "Представления" | Нет | Нет |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | Нет | Нет |
> | legacypeerings | нет | нет |
> | Операции | нет | нет |
> | peerasns | Нет | Нет |
> | peeringlocations | Нет | Нет |
> | peerings | Нет | Нет |
> | peeringservicecountries | Нет | нет |
> | peeringservicelocations | Нет | нет |
> | peeringserviceproviders | Нет | нет |
> | peeringservices | Нет | нет |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | Нет | Нет |
> | Операции | Нет | нет |
> | policyevents | Нет | нет |
> | policystates | Нет | нет |
> | policytrackedresources | Нет | Нет |
> | remediations | Нет | Нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | consoles | Нет | Нет |
> | dashboards | нет | Нет |
> | Расположения | нет | Нет |
> | locations / consoles | нет | Нет |
> | locations / usersettings | нет | Нет |
> | Операции | Нет | Нет |
> | usersettings | нет | нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | Нет |
> | locations / checknameavailability | нет | Нет |
> | Операции | Нет | Нет |
> | привателинксервицесфорповерби | нет | нет |
> | привателинксервицесфорповерби/оператионресултс | нет | нет |
> | tenants | нет | нет |
> | workspacecollections | нет | нет |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | capacities | Да | Да |
> | Расположения | нет | Нет |
> | locations / checknameavailability | нет | Нет |
> | locations / operationresults | нет | Нет |
> | locations / operationstatuses | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftpowerplatform"></a>Microsoft. Поверплатформ

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Операции | Нет | нет |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |
> | checknameavailability | Нет | Нет |
> | Операции | Нет | Нет |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Нет | нет |
> | providerregistrations | Нет | нет |
> | провидеррегистратионс/дефаултроллаутс | Нет | нет |
> | providerregistrations / resourcetyperegistrations | Нет | нет |
> | rollouts | Нет | Нет |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | Нет |
> | locations / operationstatuses | Нет | нет |
> | Операции | Нет | нет |
> | workspaces | Нет | нет |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | нет | нет |
> | Расположения | нет | нет |
> | locations / allocatedstamp | нет | нет |
> | locations / allocatestamp | Нет | Нет |
> | locations / backupaadproperties | Нет | нет |
> | locations / backupcrossregionrestore | Нет | Нет |
> | locations / backupcrrjob | Нет | нет |
> | locations / backupcrrjobs | Нет | нет |
> | locations / backupcrroperationresults | Нет | нет |
> | locations / backupcrroperationsstatus | Нет | нет |
> | locations / backupprevalidateprotection | Нет | нет |
> | locations / backupstatus | Нет | Нет |
> | locations / backupvalidatefeatures | Нет | Нет |
> | locations / checknameavailability | нет | Нет |
> | Операции | нет | Нет |
> | replicationeligibilityresults | нет | Нет |
> | vaults | Да | Да |

> [!IMPORTANT]
> Ознакомьтесь с разделом [Руководство по перемещению служб восстановления](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operationsstatus | Нет | Нет |
> | openshiftclusters | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | пространства имен | нет | нет |
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
> | Операции | нет | Нет |
> | Запросы | Да | Да |
> | resourcechangedetails | нет | Нет |
> | resourcechanges | нет | Нет |
> | ресурсов | нет | Нет |
> | resourceshistory | нет | Нет |
> | subscriptionsstatus | нет | Нет |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Нет | нет |
> | childavailabilitystatuses | Нет | нет |
> | childresources | Нет | нет |
> | emergingissues | Нет | нет |
> | события | Нет | нет |
> | метаданные | Нет | нет |
> | Уведомления | Нет | Нет |
> | Операции | Нет | Нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | калкулатетемплатехаш | Нет | нет |
> | checkpolicycompliance | Нет | нет |
> | checkresourcename | Нет | нет |
> | deployments | Нет | нет |
> | deployments / operations | Нет | Нет |
> | deploymentscripts | Нет | Нет |
> | deploymentscripts / logs | нет | Нет |
> | ссылки | нет | Нет |
> | Расположения | нет | Нет |
> | locations / deploymentscriptoperationresults | нет | Нет |
> | notifyresourcejobs | Нет | Нет |
> | operationresults | Нет | Нет |
> | Операции | Нет | Нет |
> | providers | нет | Нет |
> | resourcegroups | нет | Нет |
> | ресурсов | нет | Нет |
> | subscriptions | нет | Нет |
> | subscriptions / locations | нет | Нет |
> | subscriptions / operationresults | нет | Нет |
> | subscriptions / providers | нет | Нет |
> | subscriptions / resourcegroups | нет | Нет |
> | subscriptions / resourcegroups / resources | Нет | Нет |
> | subscriptions / resources | нет | Нет |
> | subscriptions / tagnames | нет | Нет |
> | subscriptions / tagnames / tagvalues | нет | Нет |
> | tags | нет | Нет |
> | templatespecs | нет | Нет |
> | templatespecs / versions | нет | Нет |
> | tenants | нет | Нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | Нет | нет |
> | checkmoderneligibility | Нет | нет |
> | checknameavailability | Нет | нет |
> | operationresults | Нет | нет |
> | Операции | Нет | нет |
> | saasresources | Нет | нет |

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
> | алертссуппрессионрулес | нет | нет |
> | allowedconnections | нет | нет |
> | applicationwhitelistings | нет | нет |
> | assessmentmetadata | нет | нет |
> | assessments | нет | нет |
> | autodismissalertsrules | нет | нет |
> | automations | нет | нет |
> | autoprovisioningsettings | нет | нет |
> | complianceresults | нет | нет |
> | compliances | нет | нет |
> | datacollectionagents | нет | нет |
> | devicesecuritygroups | нет | нет |
> | discoveredsecuritysolutions | нет | нет |
> | externalsecuritysolutions | нет | нет |
> | informationprotectionpolicies | нет | нет |
> | iotsecuritysolutions | Нет | Нет |
> | iotsecuritysolutions / analyticsmodels | Нет | нет |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Нет | нет |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Нет | нет |
> | иотсекуритисолутионс/иоталертс | Нет | нет |
> | иотсекуритисолутионс/иоталерттипес | Нет | нет |
> | jitnetworkaccesspolicies | Нет | нет |
> | Расположения | Нет | нет |
> | locations / alerts | Нет | нет |
> | locations / allowedconnections | Нет | нет |
> | locations / applicationwhitelistings | Нет | нет |
> | locations / discoveredsecuritysolutions | Нет | нет |
> | locations / externalsecuritysolutions | Нет | нет |
> | locations / jitnetworkaccesspolicies | Нет | нет |
> | locations / securitysolutions | Нет | нет |
> | locations / securitysolutionsreferencedata | Нет | Нет |
> | locations / tasks | Нет | Нет |
> | locations / topologies | нет | Нет |
> | Операции | нет | Нет |
> | политики | нет | Нет |
> | pricings | нет | Нет |
> | regulatorycompliancestandards | Нет | Нет |
> | regulatorycompliancestandards / regulatorycompliancecontrols | нет | нет |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | нет | нет |
> | секурескореконтролдефинитионс | Нет | Нет |
> | секурескореконтролс | Нет | нет |
> | секурескорес | Нет | нет |
> | секурескорес/секурескореконтролс | Нет | нет |
> | securitycontacts | Нет | нет |
> | securitysolutions | Нет | Нет |
> | securitysolutionsreferencedata | Нет | нет |
> | securitystatuses | Нет | нет |
> | securitystatusessummaries | Нет | нет |
> | servervulnerabilityassessments | Нет | нет |
> | Параметры | Нет | нет |
> | subassessments | Нет | нет |
> | задачи | Нет | нет |
> | topologies | Нет | нет |
> | workspacesettings | Нет | нет |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | aggregations | нет | нет |
> | alertrules | нет | нет |
> | alertruletemplates | нет | нет |
> | аутоматионрулес | нет | нет |
> | bookmarks | Нет | Нет |
> | cases | Нет | нет |
> | dataconnectors | Нет | Нет |
> | dataconnectorscheckrequirements | Нет | нет |
> | Сущности | Нет | нет |
> | entityqueries | Нет | нет |
> | incidents | Нет | нет |
> | officeconsents | Нет | нет |
> | Операции | Нет | нет |
> | Параметры | Нет | нет |
> | threatintelligence | Нет | нет |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | consoleservices | нет | нет |
> | Расположения | нет | нет |
> | locations / consoleservices | нет | нет |
> | Операции | нет | нет |

## <a name="microsoftservermanagement"></a>NNTP. серверманажемент

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | gateways | Нет | Нет |
> | Узлы | Нет | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | Нет |
> | checknamespaceavailability | нет | Нет |
> | Расположения | нет | Нет |
> | locations / deletevirtualnetworkorsubnets | нет | Нет |
> | пространства имен | нет | Нет |
> | namespaces / authorizationrules | нет | Нет |
> | namespaces / disasterrecoveryconfigs | нет | Нет |
> | namespaces / disasterrecoveryconfigs / checknameavailability | Нет | Нет |
> | namespaces / eventgridfilters | нет | Нет |
> | namespaces / networkrulesets | Нет | Нет |
> | namespaces / queues | Нет | Нет |
> | namespaces / queues / authorizationrules | Нет | Нет |
> | namespaces / topics | нет | нет |
> | namespaces / topics / authorizationrules | нет | нет |
> | namespaces / topics / subscriptions | нет | нет |
> | namespaces / topics / subscriptions / rules | Нет | Нет |
> | Операции | Нет | нет |
> | premiummessagingregions | Нет | нет |
> | sku | Нет | нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | нет | нет |
> | clusters | нет | нет |
> | clusters / applications | Нет | Нет |
> | containergroups | нет | нет |
> | containergroupsets | Нет | Нет |
> | edgeclusters | Нет | нет |
> | Расположения | Нет | нет |
> | locations / clusterversions | Нет | Нет |
> | locations / environments | Нет | Нет |
> | locations / operationresults | нет | Нет |
> | locations / operations | нет | Нет |
> | managedclusters | нет | Нет |
> | networks | нет | Нет |
> | Операции | нет | Нет |
> | secretstores | нет | Нет |
> | volumes. | Нет | Нет |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | нет | Нет |
> | containergroups | Нет | Нет |
> | gateways | нет | Нет |
> | Расположения | нет | Нет |
> | locations / applicationoperations | нет | Нет |
> | locations / gatewayoperations | нет | Нет |
> | locations / networkoperations | нет | Нет |
> | locations / secretoperations | нет | Нет |
> | locations / volumeoperations | нет | Нет |
> | networks | нет | Нет |
> | Операции | нет | Нет |
> | секретные коды | нет | Нет |
> | volumes. | нет | Нет |

## <a name="microsoftservices"></a>NNTP. Обслуживание

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | rollouts | Нет | нет |

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
> | hybridusebenefits | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Нет | нет |
> | веб-масштабированием; | Нет | нет |
> | jitrequests | Нет | нет |
> | Расположения | Нет | нет |
> | locations / operationstatuses | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | instancepools | нет | нет |
> | Расположения | Да | Да |
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
> | locations / privateendpointconnectionoperationresults | Нет | Нет |
> | locations / privateendpointconnectionproxyazureasyncoperation | нет | нет |
> | locations / privateendpointconnectionproxyoperationresults | нет | нет |
> | locations / securityalertpoliciesazureasyncoperation | нет | нет |
> | locations / securityalertpoliciesoperationresults | нет | нет |
> | locations / serveradministratorazureasyncoperation | нет | нет |
> | locations / serveradministratoroperationresults | нет | нет |
> | locations / serverazureasyncoperation | нет | нет |
> | locations / serverkeyazureasyncoperation | нет | нет |
> | locations / serverkeyoperationresults | нет | нет |
> | locations / serveroperationresults | Нет | Нет |
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
> | servers | нет | нет |
> | servers / administratoroperationresults | нет | нет |
> | servers / administrators | нет | нет |
> | servers / advisors | нет | нет |
> | servers / aggregateddatabasemetrics | Нет | Нет |
> | servers / auditingsettings | нет | нет |
> | servers / automatictuning | нет | нет |
> | servers / communicationlinks | нет | нет |
> | servers / databases | нет | нет |
> | servers / databases / advisors | нет | нет |
> | servers / databases / auditingsettings | нет | нет |
> | servers / databases / auditrecords | нет | нет |
> | servers / databases / automatictuning | нет | нет |
> | servers / databases / backuplongtermretentionpolicies | Нет | Нет |
> | servers / databases / backupshorttermretentionpolicies | Нет | Нет |
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
> | servers / databases / vulnerabilityassessmentscans | Нет | Нет |
> | servers / databases / vulnerabilityassessmentsettings | Нет | Нет |
> | servers / databases / workloadgroups | Нет | Нет |
> | servers / databasesecuritypolicies | Нет | нет |
> | servers / disasterrecoveryconfiguration | Нет | нет |
> | servers / dnsaliases | Нет | нет |
> | servers / elasticpoolestimates | Нет | нет |
> | servers / elasticpools | Нет | нет |
> | servers / elasticpools / advisors | Нет | нет |
> | servers / elasticpools / metricdefinitions | Нет | Нет |
> | servers / elasticpools / metrics | Нет | нет |
> | servers / encryptionprotector | Нет | Нет |
> | servers / extendedauditingsettings | Нет | Нет |
> | servers / failovergroups | Нет | Нет |
> | servers / import | нет | Нет |
> | servers / importexportoperationresults | Нет | Нет |
> | servers / jobaccounts | нет | нет |
> | servers / jobagents | нет | нет |
> | servers / jobagents / jobs | нет | нет |
> | servers / jobagents / jobs / executions | нет | нет |
> | servers / jobagents / jobs / steps | нет | нет |
> | servers / keys | нет | нет |
> | servers / operationresults | нет | нет |
> | servers / recommendedelasticpools | Нет | Нет |
> | servers / recoverabledatabases | нет | нет |
> | servers / restorabledroppeddatabases | нет | нет |
> | servers / securityalertpolicies | нет | нет |
> | servers / serviceobjectives | нет | нет |
> | servers / syncagents | нет | нет |
> | servers / tdecertificates | нет | нет |
> | servers / usages | нет | нет |
> | servers / virtualnetworkrules | нет | нет |
> | servers / vulnerabilityassessments | нет | нет |
> | virtualclusters | Нет | Нет |

> [!IMPORTANT]
> База данных и сервер должны быть в одной группе ресурсов. При перемещении сервера SQL Server все его базы данных также перемещаются. Такое поведение также характерно для баз данных SQL Azure и баз данных хранилища данных SQL Azure.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | нет |
> | locations / availabilitygrouplisteneroperationresults | Нет | нет |
> | locations / operationtypes | Нет | нет |
> | locations / sqlvirtualmachinegroupoperationresults | Нет | нет |
> | locations / sqlvirtualmachineoperationresults | Нет | Нет |
> | Операции | Нет | нет |
> | sqlvirtualmachinegroups | Да | Да |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | Нет | нет |
> | sqlvirtualmachines | Да | Да |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | Нет |
> | Расположения | Нет | нет |
> | locations / asyncoperations | Нет | Нет |
> | locations / checknameavailability | Нет | Нет |
> | locations / deletevirtualnetworkorsubnets | нет | Нет |
> | locations / usages | Нет | Нет |
> | Операции | нет | нет |
> | storageaccounts | Да | Да |
> | storageaccounts / blobservices | Нет | Нет |
> | storageaccounts / fileservices | Нет | нет |
> | storageaccounts / listaccountsas | Нет | нет |
> | storageaccounts / listservicesas | Нет | нет |
> | storageaccounts / queueservices | Нет | нет |
> | storageaccounts / services | Нет | Нет |
> | storageaccounts / services / metricdefinitions | Нет | Нет |
> | storageaccounts / tableservices | Нет | Нет |
> | usages | нет | нет |

## <a name="microsoftstoragecache"></a>NNTP. сторажекаче

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | caches | нет | Нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | Нет |
> | locations / checknameavailability | Нет | Нет |
> | locations / operationresults | нет | Нет |
> | locations / operations | нет | Нет |
> | locations / workflows | нет | Нет |
> | Операции | нет | Нет |
> | storagesyncservices | нет | Нет |
> | storagesyncservices / registeredservers | нет | Нет |
> | storagesyncservices / syncgroups | нет | Нет |
> | storagesyncservices / syncgroups / cloudendpoints | нет | Нет |
> | storagesyncservices / syncgroups / serverendpoints | нет | Нет |
> | storagesyncservices / workflows | Нет | Нет |

## <a name="microsoftstoragesyncdev"></a>NNTP. сторажесинкдев

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | нет | Нет |

## <a name="microsoftstoragesyncint"></a>NNTP. сторажесинЦинт

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Нет | нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | managers | нет | нет |
> | Операции | Нет | Нет |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | clusters | Да | Да |
> | Расположения | Нет | Нет |
> | locations / quotas | Нет | Нет |
> | Операции | нет | нет |
> | streamingjobs | Да | Да |

> [!IMPORTANT]
> Задания Stream Analytics в состоянии выполнения нельзя переместить.

## <a name="microsoftstreamanalyticsexplorer"></a>NNTP. стреаманалитиксексплорер

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | environments | Нет | Нет |
> | environments / eventsources | нет | Нет |
> | instances | нет | Нет |
> | instances / environments | Нет | Нет |
> | instances / environments / eventsources | Нет | Нет |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | cancel | Нет | Нет |
> | createsubscription | Нет | Нет |
> | enable | нет | Нет |
> | operationresults | Нет | Нет |
> | Операции | Нет | Нет |
> | переименовать | нет | нет |
> | subscriptiondefinitions | нет | нет |
> | subscriptionoperations | нет | нет |
> | subscriptions | Нет | Нет |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | нет | нет |
> | operationresults | Нет | Нет |
> | Операции | Нет | нет |
> | operationsstatus | Нет | нет |
> | services; | Нет | нет |
> | services / problemclassifications | Нет | нет |
> | supporttickets | Нет | нет |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Нет | Нет |
> | Операции | нет | Нет |
> | привателинкхубс | нет | Нет |
> | workspaces | нет | Нет |
> | workspaces / bigdatapools | нет | Нет |
> | workspaces / operationresults | нет | Нет |
> | workspaces / operationstatuses | нет | Нет |
> | workspaces / sqlpools | нет | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | environments | Нет | Нет |
> | environments / accesspolicies | Нет | Нет |
> | environments / eventsources | нет | Нет |
> | environments / referencedatasets | нет | Нет |
> | Операции | нет | Нет |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | stores | Нет | нет |
> | stores / accesspolicies | Нет | нет |
> | stores / services | Нет | нет |
> | stores / services / tokens | Нет | нет |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | imagetemplates | нет | нет |
> | имажетемплатес/рунаутпутс | нет | нет |
> | Расположения | Нет | Нет |
> | locations / operations | Нет | нет |
> | Операции | Нет | нет |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | account | нет | нет |
> | account / extension | нет | нет |
> | account / project | Нет | Нет |
> | checknameavailability | Нет | нет |
> | Операции | Нет | нет |

> [!IMPORTANT]
> Чтобы изменить подписку на Azure DevOps, см. статью [Изменить подписку Azure, используемую для выставления счетов](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | arczones | Нет | Нет |
> | Расположения | нет | нет |
> | locations / operationstatuses | нет | нет |
> | Операции | нет | Нет |
> | resourcepools | нет | нет |
> | vcenters | Нет | Нет |
> | virtualmachines | Нет | Нет |
> | virtualmachinetemplates | Нет | Нет |
> | virtualnetworks | нет | нет |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | нет | Нет |
> | dedicatedcloudservices | нет | Нет |
> | Расположения | нет | Нет |
> | locations / availabilities | нет | Нет |
> | locations / operationresults | нет | Нет |
> | locations / privateclouds | нет | Нет |
> | locations / privateclouds / resourcepools | нет | Нет |
> | locations / privateclouds / virtualmachinetemplates | нет | Нет |
> | locations / privateclouds / virtualnetworks | Нет | Нет |
> | locations / usages | нет | Нет |
> | Операции | нет | Нет |
> | virtualmachines | нет | Нет |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | устройства | Нет | нет |
> | Расположения | Нет | нет |
> | locations / operationstatuses | Нет | нет |
> | Операции | Нет | нет |
> | vnfs | Нет | нет |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |
> | Операции | нет | нет |
> | Планы | нет | нет |
> | registeredsubscriptions | нет | нет |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | availablestacks | нет | Нет |
> | billingmeters | нет | Нет |
> | certificates | Нет | Нет |
> | checknameavailability | нет | Нет |
> | connectiongateways | Да | Да |
> | connections | Да | Да |
> | customapis | Да | Да |
> | deletedsites | нет | Нет |
> | deploymentlocations | Нет | Нет |
> | georegions | Нет | Нет |
> | hostingenvironments | нет | Нет |
> | hostingenvironments / eventgridfilters | нет | Нет |
> | hostingenvironments / multirolepools | нет | Нет |
> | hostingenvironments / workerpools | нет | Нет |
> | ishostingenvironmentnameavailable | нет | Нет |
> | ishostnameavailable | нет | Нет |
> | isusernameavailable | нет | Нет |
> | kubeenvironments | Нет | Нет |
> | listsitesassignedtohostname | Нет | нет |
> | Расположения | Нет | нет |
> | locations / apioperations | Нет | нет |
> | locations / connectiongatewayinstallations | Нет | нет |
> | locations / deletedsites | Нет | Нет |
> | locations / deletevirtualnetworkorsubnets | Нет | Нет |
> | locations / extractapidefinitionfromwsdl | нет | Нет |
> | locations / getnetworkpolicies | нет | Нет |
> | locations / listwsdlinterfaces | Нет | Нет |
> | locations / managedapis | нет | нет |
> | locations / operationresults | нет | нет |
> | locations / operations | нет | нет |
> | locations / runtimes | нет | нет |
> | Операции | нет | нет |
> | publishingusers | нет | нет |
> | к просмотру фильмов | нет | нет |
> | resourcehealthmetadata | Нет | Нет |
> | runtimes | Нет | Нет |
> | serverfarms | Да | Да |
> | serverfarms / eventgridfilters | Нет | Нет |
> | sites | Да | Да |
> | sites / eventgridfilters | Нет | Нет |
> | sites / hostnamebindings | Нет | Нет |
> | sites / networkconfig | Нет | Нет |
> | sites / premieraddons | Нет | Нет |
> | sites / slots | Нет | Нет |
> | sites / slots / eventgridfilters | Нет | Нет |
> | sites / slots / hostnamebindings | Нет | Нет |
> | sites / slots / networkconfig | Нет | Нет |
> | sourcecontrols | Нет | Нет |
> | staticsites | Нет | Нет |
> | validate | Нет | Нет |
> | verifyhostingenvironmentvnet | Нет | Нет |

> [!IMPORTANT]
> См. статью [Руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | Нет |
> | locations / operationstatuses | Нет | Нет |
> | multipleactivationkeys | Нет | Нет |
> | Операции | Нет | Нет |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | deviceservices | Нет | Нет |
> | Операции | Нет | Нет |

## <a name="microsoftworkloadbuilder"></a>Microsoft. Ворклоадбуилдер

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Подписка |
> | ------------- | ----------- | ---------- |
> | Расположения | Нет | Нет |
> | locations / operationstatuses | Нет | Нет |
> | Операции | Нет | Нет |
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
