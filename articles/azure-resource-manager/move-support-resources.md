---
title: Поддержка операции перемещения типом ресурсов Azure
description: Список типов ресурсов Azure, подлежащих перемещению в новую группу ресурсов или подписку.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 73f4b6fe4714d21c12d2c7bd387cd30f6f711d5a
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624325"
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
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| domainservices | Нет | Нет |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| tenants | Нет | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| актионрулес | Да | Да |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| серверы | Да | Да |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| служба | Да | Да |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| конфигуратионсторес | Да | Да |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| apiapps | Нет | Нет |
| appidentities | Нет | Нет |
| gateways | Нет | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| policyassignments | Нет | Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| automationaccounts | Да | Да |
| automationaccounts/configurations | Да | Да |
| automationaccounts/runbooks | Да | Да |

> [!IMPORTANT]
> Модули Runbook должны находиться в той же группе ресурсов, что и учетная запись службы автоматизации.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| b2cdirectories | Да | Да |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| registrations | Да | Да |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| backupvault | Нет | Нет |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| batchaccounts | Да | Да |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| clusters | Нет | Нет |
| fileservers | Нет | Нет |
| задания | Нет | Нет |
| рабочие области | Нет | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| mapapis | Нет | Нет |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| biztalk | Да | Да |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| blockchainmembers | Да | Да |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| blueprintassignments | Нет | Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| botservices | Да | Да |

## <a name="microsoftcache"></a>Microsoft.Cache
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| redis | Да | Да |

> [!IMPORTANT]
> Если для экземпляра кэша Azure для Redis настроена виртуальная сеть, экземпляр нельзя переместить в другую подписку. См. раздел [ограничения на перемещение виртуальных сетей](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| profiles | Да | Да |
| profiles/endpoints | Да | Да |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| certificateorders | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| domainnames | Да | Нет |
| virtualmachines | Да | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Нет | Нет |
| reservedips | Нет | Нет |
| virtualnetworks | Нет | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storageaccounts | Да | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Да | Да |

## <a name="microsoftcompute"></a>Microsoft.Compute;
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| availabilitysets | Да | Да |
| диски | Да | Да |
| galleries | Нет | Нет |
| galleries/images | Нет | Нет |
| galleries/images/versions | Нет | Нет |
| хостграупс | Нет | Нет |
| хостграупс и узлы | Нет | Нет |
| образы | Да | Да |
| proximityplacementgroups | Нет | Нет |
| restorepointcollections | Нет | Нет |
| sharedvmimages | Нет | Нет |
| sharedvmimages/versions | Нет | Нет |
| моментальные снимки | Да | Да |
| virtualmachines | Да | Да |
| virtualmachines/extensions | Да | Да |
| virtualmachinescalesets | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению виртуальных машин](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| containergroups | Нет | Нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| containergroups | Нет | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| registries | Да | Да |
| registries/buildtasks | Да | Да |
| registries/replications | Да | Да |
| registries/tasks | Да | Да |
| registries/webhooks | Да | Да |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| containerservices | Нет | Нет |
| managedclusters | Нет | Нет |
| openshiftmanagedclusters | Нет | Нет |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| приложения | Да | Да |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Нет | Нет |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| соединители | Да | Да |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hubs | Да | Да |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| задания | Нет | Нет |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Нет | Нет |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| рабочие области | Нет | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| catalogs | Да | Да |
| каталоги | Нет | Нет |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| connectionmanagers | Нет | Нет |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| Пакеты | Нет | Нет |
| Планы | Нет | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| datafactories | Да | Да |
| factories | Да | Да |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Нет | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Да | Да |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Да | Да |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| services; | Нет | Нет |
| services/projects | Нет | Нет |
| slots | Нет | Нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| серверы | Да | Да |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| серверы | Да | Да |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| servergroups | Нет | Нет |
| серверы | Да | Да |
| serversv2 | Да | Да |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| artifactsources | Да | Да |
| rollouts | Да | Да |
| servicetopologies | Да | Да |
| servicetopologies/services | Да | Да |
| servicetopologies/services/serviceunits | Да | Да |
| steps | Да | Да |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| elasticpools | Нет | Нет |
| elasticpools/iothubtenants | Нет | Нет |
| iothubs | Да | Да |
| provisioningservices | Да | Да |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| controllers | Нет | Нет |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| labcenters | Нет | Нет |
| labs | Да | Нет |
| лаборатории и среды | Да | Да |
| labs/servicerunners | Да | Да |
| labs/virtualmachines | Да | Нет |
| schedules | Да | Да |

## <a name="microsoftdns"></a>microsoft.dns
| Тип ресурса | Группа ресурсов | Подписка |
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
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| databaseaccounts | Да | Да |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| domains | Да | Да |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| services; | Да | Да |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| domains | Да | Да |
| topics | Да | Да |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| clusters | Да | Да |
| пространства имен | Да | Да |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Нет | Нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hanainstances | Да | Да |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| clusters | Да | Да |

> [!IMPORTANT]
> Кластеры HDInsight можно переместить в новую подписку или группу ресурсов. Однако перемещение между подписками недоступно для сетевых ресурсов, связанных с кластером HDInsight (таких как виртуальная сеть, сетевой адаптер или подсистема балансировки нагрузки). Также невозможно переместить в новую группу ресурсов сетевой адаптер, который подключен к виртуальной машине кластера.
>
> При перемещении кластера HDInsight в новую подписку сначала необходимо переместить другие ресурсы (такие как учетная запись хранения). А затем переместить сам кластер HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| services; | Да | Да |

## <a name="microsofthybridcompute"></a>Microsoft. Хибридкомпуте
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| Компьютеры | Нет | Нет |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| datamanagers | Да | Да |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| задания | Да | Да |

## <a name="microsoftinsights"></a>microsoft.insights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Нет | Нет |
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
| книги | Да | Да |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| iotapps | Да | Да |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| checknameavailability | Да | Да |
| Граф | Да | Да |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hsmpools | Нет | Нет |
| vaults | Да | Да |

> [!IMPORTANT]
> Хранилища ключей, используемые для шифрования дисков, нельзя переместить в группу ресурсов в одной и той же подписке или в рамках подписок.

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| clusters | Да | Да |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| labaccounts | Нет | Нет |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Да | Да |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Нет | Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hostingenvironments | Нет | Нет |
| integrationaccounts | Да | Да |
| integrationserviceenvironments | Нет | Нет |
| isolatedenvironments | Нет | Нет |
| workflows | Да | Да |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| commitmentplans | Да | Да |
| webservices | Да | Нет |
| рабочие области | Да | Да |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Да | Да |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Нет | Нет |
| accounts/workspaces | Нет | Нет |
| accounts/workspaces/projects | Нет | Нет |
| teamaccounts | Нет | Нет |
| teamaccounts/workspaces | Нет | Нет |
| teamaccounts/workspaces/projects | Нет | Нет |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Да | Да |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hostingaccounts | Нет | Нет |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| рабочие области | Нет | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| userassignedidentities | Нет | Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Да | Да |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| classicdevservices | Нет | Нет |

## <a name="microsoftmedia"></a>Microsoft.Media
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| mediaservices | Да | Да |
| mediaservices/liveevents | Да | Да |
| mediaservices/streamingendpoints | Да | Да |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| assessmentprojects | Нет | Нет |
| migrateprojects | Нет | Нет |
| projects | Нет | Нет |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| netappaccounts | Нет | Нет |
| netappaccounts/capacitypools | Нет | Нет |
| netappaccounts/capacitypools/volumes | Нет | Нет |
| netappaccounts/capacitypools/volumes/mounttargets | Нет | Нет |
| netappaccounts/capacitypools/volumes/snapshots | Нет | Нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| applicationgateways | Нет | Нет |
| аппликатионгатевайвебаппликатионфиреваллполиЦиес | Нет | Нет |
| applicationsecuritygroups | Да | Да |
| azurefirewalls | Да | Да |
| bastionhosts | Нет | Нет |
| подключения | Да | Да |
| ddoscustompolicies | Да | Да |
| ddosprotectionplans | Нет | Нет |
| dnszones | Да | Да |
| expressroutecircuits | Нет | Нет |
| expressroutecrossconnections | Нет | Нет |
| expressroutegateways | Нет | Нет |
| expressrouteports | Нет | Нет |
| frontdoor | Нет | Нет |
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
> См. [руководство по перемещению в сеть](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| пространства имен | Да | Да |
| namespaces/notificationhubs | Да | Да |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| рабочие области | Да | Да |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| managementconfigurations | Да | Да |
| решения | Да | Да |
| просмотры | Да | Да |

## <a name="microsoftpeering"></a>Microsoft. пиринг
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| пиринги | Нет | Нет |

## <a name="microsoftportal"></a>Microsoft.Portal
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| dashboards | Да | Да |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| rootresources | Нет | Нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| workspacecollections | Да | Да |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| capacities | Да | Да |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Нет | Нет |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| vaults | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению служб восстановления](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| пространства имен | Да | Да |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| приложения | Да | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| Потоки | Да | Да |
| jobcollections | Да | Да |

## <a name="microsoftsearch"></a>Microsoft.Search
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| searchservices | Да | Да |

> [!IMPORTANT]
> В одной операции невозможно переместить несколько ресурсов поиска в разных регионах. Переместите их в отдельных операциях.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| иотсекуритисолутионс | Да | Да |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| gateways | Нет | Нет |
| Узлы | Нет | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| пространства имен | Да | Да |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| приложения | Нет | Нет |
| clusters | Да | Да |
| containergroups | Нет | Нет |
| containergroupsets | Нет | Нет |
| edgeclusters | Нет | Нет |
| Сети | Нет | Нет |
| secretstores | Нет | Нет |
| тома | Нет | Нет |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| приложения | Да | Да |
| containergroups | Нет | Нет |
| gateways | Да | Да |
| Сети | Да | Да |
| секреты | Да | Да |
| тома | Да | Да |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| signalr | Да | Да |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Нет | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению служб восстановления](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Нет | Нет |
| appliances | Нет | Нет |
| applicationdefinitions | Нет | Нет |
| приложения | Нет | Нет |
| jitrequests | Нет | Нет |

## <a name="microsoftsql"></a>Microsoft.Sql
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| инстанцепулс | Нет | Нет |
| managedinstances | Нет | Нет |
| managedinstances/databases | Нет | Нет |
| серверы | Да | Да |
| servers/databases | Да | Да |
| servers/elasticpools | Да | Да |
| virtualclusters | Да | Да |

> [!IMPORTANT]
> База данных и сервер должны находиться в одной группе ресурсов. При перемещении сервера SQL Server все его базы данных также перемещаются. Такое поведение также характерно для баз данных SQL Azure и баз данных хранилища данных SQL Azure.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Да | Да |
| sqlvirtualmachines | Да | Да |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| dwvm | Нет | Нет |

## <a name="microsoftstorage"></a>Microsoft.Storage;
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storageaccounts | Да | Да |

## <a name="microsoftstoragecache"></a>Microsoft. Сторажекаче
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| кэширует | Нет | Нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storagesyncservices | Да | Да |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storagesyncservices | Нет | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storagesyncservices | Нет | Нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| managers | Нет | Нет |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| streamingjobs | Да | Да |

> [!IMPORTANT]
> Невозможно переместить задания Stream Analytics в состоянии выполнения.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| environments | Нет | Нет |
| environments/eventsources | Нет | Нет |
| Экземпляры | Нет | Нет |
| instances/environments | Нет | Нет |
| instances/environments/eventsources | Нет | Нет |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| providerregistrations | Нет | Нет |
| ресурсы | Нет | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| environments | Да | Да |
| environments/eventsources | Да | Да |
| environments/referencedatasets | Да | Да |

## <a name="microsofttoken"></a>Microsoft. Token
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| сохраняют | Нет | Нет |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| imagetemplates | Нет | Нет |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетная запись | Да | Да |
| account/extension | Да | Да |
| account/project | Да | Да |

> [!IMPORTANT]
> Чтобы изменить подписку на Azure DevOps, см. статью [изменение подписки Azure, используемой для выставления счетов](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| дедикатедклауднодес | Да | Да |
| дедикатедклаудсервицес | Да | Да |
| virtualmachines | Да | Да |

## <a name="microsoftweb"></a>Microsoft.Web
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| сертификаты | Нет | Да |
| connectiongateways | Да | Да |
| подключения | Да | Да |
| customapis | Да | Да |
| hostingenvironments | Нет | Нет |
| serverfarms | Да | Да |
| сайты | Да | Да |
| sites/premieraddons | Да | Да |
| sites/slots | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| deviceservices | Нет | Нет |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. Виндовсвиртуалдесктоп
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| аппликатионграупс | Нет | Нет |
| хостпулс | Нет | Нет |
| рабочие области | Нет | Нет |

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.

## <a name="next-steps"></a>Следующие шаги
Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
