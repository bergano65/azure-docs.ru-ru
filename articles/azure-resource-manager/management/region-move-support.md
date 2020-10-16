---
title: Поддержка перемещения ресурсов Azure в разных регионах
description: Список типов ресурсов Azure, которые можно перемещать между регионами Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 83cd36683a0487f13ab5707e4b1534cc7f20a88a
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948533"
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
> - [Microsoft.Resources](#microsoftresources)
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
> | domainservices | нет | 
> | DomainServices/репликасетс | нет | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | tenants | нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | actionrules | нет | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | servers | нет |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | служба |  Да (с использованием шаблона) <br/><br/> [Перемещение управления API между регионами](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | configurationstores | нет | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | apiapps | Да (с использованием шаблона)<br/><br/> [Перемещение приложения службы приложений в другой регион](../../app-service/manage-move-across-regions.md) | 
> | appidentities | нет | 
> | gateways | нет | 


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
> | automationaccounts / configurations | нет | 
> | automationaccounts / runbooks | нет | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | b2cdirectories | нет | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | sqlserverregistrations | нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | registrations | нет | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | batchaccounts |  Учетные записи пакетной службы нельзя переместить непосредственно из одного региона в другой, но можно использовать шаблон для экспорта шаблона, его изменения и развертывания шаблона в новом регионе. <br/><br/> Сведения о [перемещении учетной записи пакетной службы между регионами](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | clusters | нет <br/><br/> Служба Azure Batch AI [прекращена](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai).
> | fileservers | нет | 
> | jobs | нет | 
> | workspaces | нет | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | mapapis | нет | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | biztalk | нет | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | blockchainmembers | нет <br/><br/> Сеть блокчейн не может иметь узлы в разных регионах. 
> | watchers | нет | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | blueprintassignments | нет | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | botservices | нет | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | redis | нет | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | нет |
> | профили | нет | 
> | profiles / endpoints | нет | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | certificateorders | нет | 


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
> | virtualnetworks | нет | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | storageaccounts | Да |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | нет | 
> | Когнитивный поиск | Поддерживается с ручными действиями.<br/><br/> Дополнительные сведения о [перемещении службы когнитивный Поиск Azure в другой регион](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | availabilitysets | Да <br/><br/> Используйте перемещение [ресурсов Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) для перемещения групп доступности. | 
> | diskencryptionsets | нет | 
> | disks | Да <br/><br/> Используйте перемещение [ресурсов Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) для перемещения виртуальных машин Azure и связанных дисков. | 
> | galleries | нет | 
> | galleries / images | нет | 
> | galleries / images / versions | нет | 
> | hostgroups | нет | 
> | hostgroups / hosts | нет | 
> | images | нет | 
> | proximityplacementgroups | нет | 
> | restorepointcollections | нет | 
> | sharedvmimages | нет | 
> | sharedvmimages / versions | нет | 
> | snapshots | нет | 
> | virtualmachines | Да <br/><br/> Перемещение виртуальных машин Azure с помощью [перемещения ресурсов Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | virtualmachines / extensions | нет | 
> | virtualmachinescalesets | нет | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | containergroups | нет | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | containergroups | нет | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | registries | нет |  
> | registries / buildtasks | нет |  
> | registries / replications | нет | 
> | registries / tasks | нет |  
> | registries / webhooks | нет | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | containerservices | Нет.<br/><br/> Служба [прекращена](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/).
> | managedclusters | нет | 
> | openshiftmanagedclusters | нет | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | веб-масштабированием; | нет | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | соединители | нет |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | hubs | нет |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | resourceproviders | нет | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | jobs | нет | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | databoxedgedevices | нет | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | workspaces | нет | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | catalogs | нет | 
> | datacatalogs | нет | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | connectionmanagers | нет | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | Пакеты | нет | 
> | Планы | нет | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | datafactories | нет | 
> | factories | нет |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | datalakeaccounts | нет | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | services; | нет | 
> | services / projects | нет | 
> | slots | нет | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | servers | Для перемещения существующего сервера можно использовать реплику чтения между регионами. [Подробнее.](../../postgresql/howto-move-regions-portal.md)<br/><br/> Если служба подготовлена с геоизбыточным хранилищем резервных копий, можно использовать геовосстановление для восстановления в других регионах. [Подробнее.](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | servers | Для перемещения существующего сервера можно использовать реплику чтения между регионами. [Подробнее.](../../mysql/howto-move-regions-portal.md)

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | servergroups | нет | 
> | servers | Для перемещения существующего сервера можно использовать реплику чтения между регионами. Дополнительные [сведения](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | нет | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | artifactsources | нет | 
> | rollouts | нет |  
> | servicetopologies | нет | 
> | servicetopologies / services | нет |  
> | servicetopologies / services / serviceunits | нет | 
> | steps | нет | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | elasticpools | Нет. Ресурс не предоставлен.
> | elasticpools / iothubtenants | Нет. Ресурс не предоставлен.
> | iothubs | Да. [Подробнее](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | нет | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | controllers | нет | 
> | Кластер AKS | нет<br/><br/> Дополнительные [сведения](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) о переходе в другой регион.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | labcenters | нет | 
> | labs | нет | 
> | labs / environments | нет |  
> | labs / servicerunners | нет | 
> | labs / virtualmachines | нет |  
> | schedules | нет |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | databaseaccounts | нет | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | domains | нет | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | services; | нет |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | domains | нет |  
> | topics | нет | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | clusters | нет |  
> | пространства имен | Да (с шаблоном)<br/><br/> [Перемещение пространства имен концентратора событий в другой регион](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | hanainstances | нет | 
> | sapmonitors | нет |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | clusters | нет | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | services; | нет |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | machines | нет | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | datamanagers |  нет | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | jobs |  нет | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет. [Подробнее.](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region)
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
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | checknameavailability |  Нет.<br/><br/> IoT Central работает с географическими и не регионами.
> | graph | нет

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> |  iothub |  Да (концентратор клонирования) <br/><br/> [Клонирование центра Интернета вещей в другой регион](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | checknameavailability |  нет |  
> | graph |  нет | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | hsmpools | нет | 
> | vaults |  нет | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | clusters |  нет |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | labaccounts | нет | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | Нет, это глобальная служба.

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | hostingenvironments | нет | 
> | integrationaccounts |  нет |  
> | integrationserviceenvironments | нет | 
> | isolatedenvironments | нет | 
> | workflows |  нет |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | commitmentplans |  нет | 
> | webservices |  нет | 
> | workspaces |  нет | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | operationalizationclusters |  нет | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | нет | 
> | accounts / workspaces | нет | 
> | accounts / workspaces / projects | нет | 
> | teamaccounts | нет | 
> | teamaccounts / workspaces | нет | 
> | teamaccounts / workspaces / projects | нет | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | hostingaccounts | нет | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | workspaces | нет | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | userassignedidentities | нет | 

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
> | mediaservices |  нет | 
> | mediaservices / liveevents |  нет | 
> | mediaservices / streamingendpoints |  нет | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | appclusters | нет | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | assessmentprojects | нет | 
> | migrateprojects | нет | 
> | projects | нет | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | netappaccounts | нет | 
> | netappaccounts / capacitypools | нет | 
> | netappaccounts / capacitypools / volumes | нет | 
> | netappaccounts / capacitypools / volumes / mounttargets | нет | 
> | netappaccounts / capacitypools / volumes / snapshots | нет | 

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
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
> | loadbalancers | Да <br/><br/> Используйте перемещение [ресурсов Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) для перемещения внутренних и внешних подсистем балансировки нагрузки. |
> | localnetworkgateways |  нет | 
> | natgateways |  нет | 
> | networkintentpolicies |  нет | 
> | networkinterfaces | Да <br/><br/> Перемещение сетевых карт с помощью перемещения [ресурсов Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | networkprofiles | нет | 
> | networksecuritygroups | Да <br/><br/> Используйте перемещение [ресурсов Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) для перемещения групп безопасности сети (нгсс). | 
> | networkwatchers |  нет |  
> | networkwatchers / connectionmonitors |  нет | 
> | нетворкватчерс/lenses |  нет | 
> | networkwatchers / pingmeshes |  нет | 
> | p2svpngateways | нет | 
> | privatednszones |  нет |  
> | privatednszones / virtualnetworklinks |  нет |  
> | privateendpoints | нет | 
> | privatelinkservices | нет | 
> | publicipaddresses | Да<br/><br/> Используйте перемещение [ресурсов Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) для перемещения общедоступных IP-адресов. |
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
> | vpngateways (Virtual WAN) | нет | 
> | vpnsites (Virtual WAN) | нет | 
> | webapplicationfirewallpolicies |  нет | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | пространства имен |  нет | 
> | namespaces / notificationhubs |  нет |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | workspaces |  нет | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | managementconfigurations |  нет | 
> | узел "Представления" |  нет | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | peerings | нет | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | dashboards | нет | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | rootresources | нет | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | workspacecollections |  нет | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | capacities |  нет | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | accounts | нет | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | vaults | Нет.<br/><br/> Перемещение хранилищ служб восстановления для Azure Backup в регионах Azure не поддерживается.<br/><br/> В хранилищах служб восстановления для Azure Site Recovery можно [Отключить и повторно создать хранилище](../../site-recovery/move-vaults-across-regions.md) в целевом регионе. | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | пространства имен |  нет | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | Запросы |  нет |  

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона |
> | ------------- | ----------- |
> | deploymentScripts |  Да<br/><br/>[Перемещение ресурсов Microsoft. Resources в новый регион](microsoft-resources-move-regions.md) |
> | темплатеспекс |  Да<br/><br/>[Перемещение ресурсов Microsoft. Resources в новый регион](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | веб-масштабированием; |  нет | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | flows |  нет |  
> | jobcollections |  нет | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | searchservices |  нет | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  нет | 
> | плайбукконфигуратионс | нет | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | gateways | нет | 
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
> | веб-масштабированием; | нет | 
> | clusters |  нет | 
> | clusters / applications | нет | 
> | containergroups | нет | 
> | containergroupsets | нет | 
> | edgeclusters | нет | 
> | networks | нет | 
> | secretstores | нет | 
> | volumes. | нет | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | веб-масштабированием; |  нет | 
> | containergroups | нет | 
> | gateways |  нет | 
> | networks |  нет | 
> | секретные коды |  нет | 
> | volumes. |  нет |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | signalr |  нет |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | appliancedefinitions | нет | 
> | appliances | нет | 
> | applicationdefinitions | нет | 
> | веб-масштабированием; | нет | 
> | jitrequests | нет | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | instancepools | нет | 
> | managedinstances | Да <br/><br/> [Дополнительные сведения](../../azure-sql/database/move-resources-across-regions.md) о перемещении управляемых экземпляров между регионами. | 
> | managedinstances / databases | Да | 
> | servers | Да | 
> | servers / databases | Да <br/><br/> [Узнайте больше](../../azure-sql/database/move-resources-across-regions.md) о перемещении баз данных между регионами.<br/><br/> [Дополнительные сведения](../../resource-mover/tutorial-move-region-sql.md) об использовании средства перемещения ресурсов Azure для перемещения баз данных SQL Azure.  | 
> | servers / elasticpools | Да <br/><br/> [Узнайте больше](../../azure-sql/database/move-resources-across-regions.md) о перемещении пулов эластичных БД по регионам.<br/><br/> [Узнайте больше](../../resource-mover/tutorial-move-region-sql.md) об использовании перемещения ресурсов Azure для перемещения эластичных ПУЛОВ Azure SQL.  | 
> | virtualclusters | Да | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  нет |  
> | sqlvirtualmachines |  нет |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | dwvm | нет | 

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | storageaccounts | Да<br/><br/> [Перемещение учетной записи хранения Azure в другой регион](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | caches | нет | 

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
> | storagesyncservices | нет | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | managers | нет | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | streamingjobs |  нет |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | environments | нет | 
> | environments / eventsources | нет | 
> | instances | нет | 
> | instances / environments | нет | 
> | instances / environments / eventsources | нет | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | providerregistrations | нет | 
> | ресурсов | нет | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | environments |  нет | 
> | environments / eventsources |  нет |  
> | environments / referencedatasets |  нет | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | stores | нет | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | imagetemplates | нет | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | account |  нет | 
> | account / extension |  нет | 
> | account / project |  нет | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | нет | 
> | dedicatedcloudservices | нет | 
> | virtualmachines | нет | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Перемещение региона | 
> | ------------- | ----------- |
> | certificates | нет | 
> | connectiongateways |  нет |  
> | connections |  нет |  
> | customapis |  нет | 
> | hostingenvironments | нет | 
> | serverfarms |  нет |  
> | sites |  нет | 
> | sites / premieraddons |  нет |  
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
> | applicationgroups | нет | 
> | hostpools | нет | 
> | workspaces | нет | 

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.