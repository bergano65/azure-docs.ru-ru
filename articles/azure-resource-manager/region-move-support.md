---
title: Поддержка перемещения ресурсов Azure в разных регионах
description: Список типов ресурсов Azure, которые можно перемещать между регионами Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 60b8708458e081d66514e092edc9ef9af7c33494
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308106"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Поддержка перемещения ресурсов Azure в разных регионах

В этой статье мы подтверждаем, поддерживает ли тип ресурсов Azure перемещение в другой регион Azure. 

Переход к пространству имен поставщика ресурсов:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft. аадиам](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. BatchAI](#microsoftbatchai)
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
> - [Microsoft. Container](#microsoftcontainer)
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
> - [Microsoft. Обмен](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft. Data Lake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
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
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft. Локатионбаседсервицес](#microsoftlocationbasedservices)
> - [Microsoft. файл locationservices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft. Мачинелеарнингкомпуте](#microsoftmachinelearningcompute)
> - [Microsoft. Мачинелеарнинжекспериментатион](#microsoftmachinelearningexperimentation)
> - [Microsoft. Мачинелеарнингмоделманажемент](#microsoftmachinelearningmodelmanagement)
> - [Microsoft. Мачинелеарнингоператионализатион](#microsoftmachinelearningoperationalization)
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
> - [Microsoft. Порталсдк](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. Прожектоксфорд](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. Серверманажемент](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Стреаманалитиксексплорер](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Терраформосс](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. Token](#microsofttoken)
> - [Microsoft. Виртуалмачинеимажес](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. Виндовсвиртуалдесктоп](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- | 
> | domainservices | Нет | 
> | DomainServices/репликасетс | Нет | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | tenants | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | актионрулес | Нет | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | servers | Нет |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | Служба | Нет |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | конфигуратионсторес | Нет | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | apiapps | Нет | 
> | appidentities | Нет | 
> | gateways | Нет | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | policyassignments | Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | automationaccounts | Нет | 
> | automationaccounts и конфигурации | Нет | 
> | automationaccounts и модули Runbook | Нет | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | b2cdirectories | Нет | 

## <a name="microsoftazuredata"></a>Microsoft. Азуредата

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | склсерверрегистратионс | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | registrations | Нет | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | batchaccounts | Нет |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | clusters | Нет | 
> | fileservers | Нет | 
> | jobs | Нет | 
> | workspaces | Нет | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | mapapis | Нет | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | biztalk | Нет | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | blockchainmembers | Нет |
> | наблюдатели | Нет | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | blueprintassignments | Нет | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | botservices | Нет | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | redis | Нет | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | кднвебаппликатионфиреваллполиЦиес | Нет |
> | profiles | Нет | 
> | профили и конечные точки | Нет | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | certificateorders | Нет | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | domainnames | Нет |  
> | virtualmachines | Нет | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | networksecuritygroups | Нет |
> | reservedips | Нет | 
> | virtualnetworks | Нет | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | storageaccounts | Yes |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | availabilitysets | Нет | 
> | дискенкриптионсетс | Нет | 
> | disks | Нет | 
> | galleries | Нет | 
> | коллекции и изображения | Нет | 
> | коллекции, изображения и версии | Нет | 
> | хостграупс | Нет | 
> | хостграупс и узлы | Нет | 
> | images | Нет | 
> | proximityplacementgroups | Нет | 
> | restorepointcollections | Нет | 
> | sharedvmimages | Нет | 
> | шаредвмимажес и версии | Нет | 
> | snapshots | Нет | 
> | virtualmachines | Yes | 
> | virtualmachines и расширения | Нет | 
> | virtualmachinescalesets | Нет | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | containergroups | Нет | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | containergroups | Нет | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | registries | Нет |  
> | реестры и буилдтаскс | Нет |  
> | реестры и репликации | Нет | 
> | реестры и задачи | Нет |  
> | реестры и веб-перехватчики | Нет | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | containerservices | Нет | 
> | managedclusters | Нет | 
> | openshiftmanagedclusters | Нет | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | веб-масштабированием; | Нет | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | соединители | Нет |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | hubs | Нет |  

## <a name="microsoftcustomproviders"></a>Microsoft. Кустомпровидерс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | ресаурцепровидерс | Нет | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | jobs | Нет | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | databoxedgedevices | Нет | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | workspaces | Нет | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | catalogs | Нет | 
> | каталоги | Нет | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | connectionmanagers | Нет | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | Пакеты | Нет | 
> | Планы | Нет | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | datafactories | Нет | 
> | factories | Нет |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | datalakeaccounts | Нет | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | services; | Нет | 
> | службы и проекты | Нет | 
> | slots | Нет | 

## <a name="microsoftdatashare"></a>Общая папка Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | servers | Нет |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | servers | Нет |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | servergroups | Нет | 
> | servers | Нет |  
> | serversv2 | Нет | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | artifactsources | Нет | 
> | rollouts | Нет |  
> | servicetopologies | Нет | 
> | сервицетопологиес и службы | Нет |  
> | сервицетопологиес/Services/сервицеунитс | Нет | 
> | steps | Нет | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | elasticpools | Нет | 
> | еластикпулс/иосубтенантс | Нет | 
> | iothubs | Yes | 
> | provisioningservices | Нет | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | controllers | Нет | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | labcenters | Нет | 
> | labs | Нет | 
> | лаборатории и среды | Нет |  
> | Labs и сервицеруннерс | Нет | 
> | Labs и virtualmachines | Нет |  
> | schedules | Нет |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | databaseaccounts | Нет | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | domains | Нет | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | services; | Нет |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | domains | Нет |  
> | topics | Нет | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | clusters | Нет |  
> | namespaces | Нет | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | hanainstances | Нет | 
> | сапмониторс | Нет |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | clusters | Нет | 

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | services; | Нет |  

## <a name="microsofthybridcompute"></a>Microsoft. Хибридкомпуте

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | виртуальных | Нет | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | datamanagers |  Нет | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | jobs |  Нет | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 
> | actiongroups |  Нет | 
> | activitylogalerts | Нет | 
> | alertrules |  Нет | 
> | autoscalesettings |  Нет | 
> | components |  Нет |  
> | guestdiagnosticsettings | Нет | 
> | metricalerts | Нет | 
> | notificationgroups | Нет | 
> | notificationrules | Нет | 
> | scheduledqueryrules |  Нет | 
> | webtests |  Нет | 
> | workbooks |  Нет |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | iotapps |  Нет |  

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | checknameavailability |  Нет |  
> | graph |  Нет | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | hsmpools | Нет | 
> | vaults |  Нет | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | clusters |  Нет |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | labaccounts | Нет | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | hostingenvironments | Нет | 
> | integrationaccounts |  Нет |  
> | integrationserviceenvironments | Нет | 
> | isolatedenvironments | Нет | 
> | workflows |  Нет |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | commitmentplans |  Нет | 
> | webservices |  Нет | 
> | workspaces |  Нет | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | operationalizationclusters |  Нет | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 
> | учетные записи и рабочие области | Нет | 
> | учетные записи, рабочие области и проекты | Нет | 
> | teamaccounts | Нет | 
> | теамаккаунтс и рабочие области | Нет | 
> | теамаккаунтс/рабочие области и проекты | Нет | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | hostingaccounts | Нет | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | workspaces | Нет | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | userassignedidentities | Нет | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts |  Нет |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | classicdevservices | Нет | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | mediaservices |  Нет | 
> | mediaservices/лививентс |  Нет | 
> | mediaservices/streamingendpoint |  Нет | 

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | аппклустерс | Нет | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | assessmentprojects | Нет | 
> | migrateprojects | Нет | 
> | projects | Нет | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | netappaccounts | Нет | 
> | нетаппаккаунтс/капаЦитипулс | Нет | 
> | нетаппаккаунтс/капаЦитипулс/тома | Нет | 
> | нетаппаккаунтс/капаЦитипулс/Volumes/маунттаржетс | Нет | 
> | нетаппаккаунтс/капаЦитипулс/тома/моментальные снимки | Нет | 

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | applicationgateways | Нет | 
> | аппликатионгатевайвебаппликатионфиреваллполиЦиес | Нет | 
> | applicationsecuritygroups |  Нет |  
> | azurefirewalls |  Нет |  
> | bastionhosts | Нет | 
> | connections |  Нет | 
> | ddoscustompolicies |  Нет | 
> | ddosprotectionplans | Нет | 
> | dnszones |  Нет | 
> | expressroutecircuits | Нет | 
> | expressroutecrossconnections | Нет | 
> | expressroutegateways | Нет | 
> | expressrouteports | Нет | 
> | frontdoors | Нет | 
> | frontdoorwebapplicationfirewallpolicies | Нет | 
> | loadbalancers | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br> -Да номер SKU "Стандартный" |
> | localnetworkgateways |  Нет | 
> | natgateways |  Нет | 
> | networkintentpolicies |  Нет | 
> | networkinterfaces | Yes | 
> | networkprofiles | Нет | 
> | networksecuritygroups | Yes | 
> | networkwatchers |  Нет |  
> | нетворкватчерс/коннектионмониторс |  Нет | 
> | нетворкватчерс/lenses |  Нет | 
> | нетворкватчерс/пингмешес |  Нет | 
> | p2svpngateways | Нет | 
> | приватеднсзонес |  Нет |  
> | приватеднсзонес/виртуалнетворклинкс |  Нет |  
> | приватиндпоинтс | Нет | 
> | privatelinkservices | Нет | 
> | publicipaddresses | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br>SKU No-Standard |
> | publicipprefixes | Нет | 
> | routefilters | Нет | 
> | routetables |  Нет | 
> | serviceendpointpolicies |  Нет | 
> | trafficmanagerprofiles |  Нет | 
> | virtualhubs | Нет | 
> | virtualnetworkgateways |  Нет |  
> | virtualnetworks |  Нет | 
> | virtualnetworktaps | Нет | 
> | virtualwans | Нет | 
> | впнгатевайс (Виртуальная глобальная сеть) | Нет | 
> | впнситес (Виртуальная глобальная сеть) | Нет | 
> | webapplicationfirewallpolicies |  Нет | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | namespaces |  Нет | 
> | пространства имен/notificationhubs |  Нет |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | workspaces |  Нет | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | managementconfigurations |  Нет | 
> | узел "Представления" |  Нет | 

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | пиринги | Нет | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | dashboards | Нет | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | rootresources | Нет | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | workspacecollections |  Нет | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | capacities |  Нет | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | vaults | Да (для хранилищ резервных копий я думаю? | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | namespaces |  Нет | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | Запросы |  Нет |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | веб-масштабированием; |  Нет | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | flows |  Нет |  
> | jobcollections |  Нет | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | searchservices |  Нет | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | иотсекуритисолутионс |  Нет | 
> | плайбукконфигуратионс | Нет | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | gateways | Нет | 
> | Узлы | Нет | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | namespaces |  Нет | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | веб-масштабированием; | Нет | 
> | clusters |  Нет | 
> | кластеры и приложения | Нет | 
> | containergroups | Нет | 
> | containergroupsets | Нет | 
> | edgeclusters | Нет | 
> | networks | Нет | 
> | secretstores | Нет | 
> | volumes. | Нет | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | веб-масштабированием; |  Нет | 
> | containergroups | Нет | 
> | gateways |  Нет | 
> | networks |  Нет | 
> | секретные коды |  Нет | 
> | volumes. |  Нет |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | signalr |  Нет |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | appliancedefinitions | Нет | 
> | appliances | Нет | 
> | applicationdefinitions | Нет | 
> | веб-масштабированием; | Нет | 
> | jitrequests | Нет | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | инстанцепулс | Нет | 
> | managedinstances | Yes | 
> | манажединстанцес и базы данных | Yes | 
> | servers | Yes | 
> | серверы и базы данных | Yes | 
> | серверы и еластикпулс | Yes | 
> | virtualclusters | Yes | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Нет |  
> | sqlvirtualmachines |  Нет |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | dwvm | Нет | 

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | storageaccounts | Да? | 

## <a name="microsoftstoragecache"></a>Microsoft. Сторажекаче

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | кэширует | Нет | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | storagesyncservices |  Нет | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | storagesyncservices | Нет | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | storagesyncservices | Нет | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | managers | Нет | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | streamingjobs |  Нет |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | environments | Нет | 
> | среды и классов EventSource | Нет | 
> | instances | Нет | 
> | экземпляры и среды | Нет | 
> | экземпляры/среды/классов EventSource | Нет | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | providerregistrations | Нет | 
> | ресурсов | Нет | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | environments |  Нет | 
> | среды и классов EventSource |  Нет |  
> | среды и референцедатасетс |  Нет | 

## <a name="microsofttoken"></a>Microsoft. Token

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | сохраняют | Нет | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | imagetemplates | Нет | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | учетная запись |  Нет | 
> | Учетная запись или расширение |  Нет | 
> | Учетная запись или проект |  Нет | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | дедикатедклауднодес | Нет | 
> | дедикатедклаудсервицес | Нет | 
> | virtualmachines | Нет | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | certificates | Нет | 
> | connectiongateways |  Нет |  
> | connections |  Нет |  
> | customapis |  Нет | 
> | hostingenvironments | Нет | 
> | serverfarms |  Нет |  
> | sites |  Нет | 
> | Sites/премиераддонс |  Нет |  
> | сайты и слоты |  Нет |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | deviceservices | Нет | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. Виндовсвиртуалдесктоп

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | аппликатионграупс | Нет | 
> | хостпулс | Нет | 
> | workspaces | Нет | 

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.
