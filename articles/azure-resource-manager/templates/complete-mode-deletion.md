---
title: Удаление ресурсов в полном режиме
description: Здесь показано, как происходит удаление ресурсов в полном режиме в шаблонах Azure Resource Manager по типу ресурса.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b24bf2d8076dbaa1975f5444d597647156a31f4f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715598"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Удаление ресурсов Azure для развертываний в полном режиме

В этой статье описывается, как процесс удаления будет обрабатываться различными типами, когда он не происходит в шаблоне, развернутом в завершенном режиме.

Типы ресурсов, помеченные **Да** , удаляются, если тип не находится в шаблоне, развернутом в полном режиме.

Типы ресурсов, помеченные как " **нет** ", не удаляются автоматически при отсутствии в шаблоне. Однако они удаляются, если родительский ресурс удаляется. Чтобы получить полное описание поведения см. статью [Режимы развертывания в Azure Resource Manager](deployment-modes.md).

Если развернуть в [шаблоне несколько групп ресурсов](cross-resource-group-deployment.md), то ресурсы в группе ресурсов, указанной в операции развертывания, могут быть удалены. Ресурсы во вторичных группах ресурсов не удаляются.

Переход к пространству имен поставщика ресурсов:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Надстройки Microsoft.](#microsoftaddons)
> - [Microsoft. Адхибридхеалссервице](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. Алертсманажемент](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft. Аппконфигуратион](#microsoftappconfiguration)
> - [Microsoft. Аппплатформ](#microsoftappplatform)
> - [Microsoft. Аттестация](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft. Азконфиг](#microsoftazconfig)
> - [Microsoft. Azure. Geneva](#microsoftazuregeneva)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. Азуредата](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. Batch](#microsoftbatch)
> - [Microsoft. выставление счетов](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. Блокчейн](#microsoftblockchain)
> - [Microsoft. чертеж](#microsoftblueprint)
> - [Microsoft. Ботсервице](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft. Цертификатерегистратион](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. КлассиЦинфраструктуремиграте](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. Классикстораже](#microsoftclassicstorage)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft. COMPUTE](#microsoftcompute)
> - [Microsoft. потребление](#microsoftconsumption)
> - [Microsoft. Контаинеринстанце](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft. Кортанааналитикс](#microsoftcortanaanalytics)
> - [Microsoft. Костманажемент](#microsoftcostmanagement)
> - [Microsoft. Кустомерлоккбокс](#microsoftcustomerlockbox)
> - [Microsoft. Кустомпровидерс](#microsoftcustomproviders)
> - [Microsoft. Датабокс](#microsoftdatabox)
> - [Microsoft. Датабокседже](#microsoftdataboxedge)
> - [Microsoft. кирпичы](#microsoftdatabricks)
> - [Каталог Microsoft.](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft. Перенос](#microsoftdatamigration)
> - [Общая папка Microsoft.](#microsoftdatashare)
> - [Microsoft. Дбформариадб](#microsoftdbformariadb)
> - [Microsoft. Дбформискл](#microsoftdbformysql)
> - [Microsoft. Дбфорпостгрескл](#microsoftdbforpostgresql)
> - [Microsoft. Деплойментманажер](#microsoftdeploymentmanager)
> - [Microsoft. Десктопвиртуализатион](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. Девспацес](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. Домаинрегистратион](#microsoftdomainregistration)
> - [Microsoft. Динамикслкс](#microsoftdynamicslcs)
> - [Microsoft. Ентерприсекновледжеграф](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. Features](#microsoftfeatures)
> - [Коллекция Microsoft. Gallery](#microsoftgallery)
> - [Microsoft. Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. Ханаоназуре](#microsofthanaonazure)
> - [Microsoft. Хардваресекуритимодулес](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft. Хеалскареапис](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft. Хибриддата](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Intune](#microsoftintune)
> - [Microsoft. Иотцентрал](#microsoftiotcentral)
> - [Microsoft. Иотспацес](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft. Kusto](#microsoftkusto)
> - [Microsoft. Лабсервицес](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. Мачинелеарнингсервицес](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft. Маркетплацеаппс](#microsoftmarketplaceapps)
> - [Microsoft. Маркетплацеордеринг](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. migrate](#microsoftmigrate)
> - [Microsoft. Микседреалити](#microsoftmixedreality)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. Обжектсторе](#microsoftobjectstore)
> - [Microsoft. Оффазуре](#microsoftoffazure)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. пиринг](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights).
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. Повербидедикатед](#microsoftpowerbidedicated)
> - [Microsoft. Прожектбабилон](#microsoftprojectbabylon)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. Ресаурцеграф](#microsoftresourcegraph)
> - [Microsoft. Ресаурцехеалс](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. Секуритиграф](#microsoftsecuritygraph)
> - [Microsoft. Секуритинсигхтс](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft. Сервицефабрикмеш](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. Сигналрсервице](#microsoftsignalrservice)
> - [Microsoft. SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. Софтвареплан](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. Склвиртуалмачине](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. Сторажекаче](#microsoftstoragecache)
> - [Microsoft. Сторажерепликатион](#microsoftstoragereplication)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. Сторажесинкдев](#microsoftstoragesyncdev)
> - [Microsoft. СторажесинЦинт](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. Вмвареклаудсимпле](#microsoftvmwarecloudsimple)
> - [Microsoft. Внфманажер](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft. Виндовсиот](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | DomainServices | Да |
> | DomainServices/ауконтаинер | нет |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | supportProviders | нет |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | aadsupportcases | нет |
> | addsservices | нет |
> | agents | нет |
> | anonymousapiusers | нет |
> | настройка | нет |
> | журналы | нет |
> | reports | нет |
> | servicehealthmetrics | нет |
> | services; | нет |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | конфигурации | нет |
> | generateRecommendations | нет |
> | метаданные | нет |
> | к просмотру фильмов | нет |
> | suppressions | нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | actionRules | Да |
> | оповещения | нет |
> | alertsList | нет |
> | алертсметадата | нет |
> | alertsSummary | нет |
> | alertsSummaryList | нет |
> | обратная связь | нет |
> | smartDetectorAlertRules | Да |
> | smartDetectorRuntimeEnvironments | нет |
> | smartGroups | нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | servers | Да |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | reportFeedback | нет |
> | служба | Да |
> | validateServiceName | нет |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | конфигуратионсторес | Да |
> | Конфигуратионсторес/Евентгридфилтерс | нет |

## <a name="microsoftappplatform"></a>Microsoft. Аппплатформ

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Spring | Да |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | attestationProviders | нет |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | classicAdministrators | нет |
> | Псевдонимы | нет |
> | denyAssignments | нет |
> | elevateAccess | нет |
> | финдорфанролеассигнментс | нет |
> | locks | нет |
> | разрешения | нет |
> | policyAssignments | нет |
> | policyDefinitions | нет |
> | policySetDefinitions | нет |
> | providerOperations | нет |
> | roleAssignments | нет |
> | ролеассигнментсусажеметрикс | нет |
> | roleDefinitions | нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | automationAccounts | Да |
> | automationAccounts и конфигурации | Да |
> | automationAccounts и задания | нет |
> | automationAccounts и модули Runbook | Да |
> | automationAccounts/Софтвареупдатеконфигуратионс | нет |
> | automationAccounts/веб-перехватчики | нет |

## <a name="microsoftazconfig"></a>Microsoft. Азконфиг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | конфигуратионсторес | Да |
> | Конфигуратионсторес/Евентгридфилтерс | нет |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | environments | нет |
> | среды и учетные записи | нет |
> | среды, учетные записи и пространства имен | нет |
> | среды, учетные записи, пространства имен и конфигурации | нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | b2cDirectories | Да |
> | b2ctenants | нет |

## <a name="microsoftazuredata"></a>Microsoft. Азуредата

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | хибриддатаманажерс | Да |
> | постгресинстанцес | Да |
> | склбигдатаклустерс | Да |
> | склинстанцес | Да |
> | склсерверрегистратионс | Да |
> | Склсерверрегистратионс и sqlServer | нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | registrations | Да |
> | регистрации и Кустомерсубскриптионс | нет |
> | регистрации и продукты | нет |
> | верификатионкэйс | нет |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | batchAccounts | Да |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | billingAccounts | нет |
> | Биллингаккаунтс и соглашения | нет |
> | Биллингаккаунтс/Биллингпермиссионс | нет |
> | Биллингаккаунтс/Биллингпрофилес | нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингпермиссионс | нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингролеассигнментс | нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингроледефинитионс | нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингсубскриптионс | нет |
> | Биллингаккаунтс/Биллингпрофилес/Креатебиллингролеассигнмент | нет |
> | Биллингаккаунтс/Биллингпрофилес/клиенты | нет |
> | Биллингаккаунтс/Биллингпрофилес/инструкции | нет |
> | Биллингаккаунтс/Биллингпрофилес/счета | нет |
> | Биллингаккаунтс/Биллингпрофилес/счета/прайс-лист | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингпермиссионс | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингролеассигнментс | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингроледефинитионс | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингсубскриптионс | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Креатебиллингролеассигнмент | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Инитиатетрансфер | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products/перемещение | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products/Упдатеауторенев | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/транзакции | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/передача | нет |
> | Биллингаккаунтс/Биллингпрофилес/Патчоператионс | нет |
> | Биллингаккаунтс/Биллингпрофилес/Пайментмесодс | нет |
> | Биллингаккаунтс/Биллингпрофилес/политики | нет |
> | Биллингаккаунтс/Биллингпрофилес/прайс-лист | нет |
> | Биллингаккаунтс/Биллингпрофилес/Прицешитдовнлоадоператионс | нет |
> | Биллингаккаунтс/Биллингпрофилес/Products | нет |
> | Биллингаккаунтс/Биллингпрофилес/транзакции | нет |
> | Биллингаккаунтс/Биллингролеассигнментс | нет |
> | Биллингаккаунтс/Биллингроледефинитионс | нет |
> | Биллингаккаунтс/Биллингсубскриптионс | нет |
> | Биллингаккаунтс/Биллингсубскриптионс/счета | нет |
> | Биллингаккаунтс/Креатебиллингролеассигнмент | нет |
> | Биллингаккаунтс/Креатеинвоицесектионоператионс | нет |
> | Биллингаккаунтс и клиенты | нет |
> | Биллингаккаунтс/Customers/Биллингпермиссионс | нет |
> | Биллингаккаунтс/Customers/Биллингсубскриптионс | нет |
> | Биллингаккаунтс/Customers/Инитиатетрансфер | нет |
> | Биллингаккаунтс, клиенты и политики | нет |
> | Биллингаккаунтс/Customers/Products | нет |
> | Биллингаккаунтс/Customers/Transactions | нет |
> | Биллингаккаунтс/Customers/Transfers | нет |
> | Биллингаккаунтс и отделы | нет |
> | Биллингаккаунтс/Енроллментаккаунтс | нет |
> | Биллингаккаунтс/счета | нет |
> | Биллингаккаунтс/Инвоицесектионс | нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионмовеоператионс | нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионс | нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионс/перемещение | нет |
> | Биллингаккаунтс/Инвоицесектионс/повышение прав | нет |
> | Биллингаккаунтс/Инвоицесектионс/Инитиатетрансфер | нет |
> | Биллингаккаунтс/Инвоицесектионс/Патчоператионс | нет |
> | Биллингаккаунтс/Инвоицесектионс/Продуктмовеоператионс | нет |
> | Биллингаккаунтс/Инвоицесектионс/Products | нет |
> | Биллингаккаунтс/Инвоицесектионс/продукты/перемещение | нет |
> | Биллингаккаунтс/Инвоицесектионс/Products/Упдатеауторенев | нет |
> | Биллингаккаунтс/Инвоицесектионс/транзакции | нет |
> | Биллингаккаунтс/Инвоицесектионс/передача | нет |
> | Биллингаккаунтс/Линеофкредит | нет |
> | Биллингаккаунтс/Патчоператионс | нет |
> | Биллингаккаунтс/Пайментмесодс | нет |
> | Биллингаккаунтс и продукты | нет |
> | Биллингаккаунтс/транзакции | нет |
> | billingPeriods | нет |
> | биллингпермиссионс | нет |
> | billingProperty | нет |
> | биллингролеассигнментс | нет |
> | биллингроледефинитионс | нет |
> | креатебиллингролеассигнмент | нет |
> | departments | нет |
> | enrollmentAccounts | нет |
> | invoices | нет |
> | transfers | нет |
> | передачи/Акцепттрансфер | нет |
> | передачи/Деклинетрансфер | нет |
> | передачи/значение operationstatus | нет |
> | передачи/Валидатетрансфер | нет |
> | валидатеаддресс | нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | mapApis | Да |
> | updateCommunicationPreference | нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | блоккчаинмемберс | Да |
> | кордамемберс | Да |
> | наблюдателей | Да |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | blueprintAssignments | нет |
> | Блуепринтассигнментс/Ассигнментоператионс | нет |
> | Блуепринтассигнментс/операции | нет |
> | blueprints | нет |
> | схемы и артефакты | нет |
> | проекты и версии | нет |
> | схемы, версии и артефакты | нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | botServices | Да |
> | Ботсервицес и каналы | нет |
> | Ботсервицес и подключения | нет |
> | языки | нет |
> | шаблоны | нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Redis | Да |
> | RedisConfigDefinition | нет |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | appliedReservations | нет |
> | аутокуотаинкреасе | нет |
> | калкулатиксчанже | нет |
> | calculatePrice | нет |
> | калкулатепурчасеприце | нет |
> | catalogs | нет |
> | commercialReservationOrders | нет |
> | обмен валюты | нет |
> | плацепурчасеордер | нет |
> | reservationOrders | нет |
> | Ресерватионордерс/Калкулатерефунд | нет |
> | Ресерватионордерс/Merge | нет |
> | Ресерватионордерс/резервирования | нет |
> | Ресерватионордерс/резервирования/редакции | нет |
> | Ресерватионордерс/Return | нет |
> | Ресерватионордерс/Split | нет |
> | Ресерватионордерс/swap | нет |
> | reservations | нет |
> | ресаурцепровидерс | нет |
> | ресурсов | нет |
> | validateReservationOrder | нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | кднвебаппликатионфиреваллманажедрулесетс | нет |
> | кднвебаппликатионфиреваллполиЦиес | Да |
> | edgenodes | нет |
> | профили | Да |
> | профили и конечные точки | Да |
> | профили, конечные точки/кустомдомаинс | нет |
> | профили, конечные точки и источники | нет |
> | validateProbe | нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | certificateOrders | Да |
> | Certificateorder и сертификаты | нет |
> | validateCertificateRegistrationInformation | нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capabilities | нет |
> | domainNames | Да |
> | имя_домена/возможности | нет |
> | имя_домена/Интерналлоадбаланцерс | нет |
> | имя_домена/Сервицецертификатес | нет |
> | имя_домена/слоты | нет |
> | Доменные имя, слоты и роли | нет |
> | имя_домена/слоты/роли/metricDefinitions | нет |
> | имя_домена/слоты/роли/метрики | нет |
> | moveSubscriptionResources | нет |
> | operatingSystemFamilies | нет |
> | operatingSystems | нет |
> | quotas | нет |
> | resourceTypes | нет |
> | validateSubscriptionMoveAvailability | нет |
> | virtualMachines | Да |
> | virtualMachines/diagnosticSettings | нет |
> | virtualMachines/metricDefinitions | нет |
> | virtualMachines/метрики | нет |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | classicInfrastructureResources | нет |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capabilities | нет |
> | expressRouteCrossConnections | нет |
> | Експрессраутекроссконнектионс и пиринг | нет |
> | gatewaySupportedDevices | нет |
> | networkSecurityGroups | Да |
> | quotas | нет |
> | reservedIps | Да |
> | virtualNetworks | Да |
> | virtualNetworks/Ремотевиртуалнетворкпирингпроксиес | нет |
> | virtualNetworks/Виртуалнетворкпирингс | нет |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capabilities | нет |
> | disks | нет |
> | images | нет |
> | osImages | нет |
> | osPlatformImages | нет |
> | publicImages | нет |
> | quotas | нет |
> | storageAccounts | Да |
> | storageAccounts/Блобсервицес | нет |
> | storageAccounts/Филесервицес | нет |
> | storageAccounts/metricDefinitions | нет |
> | storageAccounts/метрики | нет |
> | storageAccounts/Куеуесервицес | нет |
> | storageAccounts и службы | нет |
> | storageAccounts/Services/diagnosticSettings | нет |
> | storageAccounts/Services/metricDefinitions | нет |
> | storageAccounts/Services/метрики | нет |
> | storageAccounts/Таблесервицес | нет |
> | storageAccounts/Вмимажес | нет |
> | vmImages | нет |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | RateCard | нет |
> | UsageAggregates | нет |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | availabilitySets | Да |
> | дискенкриптионсетс | Да |
> | disks | Да |
> | galleries | Да |
> | коллекции и приложения | нет |
> | коллекции, приложения и версии | нет |
> | коллекции и изображения | нет |
> | коллекции, изображения и версии | нет |
> | хостграупс | Да |
> | Хостграупс и узлы | Да |
> | images | Да |
> | проксимитиплацементграупс | Да |
> | restorePointCollections | Да |
> | Ресторепоинтколлектионс/Ресторепоинтс | нет |
> | sharedVMImages | Да |
> | Шаредвмимажес и версии | нет |
> | snapshots | Да |
> | virtualMachines | Да |
> | virtualMachines и расширения | Да |
> | virtualMachines/metricDefinitions | нет |
> | virtualMachineScaleSets | Да |
> | virtualMachineScaleSets и расширения | нет |
> | virtualMachineScaleSets/networkInterfaces | нет |
> | virtualMachineScaleSets/publicIPAddresses | нет |
> | virtualMachineScaleSets/virtualMachines | нет |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | нет |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | AggregatedCost | нет |
> | сведения о балансе. | нет |
> | сведения о бюджете; | нет |
> | Расходы | нет |
> | CostTags | нет |
> | credits | нет |
> | события | нет |
> | Прогнозы | нет |
> | lots | нет |
> | Marketplace | нет |
> | Pricesheets | нет |
> | products | нет |
> | ReservationDetails | нет |
> | ReservationRecommendations | нет |
> | ReservationSummaries | нет |
> | ReservationTransactions | нет |
> | Теги | нет |
> | tenants | нет |
> | Термины | нет |
> | UsageDetails | нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | containerGroups | Да |
> | serviceAssociationLinks | нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | registries | Да |
> | реестры и сборки | нет |
> | реестры/сборки/Отмена | нет |
> | реестры/сборки/Жетлоглинк | нет |
> | реестры и Буилдтаскс | Да |
> | реестры, Буилдтаскс и шаги | нет |
> | реестры и Евентгридфилтерс | нет |
> | реестры и Женератекредентиалс | нет |
> | реестры и Жетбуилдсаурцеуплоадурл | нет |
> | реестры и учетные данные | нет |
> | реестры и Импортимаже | нет |
> | реестры и Куеуебуилд | нет |
> | реестры и Реженератекредентиал | нет |
> | реестры и Реженератекредентиалс | нет |
> | реестры и репликации | Да |
> | реестры и запуски | нет |
> | реестры/запуски/Отмена | нет |
> | реестры и Счедулерун | нет |
> | реестры и Скопемапс | нет |
> | реестры и Таскрунс | Да |
> | реестры и задачи | Да |
> | реестры и маркеры | нет |
> | реестры и УпдатеполиЦиес | нет |
> | реестры и веб-перехватчики | Да |
> | реестры/веб-перехватчики/Жеткаллбаккконфиг | нет |
> | реестры, веб-перехватчики и проверка связи | нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | containerServices | Да |
> | managedClusters | Да |
> | опеншифтманажедклустерс | Да |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | видны узлы | нет |
> | BillingAccounts | нет |
> | сведения о бюджете; | нет |
> | клаудконнекторс | нет |
> | Соединители | Да |
> | Departments | нет |
> | Измерения | нет |
> | EnrollmentAccounts | нет |
> | Экспорт | нет |
> | екстерналбиллингаккаунтс | нет |
> | Екстерналбиллингаккаунтс и оповещения | нет |
> | Екстерналбиллингаккаунтс и измерения | нет |
> | Екстерналбиллингаккаунтс/прогноз | нет |
> | Екстерналбиллингаккаунтс/запрос | нет |
> | екстерналсубскриптионс | нет |
> | Екстерналсубскриптионс и оповещения | нет |
> | Екстерналсубскриптионс и измерения | нет |
> | Екстерналсубскриптионс/прогноз | нет |
> | Екстерналсубскриптионс/запрос | нет |
> | Прогноз | нет |
> | Запрос | нет |
> | регистрация | нет |
> | Reportconfigs | нет |
> | Отчеты | нет |
> | Настройки | нет |
> | шовбаккрулес | нет |
> | Представления | нет |

## <a name="microsoftcustomerlockbox"></a>Microsoft. Кустомерлоккбокс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | requests | нет |

## <a name="microsoftcustomproviders"></a>Microsoft. Кустомпровидерс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | сопоставления | нет |
> | ресаурцепровидерс | Да |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | jobs | Да |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Да |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | workspaces | Да |
> | рабочие области и Дбворкспацес | нет |
> | рабочие области и storageEncryption | нет |
> | рабочие области и Виртуалнетворкпирингс | нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | catalogs | Да |
> | каталоги | Да |
> | каталоги данных и источники данных | нет |
> | каталоги данных, источники данных и проверки | нет |
> | каталоги данных/DataSources/scans/DataSets | нет |
> | каталоги данных, источники данных, проверки и триггеры | нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | dataFactories | Да |
> | Коэффициенты и diagnosticSettings | нет |
> | Коэффициенты и metricDefinitions | нет |
> | dataFactorySchema | нет |
> | factories | Да |
> | фабрики/Интегратионрунтимес | нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |
> | Accounts/Даталакестореаккаунтс | нет |
> | Accounts/storageAccounts | нет |
> | Accounts/storageAccounts/Containers | нет |
> | Accounts/Трансфераналитиксунитс | нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |
> | Accounts/Евентгридфилтерс | нет |
> | Accounts/Фиреваллрулес | нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | services; | Да |
> | службы и проекты | Да |

## <a name="microsoftdatashare"></a>Общая папка Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |
> | учетные записи и общие папки | нет |
> | учетные записи, общие ресурсы и наборы данных | нет |
> | учетные записи, общие ресурсы и приглашения | нет |
> | Accounts/shares/провидершаресубскриптионс | нет |
> | Accounts/shares/Синчронизатионсеттингс | нет |
> | Accounts/шаресубскриптионс | нет |
> | Accounts/шаресубскриптионс/Консумерсаурцедатасетс | нет |
> | Accounts/шаресубскриптионс/датасетмаппингс | нет |
> | учетные записи/шаресубскриптионс/триггеры | нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | servers | Да |
> | серверы и помощники | нет |
> | серверы и ключи | нет |
> | серверы и Приватиндпоинтконнектионпроксиес | нет |
> | серверы и Приватиндпоинтконнектионс | нет |
> | серверы и Привателинкресаурцес | нет |
> | серверы и Куеритекстс | нет |
> | серверы и Рековераблесерверс | нет |
> | серверы и Топкуеристатистикс | нет |
> | серверы и Виртуалнетворкрулес | нет |
> | серверы и Ваитстатистикс | нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | servers | Да |
> | серверы и помощники | нет |
> | серверы и ключи | нет |
> | серверы и Приватиндпоинтконнектионпроксиес | нет |
> | серверы и Приватиндпоинтконнектионс | нет |
> | серверы и Привателинкресаурцес | нет |
> | серверы и Куеритекстс | нет |
> | серверы и Рековераблесерверс | нет |
> | серверы и Топкуеристатистикс | нет |
> | серверы и Виртуалнетворкрулес | нет |
> | серверы и Ваитстатистикс | нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | серверграупс | Да |
> | servers | Да |
> | серверы и помощники | нет |
> | серверы и ключи | нет |
> | серверы и Приватиндпоинтконнектионпроксиес | нет |
> | серверы и Приватиндпоинтконнектионс | нет |
> | серверы и Привателинкресаурцес | нет |
> | серверы и Куеритекстс | нет |
> | серверы и Рековераблесерверс | нет |
> | серверы и Топкуеристатистикс | нет |
> | серверы и Виртуалнетворкрулес | нет |
> | серверы и Ваитстатистикс | нет |
> | serversv2 | Да |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | артифактсаурцес | Да |
> | rollouts | Да |
> | сервицетопологиес | Да |
> | Сервицетопологиес и службы | Да |
> | Сервицетопологиес/Services/Сервицеунитс | Да |
> | steps | Да |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. Десктопвиртуализатион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | аппликатионграупс | Да |
> | аппликатионграупс и приложения | нет |
> | аппликатионграупс и настольные системы | нет |
> | аппликатионграупс/стартменуитемс | нет |
> | хостпулс | Да |
> | хостпулс/сессионхостс | нет |
> | хостпулс/сессионхостс/усерсессионс | нет |
> | хостпулс/усерсессионс | нет |
> | workspaces | Да |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | еластикпулс | Да |
> | Еластикпулс/Иосубтенантс | Да |
> | Еластикпулс/Иосубтенантс/securitySettings | нет |
> | IotHubs | Да |
> | IotHubs/Евентгридфилтерс | нет |
> | IotHubs/securitySettings | нет |
> | ProvisioningServices | Да |
> | usages | нет |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | конвейеров | Да |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | controllers | Да |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | labcenters | Да |
> | labs | Да |
> | лаборатории и среды | Да |
> | Labs и Сервицеруннерс | Да |
> | Labs и virtualMachines | Да |
> | schedules | Да |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | databaseAccountNames | нет |
> | databaseAccounts | Да |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | domains | Да |
> | домены/Домаиновнершипидентифиерс | нет |
> | generateSsoRequest | нет |
> | topLevelDomains | нет |
> | validateDomainRegistrationInformation | нет |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | lcsprojects | нет |
> | лкспрожектс/клауддеплойментс | нет |
> | лкспрожектс и соединители | нет |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | services; | Да |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | domains | Да |
> | домены и темы | нет |
> | eventSubscriptions | нет |
> | extensionTopics | нет |
> | партнернамеспацес | Да |
> | Партнернамеспацес/eventChannels | нет |
> | партнеррегистратионс | Да |
> | партнертопикс | Да |
> | системтопикс | Да |
> | Системтопикс/eventSubscriptions | нет |
> | topics | Да |
> | topicTypes | нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | Да |
> | пространства имен | Да |
> | пространства имен/authorizationrules | нет |
> | пространства имен/дисастеррековериконфигс | нет |
> | пространства имен/eventhubs | нет |
> | пространства имен/eventhubs/authorizationrules | нет |
> | пространства имен/eventhubs/eventhub | нет |
> | пространства имен/нетворкрулесетс | нет |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | features | нет |
> | providers | нет |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | enroll | нет |
> | galleryitems | нет |
> | generateartifactaccessuri | нет |
> | myareas | нет |
> | мяреас/области | нет |
> | мяреас/области/области | нет |
> | мяреас/области/области/галлеритемс | нет |
> | мяреас/области/галлеритемс | нет |
> | мяреас/галлеритемс | нет |
> | регистрация | нет |
> | ресурсов | нет |
> | retrieveresourcesbyid | нет |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | аутоманажедаккаунтс | Да |
> | аутоманажедвмконфигуратионпрофилес | Да |
> | конфигуратионпрофилеассигнментс | нет |
> | guestConfigurationAssignments | нет |
> | software | нет |
> | софтвареупдатепрофиле | нет |
> | софтвареупдатес | нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | hanaInstances | Да |
> | сапмониторс | Да |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | дедикатедхсмс | Да |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | Да |
> | кластеры и приложения | нет |

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | services; | Да |

## <a name="microsofthybridcompute"></a>Microsoft. Хибридкомпуте

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | виртуальных | Да |
> | Компьютеры и расширения | Да |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Диспетчеры | Да |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | components | Да |
> | нетворкскопес | Да |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | jobs | Да |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | diagnosticSettings | нет |
> | diagnosticSettingsCategories | нет |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | апптемплатес | нет |
> | IoTApps | Да |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | График | Да |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | deletedVaults | нет |
> | хсмпулс | Да |
> | vaults | Да |
> | хранилища и accessPolicies | нет |
> | хранилища и Евентгридфилтерс | нет |
> | хранилища и секреты | нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | Да |
> | кластеры/аттачеддатабасеконфигуратионс | нет |
> | кластеры и базы данных | нет |
> | кластеры/базы данных/подключения | нет |
> | кластеры/базы данных/евенсубконнектионс | нет |
> | кластеры/базы данных/принЦипалассигнментс | нет |
> | кластеры/принЦипалассигнментс | нет |
> | кластеры/шаредидентитиес | нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | labaccounts | Да |
> | users | нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | hostingEnvironments | Да |
> | integrationAccounts | Да |
> | интегратионсервицеенвиронментс | Да |
> | Интегратионсервицеенвиронментс/Манажедапис | Да |
> | исолатеденвиронментс | Да |
> | workflows | Да |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | commitmentPlans | Да |
> | webServices | Да |
> | Рабочие области | Да |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | workspaces | Да |
> | рабочие области и расчеты | нет |
> | рабочие области и Евентгридфилтерс | нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Identities | нет |
> | userAssignedIdentities | Да |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | маркетплацерегистратиондефинитионс | нет |
> | регистратионассигнментс | нет |
> | регистратиондефинитионс | нет |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | getEntities | нет |
> | managementGroups | нет |
> | ресурсов | нет |
> | startTenantBackfill | нет |
> | tenantBackfillStatus | нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |
> | Accounts/Евентгридфилтерс | нет |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | offers | нет |
> | offerTypes | нет |
> | Оффертипес и издатели | нет |
> | Оффертипес, издатели и предложения | нет |
> | Оффертипес, издатели, предложения и планы | нет |
> | Оффертипес/Publishers/Offers/Plans/Agreement | нет |
> | Оффертипес/Publishers/предложения, планы и конфигурации | нет |
> | Оффертипес/Publishers/Offers/Plans/Configurations/Импортимаже | нет |
> | privategalleryitems | нет |
> | приватестореклиент | нет |
> | products | нет |
> | publishers | нет |
> | издатели и предложения | нет |
> | издатели, предложения и поправки | нет |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | classicDevServices | Да |
> | updateCommunicationPreference | нет |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | agreements | нет |
> | offertypes | нет |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | mediaservices | Да |
> | mediaservices/Аккаунтфилтерс | нет |
> | mediaservices и активы | нет |
> | mediaservices/активы/Ассетфилтерс | нет |
> | mediaservices/КонтенткэйполиЦиес | нет |
> | mediaservices/Евентгридфилтерс | нет |
> | mediaservices/Лививентоператионс | нет |
> | mediaservices/Лививентс | Да |
> | mediaservices/Лививентс/Ливеаутпутс | нет |
> | mediaservices/Ливеаутпутоператионс | нет |
> | mediaservices/Медиаграфс | нет |
> | mediaservices/Стреаминжендпоинтоператионс | нет |
> | mediaservices/Streamingendpoint | Да |
> | mediaservices/Стреаминглокаторс | нет |
> | mediaservices/СтреамингполиЦиес | нет |
> | mediaservices/преобразования | нет |
> | mediaservices/преобразования/задания | нет |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | аппклустерс | Да |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | ассессментпрожектс | Да |
> | migrateprojects | Да |
> | мовеколлектионс | Да |
> | projects | Да |

## <a name="microsoftmixedreality"></a>Microsoft. Микседреалити

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | холографиксброадкастаккаунтс | Да |
> | обжектундерстандингаккаунтс | Да |
> | ремотерендерингаккаунтс | Да |
> | спатиаланчорсаккаунтс | Да |
> | сурфацереконструктионаккаунтс | Да |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | нетаппаккаунтс | Да |
> | Нетаппаккаунтс/КапаЦитипулс | Да |
> | Нетаппаккаунтс/КапаЦитипулс/тома | Да |
> | Нетаппаккаунтс/КапаЦитипулс/Volumes/Маунттаржетс | Да |
> | Нетаппаккаунтс/КапаЦитипулс/тома/моментальные снимки | Да |
## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | applicationGateways | Да |
> | аппликатионгатевайвебаппликатионфиреваллполиЦиес | Да |
> | applicationSecurityGroups | Да |
> | azureFirewallFqdnTags | нет |
> | azureFirewalls | Да |
> | бастионхостс | Да |
> | bgpServiceCommunities | нет |
> | connections | Да |
> | ddosCustomPolicies | Да |
> | ddosProtectionPlans | Да |
> | dnsOperationStatuses | нет |
> | dnszones | Да |
> | днсзонес/A | нет |
> | днсзонес/AAAA | нет |
> | днсзонес/все | нет |
> | днсзонес/CAA | нет |
> | днсзонес и CNAME | нет |
> | днсзонес/MX | нет |
> | днсзонес/NS | нет |
> | днсзонес/PTR | нет |
> | днсзонес и наборы записей | нет |
> | днсзонес/SOA | нет |
> | днсзонес/SRV | нет |
> | днсзонес/TXT | нет |
> | expressRouteCircuits | Да |
> | expressRouteCrossConnections | Да |
> | експрессраутегатевайс | Да |
> | експрессраутепортс | Да |
> | expressRouteServiceProviders | нет |
> | фиреваллполиЦиес | Да |
> | frontdoors | Да |
> | фронтдурвебаппликатионфиреваллманажедрулесетс | нет |
> | frontdoorWebApplicationFirewallPolicies | Да |
> | getDnsResourceReference | нет |
> | internalNotify | нет |
> | loadBalancers | Да |
> | localNetworkGateways | Да |
> | natGateways | Да |
> | networkIntentPolicies | Да |
> | networkInterfaces | Да |
> | networkProfiles | Да |
> | networkSecurityGroups | Да |
> | networkWatchers | Да |
> | Нетворкватчерс/Коннектионмониторс | Да |
> | Нетворкватчерс/lenses | Да |
> | Нетворкватчерс/Пингмешес | Да |
> | p2sVpnGateways | Да |
> | приватеднсоператионстатусес | нет |
> | приватеднсзонес | Да |
> | Приватеднсзонес/A | нет |
> | Приватеднсзонес/AAAA | нет |
> | Приватеднсзонес/все | нет |
> | Приватеднсзонес и CNAME | нет |
> | Приватеднсзонес/MX | нет |
> | Приватеднсзонес/PTR | нет |
> | Приватеднсзонес/SOA | нет |
> | Приватеднсзонес/SRV | нет |
> | Приватеднсзонес/TXT | нет |
> | Приватеднсзонес/Виртуалнетворклинкс | Да |
> | приватиндпоинтс | Да |
> | privateLinkServices | Да |
> | publicIPAddresses | Да |
> | publicIPPrefixes | Да |
> | routeFilters | Да |
> | routeTables | Да |
> | serviceEndpointPolicies | Да |
> | trafficManagerGeographicHierarchies | нет |
> | trafficmanagerprofiles | Да |
> | trafficmanagerprofiles/тепловые карты | нет |
> | траффикманажерусерметрикскэйс | нет |
> | virtualHubs | Да |
> | virtualNetworkGateways | Да |
> | virtualNetworks | Да |
> | virtualNetworkTaps | Да |
> | virtualWans | Да |
> | vpnGateways | Да |
> | vpnSites | Да |
> | webApplicationFirewallPolicies | Да |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | пространства имен | Да |
> | пространства имен/notificationHubs | Да |

## <a name="microsoftobjectstore"></a>Microsoft. Обжектсторе

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | оснамеспацес | Да |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | хипервситес | Да |
> | импортситес | Да |
> | серверситес | Да |
> | вмвареситес | Да |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | Да |
> | устройства | нет |
> | linkTargets | нет |
> | storageInsightConfigs | нет |
> | workspaces | Да |
> | рабочие области и экспорты | нет |
> | рабочие области и источники данных | нет |
> | рабочие области и linkedServices | нет |
> | рабочие области и Приватиндпоинтконнектионпроксиес | нет |
> | рабочие области и Приватиндпоинтконнектионс | нет |
> | рабочие области и Привателинкресаурцес | нет |
> | рабочие области и запросы | нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managementassociations | нет |
> | managementconfigurations | Да |
> | solutions | Да |
> | узел "Представления" | Да |

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | легаципирингс | нет |
> | пираснс | нет |
> | пиринги | Да |
> | пирингсервицепровидерс | нет |
> | пирингсервицес | Да |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | policyEvents | нет |
> | полициметадата | нет |
> | policyStates | нет |
> | policyTrackedResources | нет |
> | remediations | нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | consoles | нет |
> | dashboards | Да |
> | userSettings | нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | workspaceCollections | Да |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capacities | Да |

## <a name="microsoftprojectbabylon"></a>Microsoft. Прожектбабилон

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | backupProtectedItems | нет |
> | vaults | Да |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | пространства имен | Да |
> | пространства имен/authorizationrules | нет |
> | пространства имен/хибридконнектионс | нет |
> | пространства имен/хибридконнектионс/authorizationrules | нет |
> | пространства имен/вкфрелайс | нет |
> | пространства имен/вкфрелайс/authorizationrules | нет |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | нет |
> | коллекции | Да |
> | коллекции и приложения | нет |
> | Collections/секуритипринЦипалс | нет |
> | темплатеимажес | нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Запросы | Да |
> | ресаурцечанжедетаилс | нет |
> | ресаурцечанжес | нет |
> | ресурсов | нет |
> | ресаурцешистори | нет |
> | subscriptionsStatus | нет |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | availabilityStatuses | нет |
> | childAvailabilityStatuses | нет |
> | childResources | нет |
> | емергингиссуес | нет |
> | события | нет |
> | impactedResources | нет |
> | метаданные | нет |
> | Уведомления | нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | deployments | нет |
> | развертывания и операции | нет |
> | deploymentScripts | Да |
> | deploymentScripts и журналы | нет |
> | ссылки | нет |
> | notifyResourceJobs | нет |
> | providers | нет |
> | resourceGroups | нет |
> | subscriptions | нет |
> | tenants | нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | веб-масштабированием; | Да |
> | saasresources | нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | jobcollections | Да |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | resourceHealthMetadata | нет |
> | searchServices | Да |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | адаптивенетворкхарденингс | нет |
> | advancedThreatProtectionSettings | нет |
> | оповещения | нет |
> | allowedConnections | нет |
> | applicationWhitelistings | нет |
> | ассессментметадата | нет |
> | оценки | нет |
> | аутодисмиссалертсрулес | нет |
> | инструментах автоматизации Composer | Да |
> | AutoProvisioningSettings | нет |
> | Compliances | нет |
> | dataCollectionAgents | нет |
> | девицесекуритиграупс | нет |
> | discoveredSecuritySolutions | нет |
> | externalSecuritySolutions | нет |
> | InformationProtectionPolicies | нет |
> | иотсекуритисолутионс | Да |
> | Иотсекуритисолутионс/Аналитиксмоделс | нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедалертс | нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедрекоммендатионс | нет |
> | jitNetworkAccessPolicies | нет |
> | networkData | нет |
> | политики | нет |
> | pricings | нет |
> | регулаторикомплианцестандардс | нет |
> | Регулаторикомплианцестандардс/Регулаторикомплианцеконтролс | нет |
> | Регулаторикомплианцестандардс/Регулаторикомплианцеконтролс/Регулаторикомплианцеассессментс | нет |
> | securityContacts | нет |
> | securitySolutions | нет |
> | securitySolutionsReferenceData | нет |
> | securityStatuses | нет |
> | securityStatusesSummaries | нет |
> | сервервулнерабилитяссессментс | нет |
> | Параметры | нет |
> | подоценка | нет |
> | задачи | нет |
> | topologies | нет |
> | workspaceSettings | нет |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | diagnosticSettings | нет |
> | diagnosticSettingsCategories | нет |

## <a name="microsoftsecurityinsights"></a>Microsoft. Секуритинсигхтс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | aggregations | нет |
> | alertRules | нет |
> | алертрулетемплатес | нет |
> | закладки | нет |
> | cases | нет |
> | Подключения к компонентам | нет |
> | Сущности | нет |
> | ентитикуериес | нет |
> | оффицеконсентс | нет |
> | Параметры | нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | пространства имен | Да |
> | пространства имен/authorizationrules | нет |
> | пространства имен/дисастеррековериконфигс | нет |
> | пространства имен/евентгридфилтерс | нет |
> | пространства имен/нетворкрулесетс | нет |
> | пространства имен и очереди | нет |
> | пространства имен/очереди/authorizationrules | нет |
> | пространства имен и разделы | нет |
> | пространства имен/разделы/authorizationrules | нет |
> | пространства имен, разделы и подписки | нет |
> | пространства имен, разделы, подписки и правила | нет |
> | premiumMessagingRegions | нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | веб-масштабированием; | Да |
> | clusters | Да |
> | кластеры и приложения | нет |
> | containerGroups | Да |
> | контаинерграупсетс | Да |
> | edgeclusters | Да |
> | еджеклустерс и приложения | нет |
> | networks | Да |
> | secretstores | Да |
> | секретсторес и сертификаты | нет |
> | секретсторес/секреты | нет |
> | volumes. | Да |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | веб-масштабированием; | Да |
> | containerGroups | Да |
> | gateways | Да |
> | networks | Да |
> | секретные коды | Да |
> | volumes. | Да |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | providerRegistrations | нет |
> | Провидеррегистратионс/Ресаурцетиперегистратионс | нет |
> | rollouts | Да |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SignalR | Да |
> | SignalR/Евентгридфилтерс | нет |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SiteRecoveryVault | Да |

## <a name="microsoftsoftwareplan"></a>Microsoft. Софтвареплан

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | хибридусебенефитс | нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | applicationDefinitions | Да |
> | веб-масштабированием; | Да |
> | jitRequests | Да |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managedInstances | Да |
> | Манажединстанцес и базы данных | Да |
> | Манажединстанцес/databases/БаккупшорттермретентионполиЦиес | нет |
> | Манажединстанцес/базы данных, схемы, таблицы, столбцы и Сенситивитилабелс | нет |
> | Манажединстанцес/databases/Вулнерабилитяссессментс | нет |
> | Манажединстанцес/базы данных/Вулнерабилитяссессментс/правила и базовые планы | нет |
> | Манажединстанцес/Енкриптионпротектор | нет |
> | Манажединстанцес и ключи | нет |
> | Манажединстанцес/Ресторабледроппеддатабасес/БаккупшорттермретентионполиЦиес | нет |
> | Манажединстанцес/Вулнерабилитяссессментс | нет |
> | servers | Да |
> | серверы и администраторы | нет |
> | серверы и Коммуникатионлинкс | нет |
> | серверы и базы данных | Да |
> | серверы и Енкриптионпротектор | нет |
> | серверы и Фиреваллрулес | нет |
> | серверы и ключи | нет |
> | серверы и Ресторабледроппеддатабасес | нет |
> | серверы и сервицеобжективес | нет |
> | серверы и Тдецертификатес | нет |
> | виртуалклустерс | нет |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Да |
> | Склвиртуалмачинеграупс/Аваилабилитиграуплистенерс | нет |
> | SqlVirtualMachines | Да |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageAccounts | Да |
> | storageAccounts/Блобсервицес | нет |
> | storageAccounts/Филесервицес | нет |
> | storageAccounts/Куеуесервицес | нет |
> | storageAccounts и службы | нет |
> | storageAccounts/Services/metricDefinitions | нет |
> | storageAccounts/Таблесервицес | нет |
> | usages | нет |

## <a name="microsoftstoragecache"></a>Microsoft. Сторажекаче

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | кэширует | Да |
> | кэши/Сторажетаржетс | нет |
> | усажемоделс | нет |

## <a name="microsoftstoragereplication"></a>Microsoft. Сторажерепликатион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | репликатионграупс | нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | Да |
> | Сторажесинксервицес/Регистередсерверс | нет |
> | Сторажесинксервицес/Синкграупс | нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | нет |
> | Сторажесинксервицес и рабочие процессы | нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | Да |
> | Сторажесинксервицес/Регистередсерверс | нет |
> | Сторажесинксервицес/Синкграупс | нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | нет |
> | Сторажесинксервицес и рабочие процессы | нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | Да |
> | Сторажесинксервицес/Регистередсерверс | нет |
> | Сторажесинксервицес/Синкграупс | нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | нет |
> | Сторажесинксервицес и рабочие процессы | нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managers | Да |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | streamingjobs | Да |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | cancel | нет |
> | CreateSubscription | нет |
> | enable | нет |
> | переименовать | нет |
> | SubscriptionDefinitions | нет |
> | SubscriptionOperations | нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | environments | Да |
> | среды и accessPolicies | нет |
> | среды и классов EventSource | Да |
> | среды и Референцедатасетс | Да |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | дедикатедклауднодес | Да |
> | дедикатедклаудсервицес | Да |
> | virtualMachines | Да |

## <a name="microsoftvnfmanager"></a>Microsoft. Внфманажер

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | устройства | Да |
> | разработчиков | нет |
> | поставщики и номера SKU | нет |
> | внфс | Да |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | apiManagementAccounts | нет |
> | Апиманажементаккаунтс/списков ACL для | нет |
> | Апиманажементаккаунтс/API | нет |
> | Апиманажементаккаунтс/API/списков ACL для | нет |
> | Апиманажементаккаунтс/API/списков ACL для | нет |
> | Апиманажементаккаунтс/API/подключения | нет |
> | Апиманажементаккаунтс/API/Connections/списков ACL для | нет |
> | Апиманажементаккаунтс/API/Локализеддефинитионс | нет |
> | Апиманажементаккаунтс/списков ACL для | нет |
> | Апиманажементаккаунтс и подключения | нет |
> | billingMeters | нет |
> | certificates | Да |
> | connectionGateways | Да |
> | connections | Да |
> | customApis | Да |
> | deletedSites | нет |
> | hostingEnvironments | Да |
> | hostingEnvironments/Евентгридфилтерс | нет |
> | hostingEnvironments/Мултиролепулс | нет |
> | hostingEnvironments/внешнего размещения | нет |
> | publishingUsers | нет |
> | к просмотру фильмов | нет |
> | resourceHealthMetadata | нет |
> | runtimes | нет |
> | serverFarms | Да |
> | serverFarms/Евентгридфилтерс | нет |
> | sites | Да |
> | сайты и конфигурация  | нет |
> | Sites/Евентгридфилтерс | нет |
> | Sites/Хостнамебиндингс | нет |
> | Sites/файл networkconfig | нет |
> | Sites/премиераддонс | Да |
> | сайты и слоты | Да |
> | сайты/слоты/Евентгридфилтерс | нет |
> | сайты/слоты/Хостнамебиндингс | нет |
> | сайты/слоты/файл networkconfig | нет |
> | sourceControls | нет |
> | статикситес | Да |
> | validate | нет |
> | verifyHostingEnvironmentVnet | нет |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | diagnosticSettings | нет |
> | diagnosticSettingsCategories | нет |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | DeviceServices | Да |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | components | нет |
> | componentsSummary | нет |
> | monitorInstances | нет |
> | monitorInstancesSummary | нет |
> | monitors | нет |
> | notificationSettings | нет |

## <a name="next-steps"></a>Дальнейшие действия

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
