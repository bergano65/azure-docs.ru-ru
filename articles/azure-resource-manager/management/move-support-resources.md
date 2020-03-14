---
title: Поддержка операций перемещения по типу ресурса
description: Список типов ресурсов Azure, подлежащих перемещению в новую группу ресурсов или подписку.
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 8ab194ad240e4f3e0994314ef9ade3bc7159cf81
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273934"
---
# <a name="move-operation-support-for-resources"></a>Поддержка операции перемещения для ресурсов
В этой статье указано, поддерживается ли для тех или иных типов ресурсов Azure операция перемещения. В нем также содержатся сведения о специальных условиях, которые следует учитывать при перемещении ресурса.

Переход к пространству имен поставщика ресурсов:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. аадиам](#microsoftaadiam)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. Алертсманажемент](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft. Аппконфигуратион](#microsoftappconfiguration)
> - [Microsoft. Аппплатформ](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. Азуредата](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. Batch](#microsoftbatch)
> - [Microsoft. BatchAI](#microsoftbatchai)
> - [Microsoft. выставление счетов](#microsoftbilling)
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
> - [Microsoft. потребление](#microsoftconsumption)
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
> - [Защита Microsoft.](#microsoftdataprotection)
> - [Общая папка Microsoft.](#microsoftdatashare)
> - [Microsoft. Дбформариадб](#microsoftdbformariadb)
> - [Microsoft. Дбформискл](#microsoftdbformysql)
> - [Microsoft. Дбфорпостгрескл](#microsoftdbforpostgresql)
> - [Microsoft. Деплойментманажер](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. Девспацес](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft. Дигиталтвинс](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. Домаинрегистратион](#microsoftdomainregistration)
> - [Microsoft. Ентерприсекновледжеграф](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
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
> - [Microsoft. Kubernetes](#microsoftkubernetes)
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
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Маркетплацеаппс](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. migrate](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. Обжектсторе](#microsoftobjectstore)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. пиринг](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights).
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. Порталсдк](#microsoftportalsdk)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. Повербидедикатед](#microsoftpowerbidedicated)
> - [Microsoft. Прожектбабилон](#microsoftprojectbabylon)
> - [Microsoft. Прожектоксфорд](#microsoftprojectoxford)
> - [Microsoft. Провидерхуб](#microsoftproviderhub)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. Ресаурцеграф](#microsoftresourcegraph)
> - [Microsoft. Ресаурцехеалс](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. Секуритинсигхтс](#microsoftsecurityinsights)
> - [Microsoft. Серверманажемент](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft. Сервицефабрикмеш](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. Сигналрсервице](#microsoftsignalrservice)
> - [Microsoft. Софтвареплан](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft. Склвиртуалмачине](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. Сторажесинкдев](#microsoftstoragesyncdev)
> - [Microsoft. СторажесинЦинт](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Стреаманалитиксексплорер](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft. Терраформосс](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. Token](#microsofttoken)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. Вмвареклаудсимпле](#microsoftvmwarecloudsimple)
> - [Microsoft. Всонлине](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft. Виндовсиот](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | нет | нет |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | нет | нет |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | конфигурации | нет | нет |
> | рекомендации | нет | нет |
> | suppressions | нет | нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | актионрулес | Да | Да |
> | оповещения | нет | нет |
> | алертссуммари | нет | нет |
> | смартдетекторалертрулес | Да | Да |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Да | Да |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | служба | Да | Да |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | конфигуратионсторес | Да | Да |

## <a name="microsoftappplatform"></a>Microsoft. Аппплатформ

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | Весна | Да | Да |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | нет | нет |
> | appidentities | нет | нет |
> | gateways | нет | нет |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | CheckAccess | нет | нет |
> | деняссигнментс | нет | нет |
> | финдорфанролеассигнментс | нет | нет |
> | locks | нет | нет |
> | разрешения | нет | нет |
> | policyassignments | нет | нет |
> | PolicyDefinitions | нет | нет |
> | полицисетдефинитионс | нет | нет |
> | roleassignments | нет | нет |
> | ролеассигнментсусажеметрикс | нет | нет |
> | roledefinitions | нет | нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Да | Да |
> | automationaccounts и конфигурации | Да | Да |
> | automationaccounts и модули Runbook | Да | Да |

> [!IMPORTANT]
> Модули Runbook должны находиться в той же группе ресурсов, что и учетная запись службы автоматизации.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Да | Да |

## <a name="microsoftazuredata"></a>Microsoft. Азуредата

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | хибриддатаманажерс | нет | нет |
> | постгресинстанцес | нет | нет |
> | склбигдатаклустерс | нет | нет |
> | склинстанцес | нет | нет |
> | склсерверрегистратионс | Да | Да |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | registrations | Да | Да |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Да | Да |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | нет | нет |
> | fileservers | нет | нет |
> | jobs | нет | нет |
> | workspaces | нет | нет |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | биллингпериодс | нет | нет |
> | биллингпермиссионс | нет | нет |
> | биллингролеассигнментс | нет | нет |
> | биллингроледефинитионс | нет | нет |
> | креатебиллингролеассигнмент | нет | нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | нет | нет |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | нет | нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | нет | нет |
> | наблюдателей | нет | нет |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | нет | нет |
> | blueprints | нет | нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | Да | Да |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | Да | Да |

> [!IMPORTANT]
> Если для экземпляра кэша Azure для Redis настроена виртуальная сеть, экземпляр нельзя переместить в другую подписку. См. раздел [ограничения на перемещение в сети](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | кднвебаппликатионфиреваллполиЦиес | Да | Да |
> | профили | Да | Да |
> | профили и конечные точки | Да | Да |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | Да | нет |
> | virtualmachines | Да | нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | нет | нет |
> | reservedips | нет | нет |
> | virtualnetworks | нет | нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Да | нет |

> [!IMPORTANT]
> См. [руководство по перемещению классической модели развертывания](./move-limitations/classic-model-move-limitations.md). Ресурсы классической модели развертывания можно перемещать между подписками с помощью операции, характерной для этого сценария.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Да | Да |
> | дискенкриптионсетс | нет | нет |
> | disks | Да | Да |
> | galleries | нет | нет |
> | коллекции и изображения | нет | нет |
> | коллекции, изображения и версии | нет | нет |
> | хостграупс | нет | нет |
> | хостграупс и узлы | нет | нет |
> | images | Да | Да |
> | proximityplacementgroups | нет | нет |
> | restorepointcollections | нет | нет |
> | sharedvmimages | нет | нет |
> | шаредвмимажес и версии | нет | нет |
> | снимки | Да | Да |
> | virtualmachines | Да | Да |
> | virtualmachines и расширения | Да | Да |
> | virtualmachinescalesets | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению виртуальных машин](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | аггрегатедкост | нет | нет |
> | распределяет | нет | нет |
> | увеличен | нет | нет |
> | держек | нет | нет |
> | косттагс | нет | нет |
> | credits | нет | нет |
> | события | нет | нет |
> | прогнозы | нет | нет |
> | lots | нет | нет |
> | магазинах | нет | нет |
> | оператионресултс | нет | нет |
> | значение operationstatus | нет | нет |
> | прицешитс | нет | нет |
> | products | нет | нет |
> | ресерватиондетаилс | нет | нет |
> | ресерватионрекоммендатионс | нет | нет |
> | ресерватионсуммариес | нет | нет |
> | ресерватионтрансактионс | нет | нет |
> | tags | нет | нет |
> | tenants | нет | нет |
> | terms | нет | нет |
> | usagedetails | нет | нет |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | нет | нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | нет | нет |
> | сервицеассоЦиатионлинкс | нет | нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | Да | Да |
> | реестры и буилдтаскс | Да | Да |
> | реестры и репликации | Да | Да |
> | реестры и таскрунс | Да | Да |
> | реестры и задачи | Да | Да |
> | реестры и веб-перехватчики | Да | Да |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | нет | нет |
> | managedclusters | нет | нет |
> | openshiftmanagedclusters | нет | нет |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | нет | нет |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | оповещения | нет | нет |
> | увеличен | нет | нет |
> | соединители | Да | Да |
> | измерения | нет | нет |
> | экспорты | нет | нет |
> | екстерналсубскриптионс | нет | нет |
> | грешно | нет | нет |
> | query | нет | нет |
> | репортконфигс | нет | нет |
> | отчеты | нет | нет |
> | шовбаккрулес | нет | нет |
> | узел "Представления" | нет | нет |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | нет | нет |

## <a name="microsoftcustomproviders"></a>Microsoft. Кустомпровидерс

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | сопоставления | нет | нет |
> | ресаурцепровидерс | Да | Да |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | нет | нет |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | нет | нет |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | нет | нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | Да | Да |
> | каталоги | нет | нет |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | нет | нет |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | Пакеты | нет | нет |
> | Планы | нет | нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | Да | Да |
> | factories | Да | Да |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | нет | нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | services; | нет | нет |
> | службы и проекты | нет | нет |
> | slots | нет | нет |

## <a name="microsoftdataprotection"></a>Защита Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | баккупваултс | нет | нет |

## <a name="microsoftdatashare"></a>Общая папка Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Да | Да |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Да | Да |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | нет | нет |
> | servers | Да | Да |
> | serversv2 | Да | Да |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | Да | Да |
> | rollouts | Да | Да |
> | servicetopologies | Да | Да |
> | сервицетопологиес и службы | Да | Да |
> | сервицетопологиес/Services/сервицеунитс | Да | Да |
> | steps | Да | Да |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | нет | нет |
> | еластикпулс/иосубтенантс | нет | нет |
> | iothubs | Да | Да |
> | provisioningservices | Да | Да |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | конвейеров | Да | Да |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | Да | Да |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | нет | нет |
> | labs | Да | нет |
> | лаборатории и среды | Да | Да |
> | Labs и сервицеруннерс | Да | Да |
> | Labs и virtualmachines | Да | нет |
> | schedules | Да | Да |

## <a name="microsoftdigitaltwins"></a>Microsoft. Дигиталтвинс

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | дигиталтвинсинстанцес | нет | нет |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Да | Да |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Да | Да |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | services; | Да | Да |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Да | Да |
> | eventSubscriptions | Нет — нельзя перемещать независимо, но автоматически перемещать вместе с подписанным ресурсом. | Нет — нельзя перемещать независимо, но автоматически перемещать вместе с подписанным ресурсом. |
> | EventSubscriptions | Нет — нельзя перемещать независимо, но автоматически перемещать вместе с подписанным ресурсом. | Нет — нельзя перемещать независимо, но автоматически перемещать вместе с подписанным ресурсом. |
> | екстенсионтопикс | нет | нет |
> | topics | Да | Да |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Да | Да |
> | пространства имен | Да | Да |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | гуестконфигуратионассигнментс | нет | нет |
> | программное обеспечение | нет | нет |
> | софтвареупдатепрофиле | нет | нет |
> | софтвареупдатес | нет | нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | нет | нет |
> | сапмониторс | Да | Да |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Да | Да |

> [!IMPORTANT]
> Кластеры HDInsight можно переместить в новую подписку или группу ресурсов. Однако перемещение между подписками недоступно для сетевых ресурсов, связанных с кластером HDInsight (таких как виртуальная сеть, сетевой адаптер или подсистема балансировки нагрузки). Также невозможно переместить в новую группу ресурсов сетевой адаптер, который подключен к виртуальной машине кластера.
>
> При перемещении кластера HDInsight в новую подписку сначала необходимо переместить другие ресурсы (такие как учетная запись хранения). А затем переместить сам кластер HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | services; | Да | Да |

## <a name="microsofthybridcompute"></a>Microsoft. Хибридкомпуте

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | виртуальных | Да | Да |
> | Компьютеры и расширения | нет | нет |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | Да | Да |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Да | Да |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | Да | Да |
> | activitylogalerts | нет | нет |
> | alertrules | Да | Да |
> | autoscalesettings | Да | Да |
> | baseline | нет | нет |
> | calculatebaseline | нет | нет |
> | components | Да | Да |
> | DiagnosticSettings | нет | нет |
> | диагностиксеттингскатегориес | нет | нет |
> | eventtypes | нет | нет |
> | екстендеддиагностиксеттингс | нет | нет |
> | логдефинитионс | нет | нет |
> | журналы | нет | нет |
> | metricalerts | нет | нет |
> | метрикбаселинес | нет | нет |
> | metricdefinitions | нет | нет |
> | метрикнамеспацес | нет | нет |
> | Метрики | нет | нет |
> | миворкбукс | нет | нет |
> | привателинкскопес | Да | Да |
> | scheduledqueryrules | Да | Да |
> | Топология | нет | нет |
> | транзакции | нет | нет |
> | вминсигхтсонбоардингстатусес | нет | нет |
> | webtests | Да | Да |
> | книги | Да | Да |
> | воркбуктемплатес | Да | Да |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | Да | Да |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Да | Да |
> | graph | Да | Да |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | vaults | Да | Да |

> [!IMPORTANT]
> Хранилища ключей, используемые для шифрования дисков, нельзя переместить в группу ресурсов в одной и той же подписке или в рамках подписок.

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | коннектедклустерс | нет | нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Да | Да |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | нет | нет |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | нет | нет |
> | integrationaccounts | Да | Да |
> | integrationserviceenvironments | Да | нет |
> | интегратионсервицеенвиронментс/манажедапис | Да | нет |
> | isolatedenvironments | нет | нет |
> | workflows | Да | Да |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Да | Да |
> | webservices | Да | нет |
> | workspaces | Да | Да |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | нет | нет |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |
> | учетные записи и рабочие области | нет | нет |
> | учетные записи, рабочие области и проекты | нет | нет |
> | teamaccounts | нет | нет |
> | теамаккаунтс и рабочие области | нет | нет |
> | теамаккаунтс/рабочие области и проекты | нет | нет |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | нет | нет |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | нет | нет |
> | рабочие области и расчеты | нет | нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | удостоверения | нет | нет |
> | userassignedidentities | нет | нет |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | регистратионассигнментс | нет | нет |
> | регистратиондефинитионс | нет | нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | нет | нет |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | Да | Да |
> | mediaservices/лививентс | Да | Да |
> | mediaservices/streamingendpoint | Да | Да |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | аппклустерс | нет | нет |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Да | Да |
> | migrateprojects | Да | Да |
> | проекты | нет | нет |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | нет | нет |
> | нетаппаккаунтс/баккупполиЦиес | нет | нет |
> | нетаппаккаунтс/капаЦитипулс | нет | нет |
> | нетаппаккаунтс/капаЦитипулс/тома | нет | нет |
> | нетаппаккаунтс/капаЦитипулс/Volumes/маунттаржетс | нет | нет |
> | нетаппаккаунтс/капаЦитипулс/тома/моментальные снимки | нет | нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | нет | нет |
> | аппликатионгатевайвебаппликатионфиреваллполиЦиес | нет | нет |
> | applicationsecuritygroups | Да | Да |
> | azurefirewalls | Да | Да |
> | bastionhosts | нет | нет |
> | connections | Да | Да |
> | ddoscustompolicies | Да | Да |
> | ddosprotectionplans | нет | нет |
> | dnszones | Да | Да |
> | expressroutecircuits | нет | нет |
> | expressroutegateways | нет | нет |
> | фиреваллполиЦиес | Да | Да |
> | frontdoors | нет | нет |
> | frontdoorwebapplicationfirewallpolicies | нет | нет |
> | ипграупс | Да | Да |
> | loadbalancers | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br>SKU No-Standard |
> | localnetworkgateways | Да | Да |
> | нетворкекспериментпрофилес | Да | Да |
> | networkintentpolicies | Да | Да |
> | networkinterfaces | Да | Да |
> | networkprofiles | нет | нет |
> | networksecuritygroups | Да | Да |
> | networkwatchers | Да | нет |
> | нетворкватчерс/коннектионмониторс | Да | нет |
> | нетворкватчерс/панель | Да | нет |
> | нетворкватчерс/lenses | Да | нет |
> | нетворкватчерс/пингмешес | Да | нет |
> | p2svpngateways | нет | нет |
> | приватеднсзонес | Да | Да |
> | приватеднсзонес/виртуалнетворклинкс | Да | Да |
> | приватиндпоинтредиректмапс | нет | нет |
> | приватиндпоинтс | нет | нет |
> | privatelinkservices | нет | нет |
> | publicipaddresses | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br>SKU No-Standard |
> | publicipprefixes | Да | Да |
> | routefilters | нет | нет |
> | routetables | Да | Да |
> | serviceendpointpolicies | Да | Да |
> | trafficmanagerprofiles | Да | Да |
> | virtualhubs | нет | нет |
> | virtualnetworkgateways | Да | Да |
> | virtualnetworks | Да | Да |
> | virtualnetworktaps | нет | нет |
> | виртуалраутерс | Да | Да |
> | virtualwans | нет | нет |
> | впнгатевайс (Виртуальная глобальная сеть) | нет | нет |
> | впнсерверконфигуратионс | нет | нет |
> | впнситес (Виртуальная глобальная сеть) | нет | нет |
> | webapplicationfirewallpolicies | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению в сеть](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | пространства имен | Да | Да |
> | пространства имен/notificationhubs | Да | Да |

## <a name="microsoftobjectstore"></a>Microsoft. Обжектсторе

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | оснамеспацес | Да | Да |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | сторажеинсигхтконфигс | нет | нет |
> | workspaces | Да | Да |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | нет | нет |
> | managementconfigurations | Да | Да |
> | solutions | Да | Да |
> | узел "Представления" | Да | Да |

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | пиринги | Да | Да |
> | пирингсервицес | нет | нет |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | полициевентс | нет | нет |
> | полицистатес | нет | нет |
> | полицитраккедресаурцес | нет | нет |
> | remediations | нет | нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | dashboards | Да | Да |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | нет | нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Да | Да |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | Да | Да |

## <a name="microsoftprojectbabylon"></a>Microsoft. Прожектбабилон

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | нет | нет |

## <a name="microsoftproviderhub"></a>Microsoft. Провидерхуб

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | нет | нет |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | баккуппротектедитемс | нет | нет |
> | репликатионелигибилитиресултс | нет | нет |
> | vaults | Да | Да |

> [!IMPORTANT]
> См. [руководство по перемещению служб восстановления](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | пространства имен | Да | Да |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | Запросы | Да | Да |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | аваилабилитистатусес | нет | нет |
> | чилдаваилабилитистатусес | нет | нет |
> | чилдресаурцес | нет | нет |
> | события | нет | нет |
> | уведомления | нет | нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | нет | нет |
> | ссылки | нет | нет |
> | tags | нет | нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | Да | нет |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | Да | Да |

> [!IMPORTANT]
> В одной операции невозможно переместить несколько ресурсов поиска в разных регионах. Переместите их в отдельных операциях.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | адаптивенетворкхарденингс | нет | нет |
> | адванцедсреатпротектионсеттингс | нет | нет |
> | ассессментметадата | нет | нет |
> | Оценки | нет | нет |
> | инструментах автоматизации Composer | Да | Да |
> | комплианцересултс | нет | нет |
> | требованиям | нет | нет |
> | датаколлектионажентс | нет | нет |
> | девицесекуритиграупс | нет | нет |
> | информатионпротектионполиЦиес | нет | нет |
> | иотсекуритисолутионс | Да | Да |
> | сервервулнерабилитяссессментс | нет | нет |

## <a name="microsoftsecurityinsights"></a>Microsoft. Секуритинсигхтс

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | нет | нет |
> | alertrules | нет | нет |
> | алертрулетемплатес | нет | нет |
> | закладки | нет | нет |
> | cases | нет | нет |
> | подключения к компонентам | нет | нет |
> | датаконнекторсчеккрекуирементс | нет | нет |
> | Сущности | нет | нет |
> | ентитикуериес | нет | нет |
> | возникш | нет | нет |
> | оффицеконсентс | нет | нет |
> | параметры | нет | нет |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | нет | нет |
> | Узлы | нет | нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | пространства имен | Да | Да |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | нет | нет |
> | clusters | Да | Да |
> | кластеры и приложения | нет | нет |
> | containergroups | нет | нет |
> | containergroupsets | нет | нет |
> | edgeclusters | нет | нет |
> | managedclusters | нет | нет |
> | сети | нет | нет |
> | secretstores | нет | нет |
> | volumes. | нет | нет |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | веб-масштабированием; | Да | Да |
> | gateways | Да | Да |
> | сети | Да | Да |
> | секретные коды | Да | Да |
> | volumes. | Да | Да |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | нет | нет |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | Да | Да |

## <a name="microsoftsoftwareplan"></a>Microsoft. Софтвареплан

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | хибридусебенефитс | нет | нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | нет | нет |
> | веб-масштабированием; | нет | нет |
> | jitrequests | нет | нет |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | инстанцепулс | нет | нет |
> | managedinstances | нет | нет |
> | манажединстанцес и базы данных | нет | нет |
> | servers | Да | Да |
> | серверы и базы данных | Да | Да |
> | серверы и еластикпулс | Да | Да |
> | серверы и жобаккаунтс | Да | Да |
> | серверы и жобажентс | Да | Да |
> | virtualclusters | Да | Да |

> [!IMPORTANT]
> База данных и сервер должны находиться в одной группе ресурсов. При перемещении сервера SQL Server все его базы данных также перемещаются. Такое поведение также характерно для баз данных SQL Azure и баз данных хранилища данных SQL Azure.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Да | Да |
> | sqlvirtualmachines | Да | Да |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | нет | нет |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Да | Да |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Да | Да |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | нет | нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | нет | нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | нет | нет |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Да | Да |

> [!IMPORTANT]
> Невозможно переместить задания Stream Analytics в состоянии выполнения.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | нет | нет |
> | среды и классов EventSource | нет | нет |
> | instances | нет | нет |
> | экземпляры и среды | нет | нет |
> | экземпляры/среды/классов EventSource | нет | нет |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | креатесубскриптион | нет | нет |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | supporttickets | нет | нет |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | нет | нет |
> | ресурсов | нет | нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Да | Да |
> | среды и классов EventSource | Да | Да |
> | среды и референцедатасетс | Да | Да |

## <a name="microsofttoken"></a>Microsoft. Token

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | хранилища | Да | Да |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | account | нет | нет |
> | Учетная запись или расширение | Да | Да |
> | Учетная запись или проект | Да | Да |

> [!IMPORTANT]
> Чтобы изменить подписку на Azure DevOps, см. статью [изменение подписки Azure, используемой для выставления счетов](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | дедикатедклауднодес | нет | нет |
> | дедикатедклаудсервицес | нет | нет |
> | virtualmachines | нет | нет |

## <a name="microsoftvsonline"></a>Microsoft. Всонлине

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Да | Да |
> | Планы | Да | Да |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | certificates | нет | Да |
> | connectiongateways | Да | Да |
> | connections | Да | Да |
> | customapis | Да | Да |
> | hostingenvironments | нет | нет |
> | serverfarms | Да | Да |
> | sites | Да | Да |
> | Sites/премиераддонс | Да | Да |
> | сайты и слоты | Да | Да |
> | статикситес | нет | нет |

> [!IMPORTANT]
> См. [руководство по перемещению службы приложений](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | нет | нет |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Тип ресурса | группа ресурсов. | Subscription |
> | ------------- | ----------- | ---------- |
> | components | нет | нет |
> | мониторинстанцес | нет | нет |
> | monitors | нет | нет |
> | нотификатионсеттингс | нет | нет |

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.

## <a name="next-steps"></a>Дальнейшие действия
Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](move-resource-group-and-subscription.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
