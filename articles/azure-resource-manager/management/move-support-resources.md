---
title: Поддержка операций перемещения по типу ресурса
description: Список типов ресурсов Azure, подлежащих перемещению в новую группу ресурсов или подписку.
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 8ab194ad240e4f3e0994314ef9ade3bc7159cf81
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671852"
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
> | рекомендации | Нет | Нет |
> | suppressions | Нет | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | актионрулес | Да | Да |
> | предупреждения | Нет | Нет |
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
> | блокировки | Нет | Нет |
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
> | кластеры | Нет | Нет |
> | fileservers | Нет | Нет |
> | задания | Нет | Нет |
> | рабочие области | Нет | Нет |

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
> | biztalk | Нет | Нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Нет | Нет |
> | наблюдателей | Нет | Нет |

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
> | учетные записи | Да | Да |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Да | Да |
> | дискенкриптионсетс | Нет | Нет |
> | диски | Да | Да |
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
> | снимки | Да | Да |
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
> | держек | Нет | Нет |
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
> | приложения | Нет | Нет |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | учетные записи | Нет | Нет |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | предупреждения | Нет | Нет |
> | увеличен | Нет | Нет |
> | соединители | Да | Да |
> | измерения | Нет | Нет |
> | экспорты | Нет | Нет |
> | екстерналсубскриптионс | Нет | Нет |
> | грешно | Нет | Нет |
> | query | Нет | Нет |
> | репортконфигс | Нет | Нет |
> | отчеты | Нет | Нет |
> | шовбаккрулес | Нет | Нет |
> | представления | Нет | Нет |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | Нет | Нет |

## <a name="microsoftcustomproviders"></a>Microsoft. Кустомпровидерс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | сопоставления | Нет | Нет |
> | ресаурцепровидерс | Да | Да |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | задания | Нет | Нет |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Нет | Нет |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | рабочие области | Нет | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | каталоги | Да | Да |
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
> | планы | Нет | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | Да | Да |
> | фабрики | Да | Да |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Нет | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | учетные записи | Да | Да |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | учетные записи | Да | Да |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | службы | Нет | Нет |
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
> | учетные записи | Да | Да |

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
> | шаги | Да | Да |

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
> | расписания | Да | Да |

## <a name="microsoftdigitaltwins"></a>Microsoft. Дигиталтвинс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | дигиталтвинсинстанцес | Нет | Нет |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Да | Да |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | домены | Да | Да |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | службы | Да | Да |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | домены | Да | Да |
> | eventSubscriptions | Нет — нельзя перемещать независимо, но автоматически перемещать вместе с подписанным ресурсом. | Нет — нельзя перемещать независимо, но автоматически перемещать вместе с подписанным ресурсом. |
> | EventSubscriptions | Нет — нельзя перемещать независимо, но автоматически перемещать вместе с подписанным ресурсом. | Нет — нельзя перемещать независимо, но автоматически перемещать вместе с подписанным ресурсом. |
> | екстенсионтопикс | Нет | Нет |
> | topics | Да | Да |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | кластеры | Да | Да |
> | пространства имен | Да | Да |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | учетные записи | Нет | Нет |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | гуестконфигуратионассигнментс | Нет | Нет |
> | программное обеспечение | Нет | Нет |
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
> | кластеры | Да | Да |

> [!IMPORTANT]
> Кластеры HDInsight можно переместить в новую подписку или группу ресурсов. Однако перемещение между подписками недоступно для сетевых ресурсов, связанных с кластером HDInsight (таких как виртуальная сеть, сетевой адаптер или подсистема балансировки нагрузки). Также невозможно переместить в новую группу ресурсов сетевой адаптер, который подключен к виртуальной машине кластера.
>
> При перемещении кластера HDInsight в новую подписку сначала необходимо переместить другие ресурсы (такие как учетная запись хранения). А затем переместить сам кластер HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | службы | Да | Да |

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
> | задания | Да | Да |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | Да | Да |
> | activitylogalerts | Нет | Нет |
> | alertrules | Да | Да |
> | autoscalesettings | Да | Да |
> | направляющая | Нет | Нет |
> | calculatebaseline | Нет | Нет |
> | компоненты | Да | Да |
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
> | метрики | Нет | Нет |
> | миворкбукс | Нет | Нет |
> | привателинкскопес | Да | Да |
> | scheduledqueryrules | Да | Да |
> | Топология | Нет | Нет |
> | транзакции | Нет | Нет |
> | вминсигхтсонбоардингстатусес | Нет | Нет |
> | webtests | Да | Да |
> | книги | Да | Да |
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
> | кластеры | Да | Да |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | Нет | Нет |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | учетные записи | Нет | Нет |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | учетные записи | Нет | Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Нет | Нет |
> | integrationaccounts | Да | Да |
> | integrationserviceenvironments | Да | Нет |
> | интегратионсервицеенвиронментс/манажедапис | Да | Нет |
> | isolatedenvironments | Нет | Нет |
> | рабочие процессы | Да | Да |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Да | Да |
> | webservices | Да | Нет |
> | рабочие области | Да | Да |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Нет | Нет |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | учетные записи | Нет | Нет |
> | учетные записи и рабочие области | Нет | Нет |
> | учетные записи, рабочие области и проекты | Нет | Нет |
> | teamaccounts | Нет | Нет |
> | теамаккаунтс и рабочие области | Нет | Нет |
> | теамаккаунтс/рабочие области и проекты | Нет | Нет |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | учетные записи | Нет | Нет |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Нет | Нет |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | рабочие области | Нет | Нет |
> | рабочие области и расчеты | Нет | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | удостоверения | Нет | Нет |
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
> | учетные записи | Да | Да |

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
> | проекты | Нет | Нет |

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
> | фиреваллполиЦиес | Да | Да |
> | frontdoors | Нет | Нет |
> | frontdoorwebapplicationfirewallpolicies | Нет | Нет |
> | ипграупс | Да | Да |
> | loadbalancers | Да — SKU "базовый"<br>SKU No-Standard | Да — SKU "базовый"<br>SKU No-Standard |
> | localnetworkgateways | Да | Да |
> | нетворкекспериментпрофилес | Да | Да |
> | networkintentpolicies | Да | Да |
> | networkinterfaces | Да | Да |
> | networkprofiles | Нет | Нет |
> | networksecuritygroups | Да | Да |
> | networkwatchers | Да | Нет |
> | нетворкватчерс/коннектионмониторс | Да | Нет |
> | нетворкватчерс/панель | Да | Нет |
> | нетворкватчерс/lenses | Да | Нет |
> | нетворкватчерс/пингмешес | Да | Нет |
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
> | рабочие области | Да | Да |

> [!IMPORTANT]
> Убедитесь, что перемещение в новую подписку не превышает [квоты подписки](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | Нет | Нет |
> | managementconfigurations | Да | Да |
> | решения | Да | Да |
> | представления | Да | Да |

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
> | панели мониторинга | Да | Да |

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
> | емкости | Да | Да |

## <a name="microsoftprojectbabylon"></a>Microsoft. Прожектбабилон

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | учетные записи | Нет | Нет |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | учетные записи | Нет | Нет |

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
> | уведомления | Нет | Нет |

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
> | приложения | Да | Нет |

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
> | Оценки | Нет | Нет |
> | инструментах автоматизации Composer | Да | Да |
> | комплианцересултс | Нет | Нет |
> | требованиям | Нет | Нет |
> | датаколлектионажентс | Нет | Нет |
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
> | датаконнекторсчеккрекуирементс | Нет | Нет |
> | сущности | Нет | Нет |
> | ентитикуериес | Нет | Нет |
> | возникш | Нет | Нет |
> | оффицеконсентс | Нет | Нет |
> | параметры | Нет | Нет |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | Нет | Нет |
> | кластера | Нет | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | пространства имен | Да | Да |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | приложения | Нет | Нет |
> | кластеры | Да | Да |
> | кластеры и приложения | Нет | Нет |
> | containergroups | Нет | Нет |
> | containergroupsets | Нет | Нет |
> | edgeclusters | Нет | Нет |
> | managedclusters | Нет | Нет |
> | сети | Нет | Нет |
> | secretstores | Нет | Нет |
> | volumes. | Нет | Нет |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | приложения | Да | Да |
> | gateways | Да | Да |
> | сети | Да | Да |
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
> | приложения | Нет | Нет |
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
> | серверы и жобаккаунтс | Да | Да |
> | серверы и жобажентс | Да | Да |
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
> | среды | Нет | Нет |
> | среды и классов EventSource | Нет | Нет |
> | экземпляры | Нет | Нет |
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
> | supporttickets | Нет | Нет |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Нет | Нет |
> | ресурсы | Нет | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | среды | Да | Да |
> | среды и классов EventSource | Да | Да |
> | среды и референцедатасетс | Да | Да |

## <a name="microsofttoken"></a>Microsoft. Token

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | хранилища | Да | Да |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | учетная запись | Нет | Нет |
> | Учетная запись или расширение | Да | Да |
> | Учетная запись или проект | Да | Да |

> [!IMPORTANT]
> Чтобы изменить подписку на Azure DevOps, см. статью [изменение подписки Azure, используемой для выставления счетов](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

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
> | учетные записи | Да | Да |
> | планы | Да | Да |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Группа ресурсов | Subscription |
> | ------------- | ----------- | ---------- |
> | сертификаты | Нет | Да |
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
> | компоненты | Нет | Нет |
> | мониторинстанцес | Нет | Нет |
> | мониторы | Нет | Нет |
> | нотификатионсеттингс | Нет | Нет |

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.

## <a name="next-steps"></a>Следующие шаги
Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](move-resource-group-and-subscription.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
