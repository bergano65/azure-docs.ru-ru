---
title: Поддержка операций перемещения по типу ресурса
description: Список типов ресурсов Azure, подлежащих перемещению в новую группу ресурсов или подписку.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: da08775ed6c694b95ecec452507f94638091db0c
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261064"
---
# <a name="move-operation-support-for-resources"></a>Поддержка операции перемещения для ресурсов
В этой статье указано, поддерживается ли для тех или иных типов ресурсов Azure операция перемещения. В нем также содержатся сведения о специальных условиях, которые следует учитывать при перемещении ресурса.

Переход к пространству имен поставщика ресурсов:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft. аадиам](#microsoftaadiam)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. Аппплатформ](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. BatchAI](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
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
> - [Microsoft.Consumption](#microsoftconsumption)
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
> - [Защита Microsoft.](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
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
> - [Microsoft. Kubernetes](#microsoftkubernetes)
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
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. Обжектсторе](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights).
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft. Порталсдк](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. Прожектбабилон](#microsoftprojectbabylon)
> - [Microsoft. Прожектоксфорд](#microsoftprojectoxford)
> - [Microsoft. Провидерхуб](#microsoftproviderhub)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. Серверманажемент](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Стреаманалитиксексплорер](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft. Терраформосс](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. Token](#microsofttoken)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. Всонлине](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | Нет | Нет |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | Нет | Нет |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | конфигурации | Нет | Нет |
> | к просмотру фильмов | Нет | Нет |
> | suppressions | Нет | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | актионрулес | Да | Да |
> | оповещения | Нет | Нет |
> | алертссуммари | Нет | Нет |
> | смартдетекторалертрулес | Да | Да |

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

## <a name="microsoftappplatform"></a>Microsoft. Аппплатформ

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | Весна | Да | Да |

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
> | CheckAccess | Нет | Нет |
> | деняссигнментс | Нет | Нет |
> | финдорфанролеассигнментс | Нет | Нет |
> | locks | Нет | Нет |
> | разрешения | Нет | Нет |
> | policyassignments | Нет | Нет |
> | PolicyDefinitions | Нет | Нет |
> | полицисетдефинитионс | Нет | Нет |
> | roleassignments | Нет | Нет |
> | ролеассигнментсусажеметрикс | Нет | Нет |
> | roledefinitions | Нет | Нет |

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
> | хибриддатаманажерс | Нет | Нет |
> | постгресинстанцес | Нет | Нет |
> | склбигдатаклустерс | Нет | Нет |
> | склинстанцес | Нет | Нет |
> | склсерверрегистратионс | Да | Да |

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

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | биллингпериодс | Нет | Нет |
> | биллингпермиссионс | Нет | Нет |
> | биллингролеассигнментс | Нет | Нет |
> | биллингроледефинитионс | Нет | Нет |
> | креатебиллингролеассигнмент | Нет | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | Нет | Нет |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | Biztalk | Нет | Нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Нет | Нет |
> | наблюдатели | Нет | Нет |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Нет | Нет |
> | blueprints | Нет | Нет |

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
> | кднвебаппликатионфиреваллполиЦиес | Да | Да |
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

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | аггрегатедкост | Нет | Нет |
> | распределяет | Нет | Нет |
> | увеличен | Нет | Нет |
> | расходы | Нет | Нет |
> | косттагс | Нет | Нет |
> | credits | Нет | Нет |
> | события | Нет | Нет |
> | прогнозы | Нет | Нет |
> | lots | Нет | Нет |
> | магазинах | Нет | Нет |
> | оператионресултс | Нет | Нет |
> | значение operationstatus | Нет | Нет |
> | прицешитс | Нет | Нет |
> | products | Нет | Нет |
> | ресерватиондетаилс | Нет | Нет |
> | ресерватионрекоммендатионс | Нет | Нет |
> | ресерватионсуммариес | Нет | Нет |
> | ресерватионтрансактионс | Нет | Нет |
> | tags | Нет | Нет |
> | tenants | Нет | Нет |
> | terms | Нет | Нет |
> | usagedetails | Нет | Нет |

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
> | сервицеассоЦиатионлинкс | Нет | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | Да | Да |
> | реестры и буилдтаскс | Да | Да |
> | реестры и репликации | Да | Да |
> | реестры и таскрунс | Да | Да |
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
> | приложений | Нет | Нет |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | оповещения | Нет | Нет |
> | увеличен | Нет | Нет |
> | соединители | Да | Да |
> | измерения | Нет | Нет |
> | экспорты | Нет | Нет |
> | екстерналсубскриптионс | Нет | Нет |
> | forecast | Нет | Нет |
> | query | Нет | Нет |
> | репортконфигс | Нет | Нет |
> | reports | Нет | Нет |
> | шовбаккрулес | Нет | Нет |
> | узел "Представления" | Нет | Нет |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | Нет | Нет |

## <a name="microsoftcustomproviders"></a>Microsoft. Кустомпровидерс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | взаимосвязи | Нет | Нет |
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

## <a name="microsoftdataprotection"></a>Защита Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | баккупваултс | Нет | Нет |

## <a name="microsoftdatashare"></a>Общая папка Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |

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

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | конвейеров | Да | Да |

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
> | EventSubscriptions | Нет — нельзя перемещать независимо, но автоматически перемещать вместе с подписанным ресурсом. | Нет — нельзя перемещать независимо, но автоматически перемещать вместе с подписанным ресурсом. |
> | екстенсионтопикс | Нет | Нет |
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

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | гуестконфигуратионассигнментс | Нет | Нет |
> | software | Нет | Нет |
> | софтвареупдатепрофиле | Нет | Нет |
> | софтвареупдатес | Нет | Нет |

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
> | виртуальных | Да | Да |
> | Компьютеры и расширения | Нет | Нет |

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
> | actiongroups | Да | Да |
> | activitylogalerts | Нет | Нет |
> | alertrules | Да | Да |
> | аутоматедекспортсеттингс | Нет | Нет |
> | autoscalesettings | Да | Да |
> | baseline | Нет | Нет |
> | calculatebaseline | Нет | Нет |
> | components | Да | Да |
> | DiagnosticSettings | Нет | Нет |
> | диагностиксеттингскатегориес | Нет | Нет |
> | eventtypes | Нет | Нет |
> | екстендеддиагностиксеттингс | Нет | Нет |
> | логдефинитионс | Нет | Нет |
> | журналы | Нет | Нет |
> | metricalerts | Нет | Нет |
> | метрикбаселинес | Нет | Нет |
> | metricdefinitions | Нет | Нет |
> | метрикнамеспацес | Нет | Нет |
> | Метрики | Нет | Нет |
> | миворкбукс | Нет | Нет |
> | scheduledqueryrules | Да | Да |
> | Топология | Нет | Нет |
> | транзакций | Нет | Нет |
> | вминсигхтсонбоардингстатусес | Нет | Нет |
> | webtests | Да | Да |
> | workbooks | Да | Да |
> | воркбуктемплатес | Да | Да |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](azure-subscription-service-limits.md#azure-monitor-limits).

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
> | vaults | Да | Да |

> [!IMPORTANT]
> Хранилища ключей, используемые для шифрования дисков, нельзя переместить в группу ресурсов в одной и той же подписке или в рамках подписок.

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | коннектедклустерс | Нет | Нет |

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
> | integrationserviceenvironments | Да | Нет |
> | интегратионсервицеенвиронментс/манажедапис | Да | Нет |
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
> | operationalizationclusters | Нет | Нет |

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
> | удостоверения; | Нет | Нет |
> | userassignedidentities | Нет | Нет |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | регистратионассигнментс | Нет | Нет |
> | регистратиондефинитионс | Нет | Нет |

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
> | assessmentprojects | Да | Да |
> | migrateprojects | Да | Да |
> | projects | Нет | Нет |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Нет | Нет |
> | нетаппаккаунтс/баккупполиЦиес | Нет | Нет |
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
> | expressroutegateways | Нет | Нет |
> | frontdoors | Нет | Нет |
> | frontdoorwebapplicationfirewallpolicies | Нет | Нет |
> | loadbalancers | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br>SKU No-Standard |
> | localnetworkgateways | Да | Да |
> | нетворкекспериментпрофилес | Да | Да |
> | networkintentpolicies | Да | Да |
> | networkinterfaces | Да | Да |
> | networkprofiles | Нет | Нет |
> | networksecuritygroups | Да | Да |
> | networkwatchers | Да | Да |
> | нетворкватчерс/коннектионмониторс | Да | Да |
> | нетворкватчерс/панель | Да | Да |
> | нетворкватчерс/lenses | Да | Да |
> | нетворкватчерс/пингмешес | Да | Да |
> | p2svpngateways | Нет | Нет |
> | приватеднсзонес | Да | Да |
> | приватеднсзонес/виртуалнетворклинкс | Да | Да |
> | приватиндпоинтредиректмапс | Нет | Нет |
> | приватиндпоинтс | Нет | Нет |
> | privatelinkservices | Нет | Нет |
> | publicipaddresses | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br>SKU No-Standard |
> | publicipprefixes | Да | Да |
> | routefilters | Нет | Нет |
> | routetables | Да | Да |
> | serviceendpointpolicies | Да | Да |
> | trafficmanagerprofiles | Да | Да |
> | virtualhubs | Нет | Нет |
> | virtualnetworkgateways | Да | Да |
> | virtualnetworks | Да | Да |
> | virtualnetworktaps | Нет | Нет |
> | виртуалраутерс | Да | Да |
> | virtualwans | Нет | Нет |
> | впнгатевайс (Виртуальная глобальная сеть) | Нет | Нет |
> | впнсерверконфигуратионс | Нет | Нет |
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

## <a name="microsoftobjectstore"></a>Microsoft. Обжектсторе

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | оснамеспацес | Да | Да |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | сторажеинсигхтконфигс | Нет | Нет |
> | workspaces | Да | Да |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | Нет | Нет |
> | managementconfigurations | Да | Да |
> | solutions | Да | Да |
> | узел "Представления" | Да | Да |

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | пиринги | Да | Да |
> | пирингсервицес | Нет | Нет |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | полициевентс | Нет | Нет |
> | полицистатес | Нет | Нет |
> | полицитраккедресаурцес | Нет | Нет |
> | remediations | Нет | Нет |

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

## <a name="microsoftprojectbabylon"></a>Microsoft. Прожектбабилон

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Нет | Нет |

## <a name="microsoftproviderhub"></a>Microsoft. Провидерхуб

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | Нет | Нет |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | баккуппротектедитемс | Нет | Нет |
> | репликатионелигибилитиресултс | Нет | Нет |
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

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | аваилабилитистатусес | Нет | Нет |
> | чилдаваилабилитистатусес | Нет | Нет |
> | чилдресаурцес | Нет | Нет |
> | события | Нет | Нет |
> | Уведомления | Нет | Нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | Нет | Нет |
> | ссылки | Нет | Нет |
> | tags | Нет | Нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | приложений | Да | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
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
> | адаптивенетворкхарденингс | Нет | Нет |
> | адванцедсреатпротектионсеттингс | Нет | Нет |
> | ассессментметадата | Нет | Нет |
> | оценки | Нет | Нет |
> | инструментах автоматизации Composer | Да | Да |
> | комплианцересултс | Нет | Нет |
> | требованиям | Нет | Нет |
> | датаколлектионажентс | Нет | Нет |
> | датаколлектионресултс | Нет | Нет |
> | девицесекуритиграупс | Нет | Нет |
> | информатионпротектионполиЦиес | Нет | Нет |
> | иотсекуритисолутионс | Да | Да |
> | сервервулнерабилитяссессментс | Нет | Нет |

## <a name="microsoftsecurityinsights"></a>Microsoft. Секуритинсигхтс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | Нет | Нет |
> | alertrules | Нет | Нет |
> | алертрулетемплатес | Нет | Нет |
> | закладки | Нет | Нет |
> | cases | Нет | Нет |
> | подключения к компонентам | Нет | Нет |
> | Сущности | Нет | Нет |
> | ентитикуериес | Нет | Нет |
> | оффицеконсентс | Нет | Нет |
> | Параметры | Нет | Нет |

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
> | gateways | Да | Да |
> | networks | Да | Да |
> | секретные коды | Да | Да |
> | volumes. | Да | Да |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | Нет | Нет |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | Да | Да |

## <a name="microsoftsoftwareplan"></a>Microsoft. Софтвареплан

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | хибридусебенефитс | Нет | Нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
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

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | креатесубскриптион | Нет | Нет |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | креатесуппорттиккет | Нет | Нет |
> | supporttickets | Нет | Нет |

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
> | stores | Да | Да |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | дедикатедклауднодес | Нет | Нет |
> | дедикатедклаудсервицес | Нет | Нет |
> | virtualmachines | Нет | Нет |

## <a name="microsoftvsonline"></a>Microsoft. Всонлине

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |
> | Планы | Да | Да |

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
> | статикситес | Нет | Нет |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | Нет | Нет |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | components | Нет | Нет |
> | мониторинстанцес | Нет | Нет |
> | monitors | Нет | Нет |
> | нотификатионсеттингс | Нет | Нет |

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.

## <a name="next-steps"></a>Дальнейшие действия
Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](move-resource-group-and-subscription.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
