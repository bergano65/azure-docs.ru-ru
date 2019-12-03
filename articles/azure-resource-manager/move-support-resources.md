---
title: Поддержка операций перемещения по типу ресурса
description: Список типов ресурсов Azure, подлежащих перемещению в новую группу ресурсов или подписку.
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: b4a6f448807dd82b2c81cc207070d30a823151c7
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707018"
---
# <a name="move-operation-support-for-resources"></a>Поддержка операции перемещения для ресурсов
В этой статье указано, поддерживается ли для тех или иных типов ресурсов Azure операция перемещения. В нем также содержатся сведения о специальных условиях, которые следует учитывать при перемещении ресурса.

Переход к пространству имен поставщика ресурсов:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. аадиам](#microsoftaadiam)
> - [Microsoft. Алертсманажемент](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft. Аппконфигуратион](#microsoftappconfiguration)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. Азуредата](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
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
> - [Microsoft. Кустомпровидерс](#microsoftcustomproviders)
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
> - [Общая папка Microsoft.](#microsoftdatashare)
> - [Microsoft. Дбформариадб](#microsoftdbformariadb)
> - [Microsoft. Дбформискл](#microsoftdbformysql)
> - [Microsoft. Дбфорпостгрескл](#microsoftdbforpostgresql)
> - [Microsoft. Деплойментманажер](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. Девспацес](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. Домаинрегистратион](#microsoftdomainregistration)
> - [Microsoft. Ентерприсекновледжеграф](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. Genomics](#microsoftgenomics)
> - [Microsoft. Ханаоназуре](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft. Хеалскареапис](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
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
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
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
> - [Microsoft. Ресаурцеграф](#microsoftresourcegraph)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. Серверманажемент](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft. Сервицефабрикмеш](#microsoftservicefabricmesh)
> - [Microsoft. Сигналрсервице](#microsoftsignalrservice)
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

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | Нет | Нет |
> | DomainServices/репликасетс | Нет | Нет |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | Нет | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | актионрулес | ДА | ДА |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | серверы | ДА | ДА |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | Служба | ДА | ДА |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | конфигуратионсторес | ДА | ДА |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | Нет | Нет |
> | appidentities | Нет | Нет |
> | gateways | Нет | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | policyassignments | Нет | Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | ДА | ДА |
> | automationaccounts и конфигурации | ДА | ДА |
> | automationaccounts и модули Runbook | ДА | ДА |

> [!IMPORTANT]
> Модули Runbook должны находиться в той же группе ресурсов, что и учетная запись службы автоматизации.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | ДА | ДА |

## <a name="microsoftazuredata"></a>Microsoft. Азуредата

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | склсерверрегистратионс | Нет | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | registrations | ДА | ДА |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | ДА | ДА |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Нет | Нет |
> | fileservers | Нет | Нет |
> | jobs | Нет | Нет |
> | workspaces | Нет | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | Нет | Нет |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | Biztalk | ДА | ДА |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | ДА | ДА |
> | наблюдатели | Нет | Нет |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Нет | Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | ДА | ДА |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | ДА | ДА |

> [!IMPORTANT]
> Если для экземпляра кэша Azure для Redis настроена виртуальная сеть, экземпляр нельзя переместить в другую подписку. См. раздел [ограничения на перемещение в сети](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | кднвебаппликатионфиреваллполиЦиес | Нет | Нет |
> | профили | ДА | ДА |
> | профили и конечные точки | ДА | ДА |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | ДА | ДА |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | ДА | Нет |
> | virtualmachines | ДА | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Нет | Нет |
> | reservedips | Нет | Нет |
> | virtualnetworks | Нет | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | ДА | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | ДА | ДА |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | ДА | ДА |
> | дискенкриптионсетс | Нет | Нет |
> | disks | ДА | ДА |
> | galleries | Нет | Нет |
> | коллекции и изображения | Нет | Нет |
> | коллекции, изображения и версии | Нет | Нет |
> | хостграупс | Нет | Нет |
> | хостграупс и узлы | Нет | Нет |
> | images | ДА | ДА |
> | proximityplacementgroups | Нет | Нет |
> | restorepointcollections | Нет | Нет |
> | sharedvmimages | Нет | Нет |
> | шаредвмимажес и версии | Нет | Нет |
> | snapshots | ДА | ДА |
> | virtualmachines | ДА | ДА |
> | virtualmachines и расширения | ДА | ДА |
> | virtualmachinescalesets | ДА | ДА |

> [!IMPORTANT]
> См. [руководство по перемещению виртуальных машин](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Нет | Нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Нет | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | ДА | ДА |
> | реестры и буилдтаскс | ДА | ДА |
> | реестры и репликации | ДА | ДА |
> | реестры и задачи | ДА | ДА |
> | реестры и веб-перехватчики | ДА | ДА |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | Нет | Нет |
> | managedclusters | Нет | Нет |
> | openshiftmanagedclusters | Нет | Нет |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | приложений | ДА | ДА |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | соединители | ДА | ДА |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | ДА | ДА |

## <a name="microsoftcustomproviders"></a>Microsoft. Кустомпровидерс

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | ресаурцепровидерс | ДА | ДА |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Нет | Нет |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Нет | Нет |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Нет | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | ДА | ДА |
> | каталоги | Нет | Нет |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Нет | Нет |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | Пакеты | Нет | Нет |
> | Планы | Нет | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | ДА | ДА |
> | factories | ДА | ДА |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Нет | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | ДА | ДА |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | ДА | ДА |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | services; | Нет | Нет |
> | службы и проекты | Нет | Нет |
> | slots | Нет | Нет |

## <a name="microsoftdatashare"></a>Общая папка Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | серверы | ДА | ДА |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | серверы | ДА | ДА |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | Нет | Нет |
> | серверы | ДА | ДА |
> | serversv2 | ДА | ДА |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | ДА | ДА |
> | rollouts | ДА | ДА |
> | servicetopologies | ДА | ДА |
> | сервицетопологиес и службы | ДА | ДА |
> | сервицетопологиес/Services/сервицеунитс | ДА | ДА |
> | steps | ДА | ДА |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | Нет | Нет |
> | еластикпулс/иосубтенантс | Нет | Нет |
> | iothubs | ДА | ДА |
> | provisioningservices | ДА | ДА |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | ДА | ДА |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | Нет | Нет |
> | labs | ДА | Нет |
> | лаборатории и среды | ДА | ДА |
> | Labs и сервицеруннерс | ДА | ДА |
> | Labs и virtualmachines | ДА | Нет |
> | schedules | ДА | ДА |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | ДА | ДА |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | ДА | ДА |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | services; | ДА | ДА |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | ДА | ДА |
> | topics | ДА | ДА |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | ДА | ДА |
> | namespaces | ДА | ДА |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | Нет | Нет |
> | сапмониторс | ДА | ДА |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | ДА | ДА |

> [!IMPORTANT]
> Кластеры HDInsight можно переместить в новую подписку или группу ресурсов. Однако перемещение между подписками недоступно для сетевых ресурсов, связанных с кластером HDInsight (таких как виртуальная сеть, сетевой адаптер или подсистема балансировки нагрузки). Также невозможно переместить в новую группу ресурсов сетевой адаптер, который подключен к виртуальной машине кластера.
>
> При перемещении кластера HDInsight в новую подписку сначала необходимо переместить другие ресурсы (такие как учетная запись хранения). А затем переместить сам кластер HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | services; | ДА | ДА |

## <a name="microsofthybridcompute"></a>Microsoft. Хибридкомпуте

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | виртуальных | Нет | Нет |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | ДА | ДА |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | ДА | ДА |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |
> | actiongroups | ДА | ДА |
> | activitylogalerts | Нет | Нет |
> | alertrules | ДА | ДА |
> | autoscalesettings | ДА | ДА |
> | components | ДА | ДА |
> | guestdiagnosticsettings | Нет | Нет |
> | metricalerts | Нет | Нет |
> | notificationgroups | Нет | Нет |
> | notificationrules | Нет | Нет |
> | scheduledqueryrules | ДА | ДА |
> | webtests | ДА | ДА |
> | workbooks | ДА | ДА |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | ДА | ДА |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | ДА | ДА |
> | graph | ДА | ДА |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | hsmpools | Нет | Нет |
> | vaults | ДА | ДА |

> [!IMPORTANT]
> Хранилища ключей, используемые для шифрования дисков, нельзя переместить в группу ресурсов в одной и той же подписке или в рамках подписок.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | ДА | ДА |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | Нет | Нет |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Нет | Нет |
> | integrationaccounts | ДА | ДА |
> | integrationserviceenvironments | Нет | Нет |
> | isolatedenvironments | Нет | Нет |
> | workflows | ДА | ДА |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | ДА | ДА |
> | webservices | ДА | Нет |
> | workspaces | ДА | ДА |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | ДА | ДА |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |
> | учетные записи и рабочие области | Нет | Нет |
> | учетные записи, рабочие области и проекты | Нет | Нет |
> | teamaccounts | Нет | Нет |
> | теамаккаунтс и рабочие области | Нет | Нет |
> | теамаккаунтс/рабочие области и проекты | Нет | Нет |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Нет | Нет |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Нет | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | userassignedidentities | Нет | Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | ДА | ДА |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Нет | Нет |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | ДА | ДА |
> | mediaservices/лививентс | ДА | ДА |
> | mediaservices/streamingendpoint | ДА | ДА |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | аппклустерс | Нет | Нет |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Нет | Нет |
> | migrateprojects | Нет | Нет |
> | projects | Нет | Нет |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Нет | Нет |
> | нетаппаккаунтс/капаЦитипулс | Нет | Нет |
> | нетаппаккаунтс/капаЦитипулс/тома | Нет | Нет |
> | нетаппаккаунтс/капаЦитипулс/Volumes/маунттаржетс | Нет | Нет |
> | нетаппаккаунтс/капаЦитипулс/тома/моментальные снимки | Нет | Нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Нет | Нет |
> | аппликатионгатевайвебаппликатионфиреваллполиЦиес | Нет | Нет |
> | applicationsecuritygroups | ДА | ДА |
> | azurefirewalls | ДА | ДА |
> | bastionhosts | Нет | Нет |
> | connections | ДА | ДА |
> | ddoscustompolicies | ДА | ДА |
> | ddosprotectionplans | Нет | Нет |
> | dnszones | ДА | ДА |
> | expressroutecircuits | Нет | Нет |
> | expressroutecrossconnections | Нет | Нет |
> | expressroutegateways | Нет | Нет |
> | expressrouteports | Нет | Нет |
> | frontdoors | Нет | Нет |
> | frontdoorwebapplicationfirewallpolicies | Нет | Нет |
> | loadbalancers | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br>SKU No-Standard |
> | localnetworkgateways | ДА | ДА |
> | natgateways | ДА | ДА |
> | networkintentpolicies | ДА | ДА |
> | networkinterfaces | ДА | ДА |
> | networkprofiles | Нет | Нет |
> | networksecuritygroups | ДА | ДА |
> | networkwatchers | ДА | ДА |
> | нетворкватчерс/коннектионмониторс | ДА | ДА |
> | нетворкватчерс/lenses | ДА | ДА |
> | нетворкватчерс/пингмешес | ДА | ДА |
> | p2svpngateways | Нет | Нет |
> | приватеднсзонес | ДА | ДА |
> | приватеднсзонес/виртуалнетворклинкс | ДА | ДА |
> | приватиндпоинтс | Нет | Нет |
> | privatelinkservices | Нет | Нет |
> | publicipaddresses | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br>SKU No-Standard |
> | publicipprefixes | ДА | ДА |
> | routefilters | Нет | Нет |
> | routetables | ДА | ДА |
> | serviceendpointpolicies | ДА | ДА |
> | trafficmanagerprofiles | ДА | ДА |
> | virtualhubs | Нет | Нет |
> | virtualnetworkgateways | ДА | ДА |
> | virtualnetworks | ДА | ДА |
> | virtualnetworktaps | Нет | Нет |
> | virtualwans | Нет | Нет |
> | впнгатевайс (Виртуальная глобальная сеть) | Нет | Нет |
> | впнситес (Виртуальная глобальная сеть) | Нет | Нет |
> | webapplicationfirewallpolicies | ДА | ДА |

> [!IMPORTANT]
> См. [руководство по перемещению в сеть](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | ДА | ДА |
> | пространства имен/notificationhubs | ДА | ДА |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | ДА | ДА |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | ДА | ДА |
> | solutions | ДА | ДА |
> | узел "Представления" | ДА | ДА |

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | пиринги | Нет | Нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | dashboards | ДА | ДА |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | Нет | Нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | ДА | ДА |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | ДА | ДА |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | vaults | ДА | ДА |

> [!IMPORTANT]
> См. [руководство по перемещению служб восстановления](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | ДА | ДА |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | запросы | ДА | ДА |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | приложений | ДА | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | flows | ДА | ДА |
> | jobcollections | ДА | ДА |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | ДА | ДА |

> [!IMPORTANT]
> В одной операции невозможно переместить несколько ресурсов поиска в разных регионах. Переместите их в отдельных операциях.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | иотсекуритисолутионс | ДА | ДА |
> | плайбукконфигуратионс | Нет | Нет |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | Нет | Нет |
> | Узлы | Нет | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | ДА | ДА |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | приложений | Нет | Нет |
> | clusters | ДА | ДА |
> | кластеры и приложения | Нет | Нет |
> | containergroups | Нет | Нет |
> | containergroupsets | Нет | Нет |
> | edgeclusters | Нет | Нет |
> | networks | Нет | Нет |
> | secretstores | Нет | Нет |
> | volumes. | Нет | Нет |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | приложений | ДА | ДА |
> | containergroups | Нет | Нет |
> | gateways | ДА | ДА |
> | networks | ДА | ДА |
> | секретные коды | ДА | ДА |
> | volumes. | ДА | ДА |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | ДА | ДА |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | Нет | Нет |
> | appliances | Нет | Нет |
> | applicationdefinitions | Нет | Нет |
> | приложений | Нет | Нет |
> | jitrequests | Нет | Нет |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | инстанцепулс | Нет | Нет |
> | managedinstances | Нет | Нет |
> | манажединстанцес и базы данных | Нет | Нет |
> | серверы | ДА | ДА |
> | серверы и базы данных | ДА | ДА |
> | серверы и еластикпулс | ДА | ДА |
> | virtualclusters | ДА | ДА |

> [!IMPORTANT]
> База данных и сервер должны находиться в одной группе ресурсов. При перемещении сервера SQL Server все его базы данных также перемещаются. Такое поведение также характерно для баз данных SQL Azure и баз данных хранилища данных SQL Azure.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | ДА | ДА |
> | sqlvirtualmachines | ДА | ДА |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | Нет | Нет |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | ДА | ДА |

## <a name="microsoftstoragecache"></a>Microsoft. Сторажекаче

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | кэширует | Нет | Нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | ДА | ДА |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Нет | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Нет | Нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | Нет | Нет |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | ДА | ДА |

> [!IMPORTANT]
> Невозможно переместить задания Stream Analytics в состоянии выполнения.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Нет | Нет |
> | среды и классов EventSource | Нет | Нет |
> | Экземпляры | Нет | Нет |
> | экземпляры и среды | Нет | Нет |
> | экземпляры/среды/классов EventSource | Нет | Нет |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Нет | Нет |
> | ресурсов | Нет | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | ДА | ДА |
> | среды и классов EventSource | ДА | ДА |
> | среды и референцедатасетс | ДА | ДА |

## <a name="microsofttoken"></a>Microsoft. Token

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | сохраняют | Нет | Нет |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Нет | Нет |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | учетная запись | Нет | Нет |
> | Учетная запись или расширение | Нет | Нет |
> | Учетная запись или проект | Нет | Нет |

> [!IMPORTANT]
> Чтобы изменить подписку на Azure DevOps, см. статью [изменение подписки Azure, используемой для выставления счетов](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | дедикатедклауднодес | Нет | Нет |
> | дедикатедклаудсервицес | Нет | Нет |
> | virtualmachines | Нет | Нет |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | certificates | Нет | ДА |
> | connectiongateways | ДА | ДА |
> | connections | ДА | ДА |
> | customapis | ДА | ДА |
> | hostingenvironments | Нет | Нет |
> | serverfarms | ДА | ДА |
> | sites | ДА | ДА |
> | Sites/премиераддонс | ДА | ДА |
> | сайты и слоты | ДА | ДА |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | Нет | Нет |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. Виндовсвиртуалдесктоп

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | аппликатионграупс | Нет | Нет |
> | хостпулс | Нет | Нет |
> | workspaces | Нет | Нет |

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.

## <a name="next-steps"></a>Дальнейшие действия
Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
