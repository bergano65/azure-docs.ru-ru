---
title: Поддержка операций перемещения по типу ресурса
description: Список типов ресурсов Azure, подлежащих перемещению в новую группу ресурсов или подписку.
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: 65f50fe63485c2538cdef3d144c4d77824d95f56
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659395"
---
# <a name="move-operation-support-for-resources"></a>Поддержка операции перемещения для ресурсов
В этой статье указано, поддерживается ли для тех или иных типов ресурсов Azure операция перемещения. В нем также содержатся сведения о специальных условиях, которые следует учитывать при перемещении ресурса.

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
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. Виндовсвиртуалдесктоп](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | Нет | Нет |
> | DomainServices/репликасетс | Нет | Нет |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | Нет | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | актионрулес | Да | Да |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | серверы | Да | Да |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | служба | Да | Да |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | конфигуратионсторес | Да | Да |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | Нет | Нет |
> | appidentities | Нет | Нет |
> | gateways | Нет | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | policyassignments | Нет | Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Да | Да |
> | automationaccounts и конфигурации | Да | Да |
> | automationaccounts и модули Runbook | Да | Да |

> [!IMPORTANT]
> Модули Runbook должны находиться в той же группе ресурсов, что и учетная запись службы автоматизации.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Да | Да |

## <a name="microsoftazuredata"></a>Microsoft. Азуредата

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | склсерверрегистратионс | Нет | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | registrations | Да | Да |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Да | Да |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Нет | Нет |
> | fileservers | Нет | Нет |
> | jobs | Нет | Нет |
> | workspaces | Нет | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | Нет | Нет |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | Biztalk | Да | Да |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Да | Да |
> | наблюдатели | Нет | Нет |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Нет | Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | Да | Да |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | Да | Да |

> [!IMPORTANT]
> Если для экземпляра кэша Azure для Redis настроена виртуальная сеть, экземпляр нельзя переместить в другую подписку. См. раздел [ограничения на перемещение в сети](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | кднвебаппликатионфиреваллполиЦиес | Нет | Нет |
> | профили | Да | Да |
> | профили и конечные точки | Да | Да |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | Да | Нет |
> | virtualmachines | Да | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Нет | Нет |
> | reservedips | Нет | Нет |
> | virtualnetworks | Нет | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Да | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Да | Да |
> | дискенкриптионсетс | Нет | Нет |
> | disks | Да | Да |
> | galleries | Нет | Нет |
> | коллекции и изображения | Нет | Нет |
> | коллекции, изображения и версии | Нет | Нет |
> | хостграупс | Нет | Нет |
> | хостграупс и узлы | Нет | Нет |
> | images | Да | Да |
> | proximityplacementgroups | Нет | Нет |
> | restorepointcollections | Нет | Нет |
> | sharedvmimages | Нет | Нет |
> | шаредвмимажес и версии | Нет | Нет |
> | snapshots | Да | Да |
> | virtualmachines | Да | Да |
> | virtualmachines и расширения | Да | Да |
> | virtualmachinescalesets | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению виртуальных машин](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Нет | Нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Нет | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | Да | Да |
> | реестры и буилдтаскс | Да | Да |
> | реестры и репликации | Да | Да |
> | реестры и задачи | Да | Да |
> | реестры и веб-перехватчики | Да | Да |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | Нет | Нет |
> | managedclusters | Нет | Нет |
> | openshiftmanagedclusters | Нет | Нет |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | приложений | Да | Да |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | соединители | Да | Да |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | Да | Да |

## <a name="microsoftcustomproviders"></a>Microsoft. Кустомпровидерс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | ресаурцепровидерс | Да | Да |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Нет | Нет |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Нет | Нет |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Нет | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | Да | Да |
> | каталоги | Нет | Нет |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Нет | Нет |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | Пакеты | Нет | Нет |
> | Планы | Нет | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | Да | Да |
> | factories | Да | Да |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Нет | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | services; | Нет | Нет |
> | службы и проекты | Нет | Нет |
> | slots | Нет | Нет |

## <a name="microsoftdatashare"></a>Общая папка Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | серверы | Да | Да |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | серверы | Да | Да |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | Нет | Нет |
> | серверы | Да | Да |
> | serversv2 | Да | Да |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | Да | Да |
> | rollouts | Да | Да |
> | servicetopologies | Да | Да |
> | сервицетопологиес и службы | Да | Да |
> | сервицетопологиес/Services/сервицеунитс | Да | Да |
> | steps | Да | Да |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | Нет | Нет |
> | еластикпулс/иосубтенантс | Нет | Нет |
> | iothubs | Да | Да |
> | provisioningservices | Да | Да |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | Да | Да |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | Нет | Нет |
> | labs | Да | Нет |
> | лаборатории и среды | Да | Да |
> | Labs и сервицеруннерс | Да | Да |
> | Labs и virtualmachines | Да | Нет |
> | schedules | Да | Да |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Да | Да |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Да | Да |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | services; | Да | Да |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Да | Да |
> | eventSubscriptions | Нет — нельзя перемещать независимо, но автоматически перемещать вместе с подписанным ресурсом. | Нет — нельзя перемещать независимо, но автоматически перемещать вместе с подписанным ресурсом. |
> | topics | Да | Да |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Да | Да |
> | пространства имен | Да | Да |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | Нет | Нет |
> | сапмониторс | Да | Да |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Да | Да |

> [!IMPORTANT]
> Кластеры HDInsight можно переместить в новую подписку или группу ресурсов. Однако перемещение между подписками недоступно для сетевых ресурсов, связанных с кластером HDInsight (таких как виртуальная сеть, сетевой адаптер или подсистема балансировки нагрузки). Также невозможно переместить в новую группу ресурсов сетевой адаптер, который подключен к виртуальной машине кластера.
>
> При перемещении кластера HDInsight в новую подписку сначала необходимо переместить другие ресурсы (такие как учетная запись хранения). А затем переместить сам кластер HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | services; | Да | Да |

## <a name="microsofthybridcompute"></a>Microsoft. Хибридкомпуте

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | виртуальных | Нет | Нет |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | Да | Да |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Да | Да |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |
> | actiongroups | Да | Да |
> | activitylogalerts | Нет | Нет |
> | alertrules | Да | Да |
> | autoscalesettings | Да | Да |
> | components | Да | Да |
> | guestdiagnosticsettings | Нет | Нет |
> | metricalerts | Нет | Нет |
> | notificationgroups | Нет | Нет |
> | notificationrules | Нет | Нет |
> | scheduledqueryrules | Да | Да |
> | webtests | Да | Да |
> | workbooks | Да | Да |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | Да | Да |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Да | Да |
> | graph | Да | Да |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | hsmpools | Нет | Нет |
> | vaults | Да | Да |

> [!IMPORTANT]
> Хранилища ключей, используемые для шифрования дисков, нельзя переместить в группу ресурсов в одной и той же подписке или в рамках подписок.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Да | Да |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | Нет | Нет |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Нет | Нет |
> | integrationaccounts | Да | Да |
> | integrationserviceenvironments | Нет | Нет |
> | isolatedenvironments | Нет | Нет |
> | workflows | Да | Да |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Да | Да |
> | webservices | Да | Нет |
> | workspaces | Да | Да |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Да | Да |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |
> | учетные записи и рабочие области | Нет | Нет |
> | учетные записи, рабочие области и проекты | Нет | Нет |
> | teamaccounts | Нет | Нет |
> | теамаккаунтс и рабочие области | Нет | Нет |
> | теамаккаунтс/рабочие области и проекты | Нет | Нет |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Нет | Нет |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Нет | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | userassignedidentities | Нет | Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Нет | Нет |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | Да | Да |
> | mediaservices/лививентс | Да | Да |
> | mediaservices/streamingendpoint | Да | Да |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | аппклустерс | Нет | Нет |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Нет | Нет |
> | migrateprojects | Нет | Нет |
> | projects | Нет | Нет |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Нет | Нет |
> | нетаппаккаунтс/капаЦитипулс | Нет | Нет |
> | нетаппаккаунтс/капаЦитипулс/тома | Нет | Нет |
> | нетаппаккаунтс/капаЦитипулс/Volumes/маунттаржетс | Нет | Нет |
> | нетаппаккаунтс/капаЦитипулс/тома/моментальные снимки | Нет | Нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Нет | Нет |
> | аппликатионгатевайвебаппликатионфиреваллполиЦиес | Нет | Нет |
> | applicationsecuritygroups | Да | Да |
> | azurefirewalls | Да | Да |
> | bastionhosts | Нет | Нет |
> | connections | Да | Да |
> | ddoscustompolicies | Да | Да |
> | ddosprotectionplans | Нет | Нет |
> | dnszones | Да | Да |
> | expressroutecircuits | Нет | Нет |
> | expressroutecrossconnections | Нет | Нет |
> | expressroutegateways | Нет | Нет |
> | expressrouteports | Нет | Нет |
> | frontdoors | Нет | Нет |
> | frontdoorwebapplicationfirewallpolicies | Нет | Нет |
> | loadbalancers | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br>SKU No-Standard |
> | localnetworkgateways | Да | Да |
> | natgateways | Да | Да |
> | networkintentpolicies | Да | Да |
> | networkinterfaces | Да | Да |
> | networkprofiles | Нет | Нет |
> | networksecuritygroups | Да | Да |
> | networkwatchers | Да | Да |
> | нетворкватчерс/коннектионмониторс | Да | Да |
> | нетворкватчерс/lenses | Да | Да |
> | нетворкватчерс/пингмешес | Да | Да |
> | p2svpngateways | Нет | Нет |
> | приватеднсзонес | Да | Да |
> | приватеднсзонес/виртуалнетворклинкс | Да | Да |
> | приватиндпоинтс | Нет | Нет |
> | privatelinkservices | Нет | Нет |
> | publicipaddresses | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br>SKU No-Standard |
> | publicipprefixes | Да | Да |
> | routefilters | Нет | Нет |
> | routetables | Да | Да |
> | securegateways | Да | Да |
> | serviceendpointpolicies | Да | Да |
> | trafficmanagerprofiles | Да | Да |
> | virtualhubs | Нет | Нет |
> | virtualnetworkgateways | Да | Да |
> | virtualnetworks | Да | Да |
> | virtualnetworktaps | Нет | Нет |
> | virtualwans | Нет | Нет |
> | впнгатевайс (Виртуальная глобальная сеть) | Нет | Нет |
> | впнситес (Виртуальная глобальная сеть) | Нет | Нет |
> | webapplicationfirewallpolicies | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению в сеть](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | пространства имен | Да | Да |
> | пространства имен/notificationhubs | Да | Да |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Да | Да |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | Да | Да |
> | solutions | Да | Да |
> | узел "Представления" | Да | Да |

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | пиринги | Нет | Нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | dashboards | Да | Да |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | Нет | Нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Да | Да |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | Да | Да |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | vaults | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению служб восстановления](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | пространства имен | Да | Да |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | запросы | Да | Да |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | приложений | Да | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | flows | Да | Да |
> | jobcollections | Да | Да |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | Да | Да |

> [!IMPORTANT]
> В одной операции невозможно переместить несколько ресурсов поиска в разных регионах. Переместите их в отдельных операциях.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | иотсекуритисолутионс | Да | Да |
> | плайбукконфигуратионс | Нет | Нет |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | Нет | Нет |
> | Узлы | Нет | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | пространства имен | Да | Да |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | приложений | Нет | Нет |
> | clusters | Да | Да |
> | кластеры и приложения | Нет | Нет |
> | containergroups | Нет | Нет |
> | containergroupsets | Нет | Нет |
> | edgeclusters | Нет | Нет |
> | networks | Нет | Нет |
> | secretstores | Нет | Нет |
> | volumes. | Нет | Нет |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | приложений | Да | Да |
> | containergroups | Нет | Нет |
> | gateways | Да | Да |
> | networks | Да | Да |
> | секретные коды | Да | Да |
> | volumes. | Да | Да |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | Да | Да |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | Нет | Нет |
> | appliances | Нет | Нет |
> | applicationdefinitions | Нет | Нет |
> | приложений | Нет | Нет |
> | jitrequests | Нет | Нет |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | инстанцепулс | Нет | Нет |
> | managedinstances | Нет | Нет |
> | манажединстанцес и базы данных | Нет | Нет |
> | серверы | Да | Да |
> | серверы и базы данных | Да | Да |
> | серверы и еластикпулс | Да | Да |
> | virtualclusters | Да | Да |

> [!IMPORTANT]
> База данных и сервер должны находиться в одной группе ресурсов. При перемещении сервера SQL Server все его базы данных также перемещаются. Такое поведение также характерно для баз данных SQL Azure и баз данных хранилища данных SQL Azure.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Да | Да |
> | sqlvirtualmachines | Да | Да |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | Нет | Нет |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Да | Да |

## <a name="microsoftstoragecache"></a>Microsoft. Сторажекаче

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | Кэширует | Нет | Нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Да | Да |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Нет | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Нет | Нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | Нет | Нет |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Да | Да |

> [!IMPORTANT]
> Невозможно переместить задания Stream Analytics в состоянии выполнения.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Нет | Нет |
> | среды и классов EventSource | Нет | Нет |
> | Экземпляры | Нет | Нет |
> | экземпляры и среды | Нет | Нет |
> | экземпляры/среды/классов EventSource | Нет | Нет |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Нет | Нет |
> | ресурсов | Нет | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Да | Да |
> | среды и классов EventSource | Да | Да |
> | среды и референцедатасетс | Да | Да |

## <a name="microsofttoken"></a>Microsoft. Token

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | Нет | Нет |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Нет | Нет |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | дедикатедклауднодес | Нет | Нет |
> | дедикатедклаудсервицес | Нет | Нет |
> | virtualmachines | Нет | Нет |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | certificates | Нет | Да |
> | connectiongateways | Да | Да |
> | connections | Да | Да |
> | customapis | Да | Да |
> | hostingenvironments | Нет | Нет |
> | serverfarms | Да | Да |
> | sites | Да | Да |
> | Sites/премиераддонс | Да | Да |
> | сайты и слоты | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | Нет | Нет |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. Виндовсвиртуалдесктоп

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | аппликатионграупс | Нет | Нет |
> | хостпулс | Нет | Нет |
> | workspaces | Нет | Нет |

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.

## <a name="next-steps"></a>Дальнейшие действия
Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](move-resource-group-and-subscription.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
