---
title: Поддержка перемещения ресурсов Azure в разных регионах
description: Список типов ресурсов Azure, которые можно перемещать между регионами Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 05/31/2020
ms.author: raynew
ms.openlocfilehash: 0510df504c8de70cfb6a486f394db6da65dbfce2
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057693"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Поддержка перемещения ресурсов Azure в разных регионах

В этой статье мы подтверждаем, поддерживает ли тип ресурсов Azure перемещение в другой регион Azure. 

Переход к пространству имен поставщика ресурсов:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
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
> - [Microsoft.Storage](#microsoftstorage)
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
> | actionrules | Нет | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | servers | Нет |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | служба |  Да (с использованием шаблона) <br/><br/> [Перемещение управления API между регионами](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | configurationstores | Нет | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | apiapps | Да (с использованием шаблона)<br/><br/> [Перемещение приложения службы приложений в другой регион](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Нет | 
> | gateways | Нет | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | policyassignments | нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | automationaccounts | Да (с использованием шаблона) <br/><br/> [Использование георепликации](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts / configurations | Нет | 
> | automationaccounts / runbooks | Нет | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | b2cdirectories | нет | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | sqlserverregistrations | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | registrations | Нет | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | batchaccounts |  Учетные записи пакетной службы нельзя переместить непосредственно из одного региона в другой, но можно использовать шаблон для экспорта шаблона, его изменения и развертывания шаблона в новом регионе. <br/><br/> Сведения о [перемещении учетной записи пакетной службы между регионами](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | clusters | Нет <br/><br/> Служба Azure Batch AI [прекращена](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai).
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
> | blockchainmembers | Нет <br/><br/> Сеть блокчейн не может иметь узлы в разных регионах. 
> | watchers | Нет | 

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
> | cdnwebapplicationfirewallpolicies | Нет |
> | профили | Нет | 
> | profiles / endpoints | Нет | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | certificateorders | Нет | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | domainnames | Для классических служб не планируется никакой работы.
> | virtualmachines | нет | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | networksecuritygroups | Для классических служб не планируется никакой работы.
> | reservedips | нет | 
> | virtualnetworks | Нет | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | storageaccounts | Да |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 
> | Когнитивный поиск | Поддерживается с ручными действиями.<br/><br/> Дополнительные сведения о [перемещении службы когнитивный Поиск Azure в другой регион](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | availabilitysets | Нет | 
> | diskencryptionsets | Нет | 
> | disks | Нет | 
> | galleries | Нет | 
> | galleries / images | Нет | 
> | galleries / images / versions | Нет | 
> | hostgroups | Нет | 
> | hostgroups / hosts | Нет | 
> | images | Нет | 
> | proximityplacementgroups | Нет | 
> | restorepointcollections | Нет | 
> | sharedvmimages | Нет | 
> | sharedvmimages / versions | Нет | 
> | snapshots | Нет | 
> | virtualmachines | Да | 
> | virtualmachines / extensions | Нет | 
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
> | registries / buildtasks | Нет |  
> | registries / replications | Нет | 
> | registries / tasks | Нет |  
> | registries / webhooks | Нет | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | containerservices | Нет.<br/><br/> Служба [прекращена](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/).
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
> | hubs | нет |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | resourceproviders | Нет | 

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
> | datacatalogs | Нет | 

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
> | services / projects | Нет | 
> | slots | Нет | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | servers | Если служба подготовлена с геоизбыточным хранилищем резервных копий, можно использовать геовосстановление для восстановления в других регионах. [Подробнее](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)

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
> | artifactsources | нет | 
> | rollouts | нет |  
> | servicetopologies | Нет | 
> | servicetopologies / services | Нет |  
> | servicetopologies / services / serviceunits | Нет | 
> | steps | Нет | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | elasticpools | Нет. Ресурс не предоставлен.
> | elasticpools / iothubtenants | Нет. Ресурс не предоставлен.
> | iothubs | Да. [Подробнее](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Нет | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | controllers | Нет | 
> | Кластер AKS | Нет<br/><br/> Дополнительные [сведения](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) о переходе в другой регион.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | labcenters | Нет | 
> | labs | Нет | 
> | labs / environments | Нет |  
> | labs / servicerunners | Нет | 
> | labs / virtualmachines | Нет |  
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

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

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
> | пространства имен | Да (с шаблоном)<br/><br/> [Перемещение пространства имен концентратора событий в другой регион](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | hanainstances | нет | 
> | sapmonitors | Нет |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | clusters | Нет | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | services; | Нет |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | machines | нет | 

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
> | alertrules |  нет | 
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
> | checknameavailability |  Нет.<br/><br/> IoT Central работает с географическими и не регионами.
> | graph | Нет

## <a name="microsoftiothub"></a>Microsoft. IoTHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> |  iothub |  Да (концентратор клонирования) <br/><br/> [Клонирование центра Интернета вещей в другой регион](../../iot-hub/iot-hub-how-to-clone.md)

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
> | accounts | Нет, это глобальная служба.

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | hostingenvironments | Нет | 
> | integrationaccounts |  Нет |  
> | integrationserviceenvironments | Нет | 
> | isolatedenvironments | нет | 
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
> | accounts / workspaces | Нет | 
> | accounts / workspaces / projects | Нет | 
> | teamaccounts | Нет | 
> | teamaccounts / workspaces | Нет | 
> | teamaccounts / workspaces / projects | Нет | 

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
> | accounts |  Нет, Azure Maps является геопространственной службой. 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | classicdevservices | Для классических служб не запланирована работа 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | mediaservices |  Нет | 
> | mediaservices / liveevents |  Нет | 
> | mediaservices / streamingendpoints |  Нет | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | appclusters | Нет | 

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
> | netappaccounts / capacitypools | Нет | 
> | netappaccounts / capacitypools / volumes | Нет | 
> | netappaccounts / capacitypools / volumes / mounttargets | Нет | 
> | netappaccounts / capacitypools / volumes / snapshots | Нет | 

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | applicationgateways | Нет | 
> | applicationgatewaywebapplicationfirewallpolicies | нет | 
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
> | loadbalancers | Да <br/><br/> Можно экспортировать существующую конфигурацию в качестве шаблона и развернуть шаблон в новом регионе. Узнайте, как переместить [внешнюю](../..//load-balancer/move-across-regions-external-load-balancer-portal.md) или [внутреннюю](../../load-balancer/move-across-regions-internal-load-balancer-portal.md) подсистему балансировки нагрузки. |
> | localnetworkgateways |  Нет | 
> | natgateways |  Нет | 
> | networkintentpolicies |  Нет | 
> | networkinterfaces | Да | 
> | networkprofiles | Нет | 
> | networksecuritygroups | Да | 
> | networkwatchers |  Нет |  
> | networkwatchers / connectionmonitors |  Нет | 
> | нетворкватчерс/lenses |  Нет | 
> | networkwatchers / pingmeshes |  Нет | 
> | p2svpngateways | Нет | 
> | privatednszones |  Нет |  
> | privatednszones / virtualnetworklinks |  Нет |  
> | privateendpoints | Нет | 
> | privatelinkservices | Нет | 
> | publicipaddresses | Да<br/><br/> Можно экспортировать существующую конфигурацию общедоступного IP-адреса в качестве шаблона и развернуть шаблон в новом регионе. Дополнительные [сведения](../../virtual-network/move-across-regions-publicip-portal.md) о перемещении общедоступного IP-адреса. |
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
> | vpngateways (Virtual WAN) | нет | 
> | vpnsites (Virtual WAN) | нет | 
> | webapplicationfirewallpolicies |  Нет | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | пространства имен |  Нет | 
> | namespaces / notificationhubs |  Нет |  

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

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | peerings | Нет | 

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
> | vaults | Нет.<br/><br/> Перемещение хранилищ служб восстановления для Azure Backup в регионах Azure не поддерживается.<br/><br/> В хранилищах служб восстановления для Azure Site Recovery можно [Отключить и повторно создать хранилище](../../site-recovery/move-vaults-across-regions.md) в целевом регионе. | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | пространства имен |  Нет | 

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
> | iotsecuritysolutions |  Нет | 
> | плайбукконфигуратионс | Нет | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | gateways | Нет | 
> | Узлы | нет | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | пространства имен |  нет | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | веб-масштабированием; | Нет | 
> | clusters |  Нет | 
> | clusters / applications | Нет | 
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
> | instancepools | Нет | 
> | managedinstances | Да | 
> | managedinstances / databases | Да | 
> | servers | Да | 
> | servers / databases | Да | 
> | servers / elasticpools | Да | 
> | virtualclusters | Да | 

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
> | storageaccounts | Да<br/><br/> [Перемещение учетной записи хранения Azure в другой регион](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | caches | Нет | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | storagesyncservices |  нет | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | storagesyncservices | нет | 

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
> | environments / eventsources | Нет | 
> | instances | Нет | 
> | instances / environments | Нет | 
> | instances / environments / eventsources | Нет | 

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
> | environments / eventsources |  Нет |  
> | environments / referencedatasets |  Нет | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | stores | Нет | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | imagetemplates | Нет | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | account |  Нет | 
> | account / extension |  Нет | 
> | account / project |  Нет | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Нет | 
> | dedicatedcloudservices | Нет | 
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
> | sites / premieraddons |  Нет |  
> | sites / slots |  нет |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | deviceservices | нет | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. Виндовсвиртуалдесктоп

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | applicationgroups | Нет | 
> | hostpools | Нет | 
> | workspaces | нет | 

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.
