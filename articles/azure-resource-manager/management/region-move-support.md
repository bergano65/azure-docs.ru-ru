---
title: Поддержка перемещения ресурсов Azure в разных регионах
description: Списки типов ресурсов Azure, которые могут быть перемещены в регионах Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/20/2020
ms.author: raynew
ms.openlocfilehash: 9bc7dc66ccf3049ac878f7871c816e5ade1afde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760714"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Поддержка перемещения ресурсов Azure в разных регионах

В этой статье подтверждается, поддерживается ли тип ресурсов Azure для перемещения в другой регион Azure. 

Перейти к названию поставщика ресурсов:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Авторизация](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
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
> - [Share Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
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
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage;](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- | 
> | domainservices | нет | 
> | domainservices / реплики | нет | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | tenants | нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | actionrules | нет | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | servers | нет |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | служба |  Да | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | configurationstores | нет | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | apiapps | нет | 
> | appidentities | нет | 
> | gateways | нет | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | policyassignments | нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | automationaccounts | нет | 
> | автоматизация счетов / конфигураций | нет | 
> | автоматизация счетов / runbooks | нет | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | b2cdirectories | нет | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | sqlserverregistrations | нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | registrations | нет | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | batchaccounts | нет |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | clusters | нет | 
> | fileservers | нет | 
> | jobs | нет | 
> | workspaces | нет | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | mapapis | нет | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | biztalk | нет | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | blockchainmembers | нет |
> | Наблюдателей | нет | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | blueprintassignments | нет | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | botservices | нет | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | redis | нет | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpoliciepolicies | нет |
> | профили | нет | 
> | профили / конечные точки | нет | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | certificateorders | нет | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | domainnames | нет |  
> | virtualmachines | нет | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | networksecuritygroups | нет |
> | reservedips | нет | 
> | virtualnetworks | нет | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | storageaccounts | Да |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | availabilitysets | нет | 
> | дископромиски | нет | 
> | disks | нет | 
> | galleries | нет | 
> | галереи / изображения | нет | 
> | галереи / изображения / версии | нет | 
> | принимающих групп | нет | 
> | принимающие группы / хосты | нет | 
> | images | нет | 
> | proximityplacementgroups | нет | 
> | restorepointcollections | нет | 
> | sharedvmimages | нет | 
> | sharedvmimages / версии | нет | 
> | snapshots | нет | 
> | virtualmachines | Да | 
> | виртуальные машины / расширения | нет | 
> | virtualmachinescalesets | нет | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | containergroups | нет | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | containergroups | нет | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | registries | нет |  
> | реестры / сборки | нет |  
> | реестры / репликации | нет | 
> | реестры / задачи | нет |  
> | реестры / веб-крючки | нет | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | containerservices | нет | 
> | managedclusters | нет | 
> | openshiftmanagedclusters | нет | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | веб-масштабированием; | нет | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | соединители | нет |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | hubs | нет |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | ресурсоснабжаели | нет | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | jobs | нет | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | databoxedgedevices | нет | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | workspaces | нет | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | catalogs | нет | 
> | каталоги данных | нет | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | connectionmanagers | нет | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | Пакеты | нет | 
> | Планы | нет | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | datafactories | нет | 
> | factories | нет |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | datalakeaccounts | нет | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | services; | нет | 
> | услуги / проекты | нет | 
> | slots | нет | 

## <a name="microsoftdatashare"></a>Share Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | servers | нет |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | servers | нет |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | servergroups | нет | 
> | servers | нет |  
> | serversv2 | нет | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | artifactsources | нет | 
> | rollouts | нет |  
> | servicetopologies | нет | 
> | сервисопологи / услуги | нет |  
> | сервисопологи / услуги / сервисы | нет | 
> | steps | нет | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | elasticpools | нет | 
> | эластичности / йотубтенантов | нет | 
> | iothubs | Да | 
> | provisioningservices | нет | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | controllers | нет | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | labcenters | нет | 
> | labs | нет | 
> | лаборатории / среды | нет |  
> | лаборатории / сервисраннеры | нет | 
> | лаборатории / виртуальные машины | нет |  
> | schedules | нет |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | databaseaccounts | нет | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | domains | нет | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | services; | нет |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | domains | нет |  
> | topics | нет | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | clusters | нет |  
> | пространства имен | нет | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | hanainstances | нет | 
> | сапеи | нет |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | clusters | нет | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | services; | нет |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | Машины | нет | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | datamanagers |  нет | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | jobs |  нет | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | accounts | нет | 
> | actiongroups |  нет | 
> | activitylogalerts | нет | 
> | alertrules |  нет | 
> | autoscalesettings |  нет | 
> | components |  нет |  
> | guestdiagnosticsettings | нет | 
> | metricalerts | нет | 
> | notificationgroups | нет | 
> | notificationrules | нет | 
> | scheduledqueryrules |  нет | 
> | webtests |  нет | 
> | workbooks |  нет |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | iotapps |  нет |  

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | checknameavailability |  нет |  
> | graph |  нет | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | hsmpools | нет | 
> | vaults |  нет | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | clusters |  нет |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | labaccounts | нет | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | hostingenvironments | нет | 
> | integrationaccounts |  нет |  
> | integrationserviceenvironments | нет | 
> | isolatedenvironments | нет | 
> | workflows |  нет |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | commitmentplans |  нет | 
> | webservices |  нет | 
> | workspaces |  нет | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | operationalizationclusters |  нет | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | accounts | нет | 
> | учетные записи / рабочие области | нет | 
> | учетные записи / рабочие области / проекты | нет | 
> | teamaccounts | нет | 
> | учетные записи / рабочие области | нет | 
> | teamaccounts / рабочие места / проекты | нет | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | hostingaccounts | нет | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | workspaces | нет | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | userassignedidentities | нет | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | accounts |  нет |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | classicdevservices | нет | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | mediaservices |  нет | 
> | медиа-услуги / живыесобытия |  нет | 
> | медиасервисы / streamingendpoints |  нет | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | appclusters | нет | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | assessmentprojects | нет | 
> | migrateprojects | нет | 
> | projects | нет | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | netappaccounts | нет | 
> | нетапсчета / capacitypools | нет | 
> | нетапсчета / capacitypools / объемы | нет | 
> | netappaccounts / capacitypools / томов / монтировок | нет | 
> | netappaccounts / capacitypools / томов / снимки | нет | 

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | applicationgateways | нет | 
> | applicationgatewaywebapplicationfirewallpolicies | нет | 
> | applicationsecuritygroups |  нет |  
> | azurefirewalls |  нет |  
> | bastionhosts | нет | 
> | connections |  нет | 
> | ddoscustompolicies |  нет | 
> | ddosprotectionplans | нет | 
> | dnszones |  нет | 
> | expressroutecircuits | нет | 
> | expressroutecrossconnections | нет | 
> | expressroutegateways | нет | 
> | expressrouteports | нет | 
> | frontdoors | нет | 
> | frontdoorwebapplicationfirewallpolicies | нет | 
> | loadbalancers | Да - Основные SKU<br>Нет - Стандартный SKU | Да - Основные SKU<br> -Да Стандартный SKU |
> | localnetworkgateways |  нет | 
> | natgateways |  нет | 
> | networkintentpolicies |  нет | 
> | networkinterfaces | Да | 
> | networkprofiles | нет | 
> | networksecuritygroups | Да | 
> | networkwatchers |  нет |  
> | сетевики / мониторы связи |  нет | 
> | сетевики / линзы |  нет | 
> | сетевики / пингмеши |  нет | 
> | p2svpngateways | нет | 
> | privatednszones |  нет |  
> | privatednszones / virtualnetworklinks |  нет |  
> | privateendpoints | нет | 
> | privatelinkservices | нет | 
> | publicipaddresses | Да - Основные SKU<br>Нет - Стандартный SKU | Да - Основные SKU<br>Нет - Стандартный SKU |
> | publicipprefixes | нет | 
> | routefilters | нет | 
> | routetables |  нет | 
> | serviceendpointpolicies |  нет | 
> | trafficmanagerprofiles |  нет | 
> | virtualhubs | нет | 
> | virtualnetworkgateways |  нет |  
> | virtualnetworks |  нет | 
> | virtualnetworktaps | нет | 
> | virtualwans | нет | 
> | vpngateways (Виртуальный WAN) | нет | 
> | vpnsites (Виртуальный WAN) | нет | 
> | webapplicationfirewallpolicies |  нет | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | пространства имен |  нет | 
> | именные пространства / notificationhubs |  нет |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | workspaces |  нет | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | managementconfigurations |  нет | 
> | узел "Представления" |  нет | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | вглядывания | нет | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | dashboards | нет | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | rootresources | нет | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | workspacecollections |  нет | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | capacities |  нет | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | vaults | Нет. [Отключить хранилище и воссоздать](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) для восстановления сайта  | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | пространства имен |  нет | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | Запросы |  нет |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | веб-масштабированием; |  нет | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | flows |  нет |  
> | jobcollections |  нет | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | searchservices |  нет | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  нет | 
> | playbookconfigurations | нет | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | gateways | нет | 
> | Узлы | нет | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | пространства имен |  нет | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | веб-масштабированием; | нет | 
> | clusters |  нет | 
> | кластеры / приложения | нет | 
> | containergroups | нет | 
> | containergroupsets | нет | 
> | edgeclusters | нет | 
> | networks | нет | 
> | secretstores | нет | 
> | volumes. | нет | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | веб-масштабированием; |  нет | 
> | containergroups | нет | 
> | gateways |  нет | 
> | networks |  нет | 
> | секретные коды |  нет | 
> | volumes. |  нет |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | signalr |  нет |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | appliancedefinitions | нет | 
> | appliances | нет | 
> | applicationdefinitions | нет | 
> | веб-масштабированием; | нет | 
> | jitrequests | нет | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | instancepools | нет | 
> | managedinstances | Да | 
> | управляемые instances / базы данных | Да | 
> | servers | Да | 
> | серверы / базы данных | Да | 
> | серверы / эластичные пулы | Да | 
> | virtualclusters | Да | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  нет |  
> | sqlvirtualmachines |  нет |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | dwvm | нет | 

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | storageaccounts | Да | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | Кэшей | нет | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | storagesyncservices |  нет | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | storagesyncservices | нет | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | storagesyncservices | нет | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | managers | нет | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | streamingjobs |  нет |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | environments | нет | 
> | среды / источники событий | нет | 
> | instances | нет | 
> | экземпляры / среды | нет | 
> | экземпляры / среды / eventsources | нет | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | providerregistrations | нет | 
> | ресурсов | нет | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | environments |  нет | 
> | среды / источники событий |  нет |  
> | среды / наборы справочных данных |  нет | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | stores | нет | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | imagetemplates | нет | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | account |  нет | 
> | учетная запись / расширение |  нет | 
> | учетная запись / проект |  нет | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | выделенные облачные | нет | 
> | dedicatedcloudservices | нет | 
> | virtualmachines | нет | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | certificates | нет | 
> | connectiongateways |  нет |  
> | connections |  нет |  
> | customapis |  нет | 
> | hostingenvironments | нет | 
> | serverfarms |  нет |  
> | sites |  нет | 
> | сайты / Премьераддоны |  нет |  
> | сайты / слоты |  нет |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | deviceservices | нет | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Тип ресурса | Регион двигаться | 
> | ------------- | ----------- |
> | группы заявок | нет | 
> | хостпулы | нет | 
> | workspaces | нет | 

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.
