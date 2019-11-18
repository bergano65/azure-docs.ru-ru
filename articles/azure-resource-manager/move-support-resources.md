---
title: Поддержка операций перемещения по типу ресурса
description: Список типов ресурсов Azure, подлежащих перемещению в новую группу ресурсов или подписку.
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: 182e5fdd081126500c04f44776c9e9b8fad61843
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149769"
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
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. Виндовсвиртуалдесктоп](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | domainservices | Нет | Нет |
> | DomainServices/репликасетс | Нет | Нет |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | tenants | Нет | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | актионрулес | Yes | Yes |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | servers | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | Служба | Yes | Yes |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | конфигуратионсторес | Yes | Yes |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | apiapps | Нет | Нет |
> | appidentities | Нет | Нет |
> | gateways | Нет | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | policyassignments | Нет | Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Yes | Yes |
> | automationaccounts и конфигурации | Yes | Yes |
> | automationaccounts и модули Runbook | Yes | Yes |

> [!IMPORTANT]
> Модули Runbook должны находиться в той же группе ресурсов, что и учетная запись службы автоматизации.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Yes | Yes |

## <a name="microsoftazuredata"></a>Microsoft. Азуредата

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | склсерверрегистратионс | Нет | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | registrations | Yes | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Yes | Yes |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | clusters | Нет | Нет |
> | fileservers | Нет | Нет |
> | jobs | Нет | Нет |
> | workspaces | Нет | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | mapapis | Нет | Нет |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | biztalk | Yes | Yes |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Yes | Yes |
> | наблюдатели | Нет | Нет |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Нет | Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | botservices | Yes | Yes |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | redis | Yes | Yes |

> [!IMPORTANT]
> Если для экземпляра кэша Azure для Redis настроена виртуальная сеть, экземпляр нельзя переместить в другую подписку. См. раздел [ограничения на перемещение в сети](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | кднвебаппликатионфиреваллполиЦиес | Нет | Нет |
> | профили | Yes | Yes |
> | профили и конечные точки | Yes | Yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | certificateorders | Yes | Yes |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | domainnames | Yes | Нет |
> | virtualmachines | Yes | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Нет | Нет |
> | reservedips | Нет | Нет |
> | virtualnetworks | Нет | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Yes | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Yes | Yes |
> | дискенкриптионсетс | Нет | Нет |
> | disks | Yes | Yes |
> | galleries | Нет | Нет |
> | коллекции и изображения | Нет | Нет |
> | коллекции, изображения и версии | Нет | Нет |
> | хостграупс | Нет | Нет |
> | хостграупс и узлы | Нет | Нет |
> | images | Yes | Yes |
> | proximityplacementgroups | Нет | Нет |
> | restorepointcollections | Нет | Нет |
> | sharedvmimages | Нет | Нет |
> | шаредвмимажес и версии | Нет | Нет |
> | snapshots | Yes | Yes |
> | virtualmachines | Yes | Yes |
> | virtualmachines и расширения | Yes | Yes |
> | virtualmachinescalesets | Yes | Yes |

> [!IMPORTANT]
> См. [руководство по перемещению виртуальных машин](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | containergroups | Нет | Нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | containergroups | Нет | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | registries | Yes | Yes |
> | реестры и буилдтаскс | Yes | Yes |
> | реестры и репликации | Yes | Yes |
> | реестры и задачи | Yes | Yes |
> | реестры и веб-перехватчики | Yes | Yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | containerservices | Нет | Нет |
> | managedclusters | Нет | Нет |
> | openshiftmanagedclusters | Нет | Нет |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | Yes | Yes |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | соединители | Yes | Yes |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | hubs | Yes | Yes |

## <a name="microsoftcustomproviders"></a>Microsoft. Кустомпровидерс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | ресаурцепровидерс | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | jobs | Нет | Нет |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Нет | Нет |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | workspaces | Нет | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | catalogs | Yes | Yes |
> | каталоги | Нет | Нет |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Нет | Нет |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | Пакеты | Нет | Нет |
> | Планы | Нет | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | datafactories | Yes | Yes |
> | factories | Yes | Yes |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Нет | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | services; | Нет | Нет |
> | службы и проекты | Нет | Нет |
> | slots | Нет | Нет |

## <a name="microsoftdatashare"></a>Общая папка Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | servers | Yes | Yes |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | servers | Yes | Yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | servergroups | Нет | Нет |
> | servers | Yes | Yes |
> | serversv2 | Yes | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | artifactsources | Yes | Yes |
> | rollouts | Yes | Yes |
> | servicetopologies | Yes | Yes |
> | сервицетопологиес и службы | Yes | Yes |
> | сервицетопологиес/Services/сервицеунитс | Yes | Yes |
> | steps | Yes | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | elasticpools | Нет | Нет |
> | еластикпулс/иосубтенантс | Нет | Нет |
> | iothubs | Yes | Yes |
> | provisioningservices | Yes | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | controllers | Yes | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | labcenters | Нет | Нет |
> | labs | Yes | Нет |
> | лаборатории и среды | Yes | Yes |
> | Labs и сервицеруннерс | Yes | Yes |
> | Labs и virtualmachines | Yes | Нет |
> | schedules | Yes | Yes |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Yes | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | domains | Yes | Yes |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | services; | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | domains | Yes | Yes |
> | topics | Yes | Yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | clusters | Yes | Yes |
> | namespaces | Yes | Yes |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | hanainstances | Нет | Нет |
> | сапмониторс | Yes | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | clusters | Yes | Yes |

> [!IMPORTANT]
> Кластеры HDInsight можно переместить в новую подписку или группу ресурсов. Однако перемещение между подписками недоступно для сетевых ресурсов, связанных с кластером HDInsight (таких как виртуальная сеть, сетевой адаптер или подсистема балансировки нагрузки). Также невозможно переместить в новую группу ресурсов сетевой адаптер, который подключен к виртуальной машине кластера.
>
> При перемещении кластера HDInsight в новую подписку сначала необходимо переместить другие ресурсы (такие как учетная запись хранения). А затем переместить сам кластер HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | services; | Yes | Yes |

## <a name="microsofthybridcompute"></a>Microsoft. Хибридкомпуте

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | виртуальных | Нет | Нет |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | datamanagers | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | jobs | Yes | Yes |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |
> | actiongroups | Yes | Yes |
> | activitylogalerts | Нет | Нет |
> | alertrules | Yes | Yes |
> | autoscalesettings | Yes | Yes |
> | components | Yes | Yes |
> | guestdiagnosticsettings | Нет | Нет |
> | metricalerts | Нет | Нет |
> | notificationgroups | Нет | Нет |
> | notificationrules | Нет | Нет |
> | scheduledqueryrules | Yes | Yes |
> | webtests | Yes | Yes |
> | workbooks | Yes | Yes |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | iotapps | Yes | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Yes | Yes |
> | graph | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | hsmpools | Нет | Нет |
> | vaults | Yes | Yes |

> [!IMPORTANT]
> Хранилища ключей, используемые для шифрования дисков, нельзя переместить в группу ресурсов в одной и той же подписке или в рамках подписок.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | clusters | Yes | Yes |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | labaccounts | Нет | Нет |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Нет | Нет |
> | integrationaccounts | Yes | Yes |
> | integrationserviceenvironments | Нет | Нет |
> | isolatedenvironments | Нет | Нет |
> | workflows | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Yes | Yes |
> | webservices | Yes | Нет |
> | workspaces | Yes | Yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Yes | Yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |
> | учетные записи и рабочие области | Нет | Нет |
> | учетные записи, рабочие области и проекты | Нет | Нет |
> | teamaccounts | Нет | Нет |
> | теамаккаунтс и рабочие области | Нет | Нет |
> | теамаккаунтс/рабочие области и проекты | Нет | Нет |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Нет | Нет |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | workspaces | Нет | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | userassignedidentities | Нет | Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Нет | Нет |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | mediaservices | Yes | Yes |
> | mediaservices/лививентс | Yes | Yes |
> | mediaservices/streamingendpoint | Yes | Yes |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | аппклустерс | Нет | Нет |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Нет | Нет |
> | migrateprojects | Нет | Нет |
> | projects | Нет | Нет |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Нет | Нет |
> | нетаппаккаунтс/капаЦитипулс | Нет | Нет |
> | нетаппаккаунтс/капаЦитипулс/тома | Нет | Нет |
> | нетаппаккаунтс/капаЦитипулс/Volumes/маунттаржетс | Нет | Нет |
> | нетаппаккаунтс/капаЦитипулс/тома/моментальные снимки | Нет | Нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Нет | Нет |
> | аппликатионгатевайвебаппликатионфиреваллполиЦиес | Нет | Нет |
> | applicationsecuritygroups | Yes | Yes |
> | azurefirewalls | Yes | Yes |
> | bastionhosts | Нет | Нет |
> | connections | Yes | Yes |
> | ddoscustompolicies | Yes | Yes |
> | ddosprotectionplans | Нет | Нет |
> | dnszones | Yes | Yes |
> | expressroutecircuits | Нет | Нет |
> | expressroutecrossconnections | Нет | Нет |
> | expressroutegateways | Нет | Нет |
> | expressrouteports | Нет | Нет |
> | frontdoors | Нет | Нет |
> | frontdoorwebapplicationfirewallpolicies | Нет | Нет |
> | loadbalancers | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br>SKU No-Standard |
> | localnetworkgateways | Yes | Yes |
> | natgateways | Yes | Yes |
> | networkintentpolicies | Yes | Yes |
> | networkinterfaces | Yes | Yes |
> | networkprofiles | Нет | Нет |
> | networksecuritygroups | Yes | Yes |
> | networkwatchers | Yes | Yes |
> | нетворкватчерс/коннектионмониторс | Yes | Yes |
> | нетворкватчерс/lenses | Yes | Yes |
> | нетворкватчерс/пингмешес | Yes | Yes |
> | p2svpngateways | Нет | Нет |
> | приватеднсзонес | Yes | Yes |
> | приватеднсзонес/виртуалнетворклинкс | Yes | Yes |
> | приватиндпоинтс | Нет | Нет |
> | privatelinkservices | Нет | Нет |
> | publicipaddresses | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br>SKU No-Standard |
> | publicipprefixes | Yes | Yes |
> | routefilters | Нет | Нет |
> | routetables | Yes | Yes |
> | serviceendpointpolicies | Yes | Yes |
> | trafficmanagerprofiles | Yes | Yes |
> | virtualhubs | Нет | Нет |
> | virtualnetworkgateways | Yes | Yes |
> | virtualnetworks | Yes | Yes |
> | virtualnetworktaps | Нет | Нет |
> | virtualwans | Нет | Нет |
> | впнгатевайс (Виртуальная глобальная сеть) | Нет | Нет |
> | впнситес (Виртуальная глобальная сеть) | Нет | Нет |
> | webapplicationfirewallpolicies | Yes | Yes |

> [!IMPORTANT]
> См. [руководство по перемещению в сеть](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | namespaces | Yes | Yes |
> | пространства имен/notificationhubs | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | workspaces | Yes | Yes |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | Yes | Yes |
> | solutions | Yes | Yes |
> | узел "Представления" | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | пиринги | Нет | Нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | dashboards | Yes | Yes |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | rootresources | Нет | Нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | capacities | Yes | Yes |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | vaults | Yes | Yes |

> [!IMPORTANT]
> См. [руководство по перемещению служб восстановления](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | namespaces | Yes | Yes |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | Запросы | Yes | Yes |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | Yes | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | flows | Yes | Yes |
> | jobcollections | Yes | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | searchservices | Yes | Yes |

> [!IMPORTANT]
> В одной операции невозможно переместить несколько ресурсов поиска в разных регионах. Переместите их в отдельных операциях.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | иотсекуритисолутионс | Yes | Yes |
> | плайбукконфигуратионс | Нет | Нет |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | gateways | Нет | Нет |
> | Узлы | Нет | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | namespaces | Yes | Yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | Нет | Нет |
> | clusters | Yes | Yes |
> | кластеры и приложения | Нет | Нет |
> | containergroups | Нет | Нет |
> | containergroupsets | Нет | Нет |
> | edgeclusters | Нет | Нет |
> | networks | Нет | Нет |
> | secretstores | Нет | Нет |
> | volumes. | Нет | Нет |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | Yes | Yes |
> | containergroups | Нет | Нет |
> | gateways | Yes | Yes |
> | networks | Yes | Yes |
> | секретные коды | Yes | Yes |
> | volumes. | Yes | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | signalr | Yes | Yes |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | Нет | Нет |
> | appliances | Нет | Нет |
> | applicationdefinitions | Нет | Нет |
> | веб-масштабированием; | Нет | Нет |
> | jitrequests | Нет | Нет |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | инстанцепулс | Нет | Нет |
> | managedinstances | Нет | Нет |
> | манажединстанцес и базы данных | Нет | Нет |
> | servers | Yes | Yes |
> | серверы и базы данных | Yes | Yes |
> | серверы и еластикпулс | Yes | Yes |
> | virtualclusters | Yes | Yes |

> [!IMPORTANT]
> База данных и сервер должны находиться в одной группе ресурсов. При перемещении сервера SQL Server все его базы данных также перемещаются. Такое поведение также характерно для баз данных SQL Azure и баз данных хранилища данных SQL Azure.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Yes | Yes |
> | sqlvirtualmachines | Yes | Yes |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | dwvm | Нет | Нет |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Yes | Yes |

## <a name="microsoftstoragecache"></a>Microsoft. Сторажекаче

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | кэширует | Нет | Нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Yes | Yes |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Нет | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Нет | Нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | managers | Нет | Нет |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Yes | Yes |

> [!IMPORTANT]
> Невозможно переместить задания Stream Analytics в состоянии выполнения.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | environments | Нет | Нет |
> | среды и классов EventSource | Нет | Нет |
> | instances | Нет | Нет |
> | экземпляры и среды | Нет | Нет |
> | экземпляры/среды/классов EventSource | Нет | Нет |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Нет | Нет |
> | ресурсов | Нет | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | environments | Yes | Yes |
> | среды и классов EventSource | Yes | Yes |
> | среды и референцедатасетс | Yes | Yes |

## <a name="microsofttoken"></a>Microsoft. Token

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | сохраняют | Нет | Нет |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Нет | Нет |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | учетная запись | Yes | Yes |
> | Учетная запись или расширение | Yes | Yes |
> | Учетная запись или проект | Yes | Yes |

> [!IMPORTANT]
> Чтобы изменить подписку на Azure DevOps, см. статью [изменение подписки Azure, используемой для выставления счетов](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | дедикатедклауднодес | Нет | Нет |
> | дедикатедклаудсервицес | Нет | Нет |
> | virtualmachines | Нет | Нет |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | certificates | Нет | Yes |
> | connectiongateways | Yes | Yes |
> | connections | Yes | Yes |
> | customapis | Yes | Yes |
> | hostingenvironments | Нет | Нет |
> | serverfarms | Yes | Yes |
> | sites | Yes | Yes |
> | Sites/премиераддонс | Yes | Yes |
> | сайты и слоты | Yes | Yes |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | deviceservices | Нет | Нет |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. Виндовсвиртуалдесктоп

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | подписку |
> | ------------- | ----------- | ---------- |
> | аппликатионграупс | Нет | Нет |
> | хостпулс | Нет | Нет |
> | workspaces | Нет | Нет |

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.

## <a name="next-steps"></a>Дополнительная информация
Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
