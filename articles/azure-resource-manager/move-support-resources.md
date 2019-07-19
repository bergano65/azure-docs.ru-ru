---
title: Поддержка операции перемещения типом ресурсов Azure
description: Список типов ресурсов Azure, подлежащих перемещению в новую группу ресурсов или подписку.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/9/2019
ms.author: tomfitz
ms.openlocfilehash: 22493ad7998e9014c88c79e6ac5eee3bf1216119
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226818"
---
# <a name="move-operation-support-for-resources"></a>Поддержка операции перемещения для ресурсов
В этой статье указано, поддерживается ли для тех или иных типов ресурсов Azure операция перемещения. В нем также содержатся сведения о специальных условиях, которые следует учитывать при перемещении ресурса.

Переход к пространству имен поставщика ресурсов:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft. аадиам](#microsoftaadiam)
> - [Microsoft. Алертсманажемент](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft. Аппконфигуратион](#microsoftappconfiguration)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. Backup](#microsoftbackup)
> - [Microsoft. Batch](#microsoftbatch)
> - [Microsoft. BatchAI](#microsoftbatchai)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. Бизталксервицес](#microsoftbiztalkservices)
> - [Microsoft. Блокчейн](#microsoftblockchain)
> - [Microsoft. чертеж](#microsoftblueprint)
> - [Microsoft. Ботсервице](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft. Цертификатерегистратион](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. Классикстораже](#microsoftclassicstorage)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. COMPUTE](#microsoftcompute)
> - [Microsoft. Container](#microsoftcontainer)
> - [Microsoft. Контаинеринстанце](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft. Контентмодератор](#microsoftcontentmoderator)
> - [Microsoft. Кортанааналитикс](#microsoftcortanaanalytics)
> - [Microsoft. Костманажемент](#microsoftcostmanagement)
> - [Microsoft. Кустомеринсигхтс](#microsoftcustomerinsights)
> - [Microsoft. Датабокс](#microsoftdatabox)
> - [Microsoft. Датабокседже](#microsoftdataboxedge)
> - [Microsoft. кирпичы](#microsoftdatabricks)
> - [Каталог Microsoft.](#microsoftdatacatalog)
> - [Microsoft. соединение](#microsoftdataconnect)
> - [Microsoft. Обмен](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft. Data Lake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft. Перенос](#microsoftdatamigration)
> - [Microsoft. Дбформариадб](#microsoftdbformariadb)
> - [Microsoft. Дбформискл](#microsoftdbformysql)
> - [Microsoft. Дбфорпостгрескл](#microsoftdbforpostgresql)
> - [Microsoft. Деплойментманажер](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. Девспацес](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DNS](#microsoftdns)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. Домаинрегистратион](#microsoftdomainregistration)
> - [Microsoft. Ентерприсекновледжеграф](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. Genomics](#microsoftgenomics)
> - [Microsoft. Ханаоназуре](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft. Хеалскареапис](#microsofthealthcareapis)
> - [Microsoft. Хибридкомпуте](#microsofthybridcompute)
> - [Microsoft. Хибриддата](#microsofthybriddata)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. Иотцентрал](#microsoftiotcentral)
> - [Microsoft. Иотспацес](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft. Kusto](#microsoftkusto)
> - [Microsoft. Лабсервицес](#microsoftlabservices)
> - [Microsoft. Локатионбаседсервицес](#microsoftlocationbasedservices)
> - [Microsoft. файл locationservices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. Мачинелеарнингкомпуте](#microsoftmachinelearningcompute)
> - [Microsoft. Мачинелеарнинжекспериментатион](#microsoftmachinelearningexperimentation)
> - [Microsoft. Мачинелеарнингмоделманажемент](#microsoftmachinelearningmodelmanagement)
> - [Microsoft. Мачинелеарнингоператионализатион](#microsoftmachinelearningoperationalization)
> - [Microsoft. Мачинелеарнингсервицес](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Маркетплацеаппс](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. migrate](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. пиринг](#microsoftpeering)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. Порталсдк](#microsoftportalsdk)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. Повербидедикатед](#microsoftpowerbidedicated)
> - [Microsoft. Прожектоксфорд](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. Серверманажемент](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft. Сервицефабрикмеш](#microsoftservicefabricmesh)
> - [Microsoft. Сигналрсервице](#microsoftsignalrservice)
> - [Microsoft. SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft. Склвиртуалмачине](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. Сторажекаче](#microsoftstoragecache)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. Сторажесинкдев](#microsoftstoragesyncdev)
> - [Microsoft. СторажесинЦинт](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Стреаманалитиксексплорер](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Терраформосс](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. Token](#microsofttoken)
> - [Microsoft. Виртуалмачинеимажес](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. Вмвареклаудсимпле](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft. Виндовсиот](#microsoftwindowsiot)
> - [Microsoft. Виндовсвиртуалдесктоп](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainservices | Нет | Нет |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tenants | Нет | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| актионрулес | Да | Да |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | Да | Да |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| служба | Да | Да |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| конфигуратионсторес | Да | Да |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| apiapps | Нет | Нет |
| appidentities | Нет | Нет |
| gateways | Нет | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| policyassignments | Нет | Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| automationaccounts | Да | Да |
| automationaccounts/configurations | Да | Да |
| automationaccounts/runbooks | Да | Да |

> [!IMPORTANT]
> Модули Runbook должны находиться в той же группе ресурсов, что и учетная запись службы автоматизации.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| b2cdirectories | Да | Да |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| registrations | Да | Да |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| backupvault | Нет | Нет |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| batchaccounts | Да | Да |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Нет | Нет |
| fileservers | Нет | Нет |
| jobs | Нет | Нет |
| workspaces | Нет | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mapapis | Нет | Нет |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| biztalk | Да | Да |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blockchainmembers | Да | Да |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blueprintassignments | Нет | Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| botservices | Да | Да |

## <a name="microsoftcache"></a>Microsoft.Cache
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| redis | Да | Да |

> [!IMPORTANT]
> Если для экземпляра кэша Azure для Redis настроена виртуальная сеть, экземпляр нельзя переместить в другую подписку. См. раздел [ограничения на перемещение виртуальных сетей](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| profiles | Да | Да |
| profiles/endpoints | Да | Да |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| certificateorders | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainnames | Да | Нет |
| virtualmachines | Да | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Нет | Нет |
| reservedips | Нет | Нет |
| virtualnetworks | Нет | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Да | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Да | Да |

## <a name="microsoftcompute"></a>Microsoft.Compute;
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| availabilitysets | Да | Да |
| disks | Да | Да |
| galleries | Нет | Нет |
| galleries/images | Нет | Нет |
| galleries/images/versions | Нет | Нет |
| хостграупс | Нет | Нет |
| хостграупс и узлы | Нет | Нет |
| images | Да | Да |
| proximityplacementgroups | Нет | Нет |
| restorepointcollections | Нет | Нет |
| sharedvmimages | Нет | Нет |
| sharedvmimages/versions | Нет | Нет |
| snapshots | Да | Да |
| virtualmachines | Да | Да |
| virtualmachines/extensions | Да | Да |
| virtualmachinescalesets | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению виртуальных машин](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Нет | Нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Нет | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| registries | Да | Да |
| registries/buildtasks | Да | Да |
| registries/replications | Да | Да |
| registries/tasks | Да | Да |
| registries/webhooks | Да | Да |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containerservices | Нет | Нет |
| managedclusters | Нет | Нет |
| openshiftmanagedclusters | Нет | Нет |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| веб-масштабированием; | Да | Да |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Нет | Нет |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| соединители | Да | Да |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hubs | Да | Да |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| jobs | Нет | Нет |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Нет | Нет |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | Нет | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| catalogs | Да | Да |
| каталоги | Нет | Нет |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| connectionmanagers | Нет | Нет |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Пакеты | Нет | Нет |
| Планы | Нет | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datafactories | Да | Да |
| factories | Да | Да |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Нет | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Да | Да |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Да | Да |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services; | Нет | Нет |
| services/projects | Нет | Нет |
| slots | Нет | Нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | Да | Да |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | Да | Да |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servergroups | Нет | Нет |
| servers | Да | Да |
| serversv2 | Да | Да |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| artifactsources | Да | Да |
| rollouts | Да | Да |
| servicetopologies | Да | Да |
| servicetopologies/services | Да | Да |
| servicetopologies/services/serviceunits | Да | Да |
| steps | Да | Да |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| elasticpools | Нет | Нет |
| elasticpools/iothubtenants | Нет | Нет |
| iothubs | Да | Да |
| provisioningservices | Да | Да |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| controllers | Нет | Нет |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labcenters | Нет | Нет |
| labs | Да | Нет |
| лаборатории и среды | Да | Да |
| labs/servicerunners | Да | Да |
| labs/virtualmachines | Да | Нет |
| schedules | Да | Да |

## <a name="microsoftdns"></a>microsoft.dns
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dnszones | Нет | Нет |
| dnszones/a | Нет | Нет |
| dnszones/aaaa | Нет | Нет |
| dnszones/cname | Нет | Нет |
| dnszones/mx | Нет | Нет |
| dnszones/ptr | Нет | Нет |
| dnszones/srv | Нет | Нет |
| dnszones/txt | Нет | Нет |
| trafficmanagerprofiles | Нет | Нет |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databaseaccounts | Да | Да |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domains | Да | Да |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services; | Да | Да |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domains | Да | Да |
| topics | Да | Да |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Да | Да |
| namespaces | Да | Да |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Нет | Нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hanainstances | Да | Да |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Да | Да |

> [!IMPORTANT]
> Кластеры HDInsight можно переместить в новую подписку или группу ресурсов. Однако перемещение между подписками недоступно для сетевых ресурсов, связанных с кластером HDInsight (таких как виртуальная сеть, сетевой адаптер или подсистема балансировки нагрузки). Также невозможно переместить в новую группу ресурсов сетевой адаптер, который подключен к виртуальной машине кластера.
>
> При перемещении кластера HDInsight в новую подписку сначала необходимо переместить другие ресурсы (такие как учетная запись хранения). А затем переместить сам кластер HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services; | Да | Да |

## <a name="microsofthybridcompute"></a>Microsoft. Хибридкомпуте
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| виртуальных | Нет | Нет |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datamanagers | Да | Да |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| jobs | Да | Да |

## <a name="microsoftinsights"></a>microsoft.insights
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Нет | Нет |
| actiongroups | Да | Да |
| activitylogalerts | Нет | Нет |
| alertrules | Да | Да |
| autoscalesettings | Да | Да |
| components | Да | Да |
| guestdiagnosticsettings | Нет | Нет |
| metricalerts | Нет | Нет |
| notificationgroups | Нет | Нет |
| notificationrules | Нет | Нет |
| scheduledqueryrules | Да | Да |
| webtests | Да | Да |
| workbooks | Да | Да |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotapps | Да | Да |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| checknameavailability | Да | Да |
| graph | Да | Да |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hsmpools | Нет | Нет |
| vaults | Да | Да |

> [!IMPORTANT]
> Хранилища ключей, используемые для шифрования дисков, нельзя переместить в группу ресурсов в одной и той же подписке или в рамках подписок.

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Да | Да |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labaccounts | Нет | Нет |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Да | Да |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Нет | Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingenvironments | Нет | Нет |
| integrationaccounts | Да | Да |
| integrationserviceenvironments | Нет | Нет |
| isolatedenvironments | Нет | Нет |
| workflows | Да | Да |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| commitmentplans | Да | Да |
| webservices | Да | Нет |
| workspaces | Да | Да |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Да | Да |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Нет | Нет |
| accounts/workspaces | Нет | Нет |
| accounts/workspaces/projects | Нет | Нет |
| teamaccounts | Нет | Нет |
| teamaccounts/workspaces | Нет | Нет |
| teamaccounts/workspaces/projects | Нет | Нет |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Да | Да |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingaccounts | Нет | Нет |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | Нет | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| userassignedidentities | Нет | Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Да | Да |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| classicdevservices | Нет | Нет |

## <a name="microsoftmedia"></a>Microsoft.Media
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mediaservices | Да | Да |
| mediaservices/liveevents | Да | Да |
| mediaservices/streamingendpoints | Да | Да |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| assessmentprojects | Нет | Нет |
| migrateprojects | Нет | Нет |
| projects | Нет | Нет |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| netappaccounts | Нет | Нет |
| netappaccounts/capacitypools | Нет | Нет |
| netappaccounts/capacitypools/volumes | Нет | Нет |
| netappaccounts/capacitypools/volumes/mounttargets | Нет | Нет |
| netappaccounts/capacitypools/volumes/snapshots | Нет | Нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgateways | Нет | Нет |
| аппликатионгатевайвебаппликатионфиреваллполиЦиес | Нет | Нет |
| applicationsecuritygroups | Да | Да |
| azurefirewalls | Да | Да |
| bastionhosts | Нет | Нет |
| connections | Да | Да |
| ddoscustompolicies | Да | Да |
| ddosprotectionplans | Нет | Нет |
| dnszones | Да | Да |
| expressroutecircuits | Нет | Нет |
| expressroutecrossconnections | Нет | Нет |
| expressroutegateways | Нет | Нет |
| expressrouteports | Нет | Нет |
| frontdoors | Нет | Нет |
| frontdoorwebapplicationfirewallpolicies | Нет | Нет |
| loadbalancers | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br>SKU No-Standard |
| localnetworkgateways | Да | Да |
| natgateways | Да | Да |
| networkintentpolicies | Да | Да |
| networkinterfaces | Да | Да |
| networkprofiles | Нет | Нет |
| networksecuritygroups | Да | Да |
| networkwatchers | Да | Да |
| networkwatchers/connectionmonitors | Да | Да |
| networkwatchers/lenses | Да | Да |
| networkwatchers/pingmeshes | Да | Да |
| p2svpngateways | Нет | Нет |
| приватеднсзонес | Да | Да |
| приватеднсзонес/виртуалнетворклинкс | Да | Да |
| приватиндпоинтс | Нет | Нет |
| privatelinkservices | Нет | Нет |
| publicipaddresses | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br>SKU No-Standard |
| publicipprefixes | Да | Да |
| routefilters | Нет | Нет |
| routetables | Да | Да |
| securegateways | Да | Да |
| serviceendpointpolicies | Да | Да |
| trafficmanagerprofiles | Да | Да |
| virtualhubs | Нет | Нет |
| virtualnetworkgateways | Да | Да |
| virtualnetworks | Да | Да |
| virtualnetworktaps | Нет | Нет |
| virtualwans | Нет | Нет |
| vpngateways | Нет | Нет |
| vpnsites | Нет | Нет |
| webapplicationfirewallpolicies | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению виртуальных сетей](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Да | Да |
| namespaces/notificationhubs | Да | Да |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | Да | Да |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managementconfigurations | Да | Да |
| solutions | Да | Да |
| узел "Представления" | Да | Да |

## <a name="microsoftpeering"></a>Microsoft. пиринг
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| пиринги | Нет | Нет |

## <a name="microsoftportal"></a>Microsoft.Portal
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dashboards | Да | Да |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| rootresources | Нет | Нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspacecollections | Да | Да |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| capacities | Да | Да |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Нет | Нет |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| vaults | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению служб восстановления](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Да | Да |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| веб-масштабированием; | Да | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| flows | Да | Да |
| jobcollections | Да | Да |

## <a name="microsoftsearch"></a>Microsoft.Search
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| searchservices | Да | Да |

> [!IMPORTANT]
> В одной операции невозможно переместить несколько ресурсов поиска в разных регионах. Переместите их в отдельных операциях.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| иотсекуритисолутионс | Да | Да |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| gateways | Нет | Нет |
| Узлы | Нет | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Да | Да |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| веб-масштабированием; | Нет | Нет |
| clusters | Да | Да |
| containergroups | Нет | Нет |
| containergroupsets | Нет | Нет |
| edgeclusters | Нет | Нет |
| networks | Нет | Нет |
| secretstores | Нет | Нет |
| volumes. | Нет | Нет |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| веб-масштабированием; | Да | Да |
| containergroups | Нет | Нет |
| gateways | Да | Да |
| networks | Да | Да |
| секретные коды | Да | Да |
| volumes. | Да | Да |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| signalr | Да | Да |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Нет | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению служб восстановления](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Нет | Нет |
| appliances | Нет | Нет |
| applicationdefinitions | Нет | Нет |
| веб-масштабированием; | Нет | Нет |
| jitrequests | Нет | Нет |

## <a name="microsoftsql"></a>Microsoft.Sql
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| инстанцепулс | Нет | Нет |
| managedinstances | Нет | Нет |
| managedinstances/databases | Нет | Нет |
| servers | Да | Да |
| servers/databases | Да | Да |
| servers/elasticpools | Да | Да |
| virtualclusters | Да | Да |

> [!IMPORTANT]
> База данных и сервер должны находиться в одной группе ресурсов. При перемещении сервера SQL Server все его базы данных также перемещаются. Такое поведение также характерно для баз данных SQL Azure и баз данных хранилища данных SQL Azure.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Да | Да |
| sqlvirtualmachines | Да | Да |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dwvm | Нет | Нет |

## <a name="microsoftstorage"></a>Microsoft.Storage;
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Да | Да |

## <a name="microsoftstoragecache"></a>Microsoft. Сторажекаче
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| кэширует | Нет | Нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Да | Да |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Нет | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Нет | Нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managers | Нет | Нет |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| streamingjobs | Да | Да |

> [!IMPORTANT]
> Невозможно переместить задания Stream Analytics в состоянии выполнения.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| environments | Нет | Нет |
| environments/eventsources | Нет | Нет |
| instances | Нет | Нет |
| instances/environments | Нет | Нет |
| instances/environments/eventsources | Нет | Нет |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| providerregistrations | Нет | Нет |
| ресурсов | Нет | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| environments | Да | Да |
| environments/eventsources | Да | Да |
| environments/referencedatasets | Да | Да |

## <a name="microsofttoken"></a>Microsoft. Token
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| сохраняют | Нет | Нет |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| imagetemplates | Нет | Нет |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| учетная запись | Да | Да |
| account/extension | Да | Да |
| account/project | Да | Да |

> [!IMPORTANT]
> Чтобы изменить подписку на Azure DevOps, см. статью [изменение подписки Azure, используемой для выставления счетов](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| дедикатедклауднодес | Да | Да |
| дедикатедклаудсервицес | Да | Да |
| virtualmachines | Да | Да |

## <a name="microsoftweb"></a>Microsoft.Web
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| certificates | Нет | Да |
| connectiongateways | Да | Да |
| connections | Да | Да |
| customapis | Да | Да |
| hostingenvironments | Нет | Нет |
| serverfarms | Да | Да |
| sites | Да | Да |
| sites/premieraddons | Да | Да |
| sites/slots | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| deviceservices | Нет | Нет |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. Виндовсвиртуалдесктоп
| Тип ресурса | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| аппликатионграупс | Нет | Нет |
| хостпулс | Нет | Нет |
| workspaces | Нет | Нет |

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.

## <a name="next-steps"></a>Следующие шаги
Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
