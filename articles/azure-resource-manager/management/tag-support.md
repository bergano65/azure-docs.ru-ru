---
title: Поддержка тегов для ресурсов
description: В этой статье описываются типы ресурсов Azure, поддерживающие теги, а также приводятся сведения о всех службах Azure.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: e17e6a996f54d6211f87ec20765d3fde782b7d6d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715601"
---
# <a name="tag-support-for-azure-resources"></a>Поддержка тегов для ресурсов Azure
В этой статье приводятся сведения о поддержке типом ресурса функции [тегов](tag-resources.md). Столбец с подписью **поддерживает теги** . указывает, имеет ли тип ресурса свойство для тега. Столбец с меткой **тег в отчете о затратах** указывает, передает ли этот тип ресурса тег в отчет о затратах. Вы можете просматривать затраты по тегам в [анализе затрат на управление затратами](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) , а также в [счетах на счета и данные о ежедневном использовании в Azure](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

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
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | DomainServices | Да | Да |
> | DomainServices/ауконтаинер | нет | нет |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | supportProviders | нет | нет |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | нет | нет |
> | addsservices | нет | нет |
> | agents | нет | нет |
> | anonymousapiusers | нет | нет |
> | настройка | нет | нет |
> | журналы | нет | нет |
> | reports | нет | нет |
> | servicehealthmetrics | нет | нет |
> | services; | нет | нет |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конфигурации | нет | нет |
> | generateRecommendations | нет | нет |
> | метаданные | нет | нет |
> | к просмотру фильмов | нет | нет |
> | suppressions | нет | нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | actionRules | Да | Да |
> | оповещения | нет | нет |
> | alertsList | нет | нет |
> | алертсметадата | нет | нет |
> | alertsSummary | нет | нет |
> | alertsSummaryList | нет | нет |
> | обратная связь | нет | нет |
> | smartDetectorAlertRules | Да | Да |
> | smartDetectorRuntimeEnvironments | нет | нет |
> | smartGroups | нет | нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | servers | Да | Да |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | reportFeedback | нет | нет |
> | служба | Да | Да |
> | validateServiceName | нет | нет |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конфигуратионсторес | Да | Да |
> | Конфигуратионсторес/Евентгридфилтерс | нет | нет |

## <a name="microsoftappplatform"></a>Microsoft. Аппплатформ

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Spring | Да | Да |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | attestationProviders | нет | нет |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | нет | нет |
> | Псевдонимы | нет | нет |
> | denyAssignments | нет | нет |
> | elevateAccess | нет | нет |
> | финдорфанролеассигнментс | нет | нет |
> | locks | нет | нет |
> | разрешения | нет | нет |
> | policyAssignments | нет | нет |
> | policyDefinitions | нет | нет |
> | policySetDefinitions | нет | нет |
> | providerOperations | нет | нет |
> | roleAssignments | нет | нет |
> | ролеассигнментсусажеметрикс | нет | нет |
> | roleDefinitions | нет | нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Да | Да |
> | automationAccounts и конфигурации | Да | Да |
> | automationAccounts и задания | нет | нет |
> | automationAccounts и модули Runbook | Да | Да |
> | automationAccounts/Софтвареупдатеконфигуратионс | нет | нет |
> | automationAccounts/веб-перехватчики | нет | нет |

## <a name="microsoftazconfig"></a>Microsoft. Азконфиг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конфигуратионсторес | Да | Да |
> | Конфигуратионсторес/Евентгридфилтерс | нет | нет |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | environments | нет | нет |
> | среды и учетные записи | нет | нет |
> | среды, учетные записи и пространства имен | нет | нет |
> | среды, учетные записи, пространства имен и конфигурации | нет | нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Да | нет |
> | b2ctenants | нет | нет |

## <a name="microsoftazuredata"></a>Microsoft. Азуредата

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хибриддатаманажерс | Да | Да |
> | постгресинстанцес | Да | Да |
> | склбигдатаклустерс | Да | Да |
> | склинстанцес | Да | Да |
> | склсерверрегистратионс | Да | Да |
> | Склсерверрегистратионс и sqlServer | нет | нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | registrations | Да | Да |
> | регистрации и Кустомерсубскриптионс | нет | нет |
> | регистрации и продукты | нет | нет |
> | верификатионкэйс | нет | нет |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Да | Да |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | billingAccounts | нет | нет |
> | Биллингаккаунтс и соглашения | нет | нет |
> | Биллингаккаунтс/Биллингпермиссионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингпермиссионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингролеассигнментс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингроледефинитионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингсубскриптионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Креатебиллингролеассигнмент | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/клиенты | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/инструкции | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/счета | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/счета/прайс-лист | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингпермиссионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингролеассигнментс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингроледефинитионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингсубскриптионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Креатебиллингролеассигнмент | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Инитиатетрансфер | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products/перемещение | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products/Упдатеауторенев | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/транзакции | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/передача | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Патчоператионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Пайментмесодс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/политики | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/прайс-лист | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Прицешитдовнлоадоператионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Products | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/транзакции | нет | нет |
> | Биллингаккаунтс/Биллингролеассигнментс | нет | нет |
> | Биллингаккаунтс/Биллингроледефинитионс | нет | нет |
> | Биллингаккаунтс/Биллингсубскриптионс | нет | нет |
> | Биллингаккаунтс/Биллингсубскриптионс/счета | нет | нет |
> | Биллингаккаунтс/Креатебиллингролеассигнмент | нет | нет |
> | Биллингаккаунтс/Креатеинвоицесектионоператионс | нет | нет |
> | Биллингаккаунтс и клиенты | нет | нет |
> | Биллингаккаунтс/Customers/Биллингпермиссионс | нет | нет |
> | Биллингаккаунтс/Customers/Биллингсубскриптионс | нет | нет |
> | Биллингаккаунтс/Customers/Инитиатетрансфер | нет | нет |
> | Биллингаккаунтс, клиенты и политики | нет | нет |
> | Биллингаккаунтс/Customers/Products | нет | нет |
> | Биллингаккаунтс/Customers/Transactions | нет | нет |
> | Биллингаккаунтс/Customers/Transfers | нет | нет |
> | Биллингаккаунтс и отделы | нет | нет |
> | Биллингаккаунтс/Енроллментаккаунтс | нет | нет |
> | Биллингаккаунтс/счета | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионмовеоператионс | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионс | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионс/перемещение | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/повышение прав | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Инитиатетрансфер | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Патчоператионс | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Продуктмовеоператионс | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Products | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/продукты/перемещение | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Products/Упдатеауторенев | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/транзакции | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/передача | нет | нет |
> | Биллингаккаунтс/Линеофкредит | нет | нет |
> | Биллингаккаунтс/Патчоператионс | нет | нет |
> | Биллингаккаунтс/Пайментмесодс | нет | нет |
> | Биллингаккаунтс и продукты | нет | нет |
> | Биллингаккаунтс/транзакции | нет | нет |
> | billingPeriods | нет | нет |
> | биллингпермиссионс | нет | нет |
> | billingProperty | нет | нет |
> | биллингролеассигнментс | нет | нет |
> | биллингроледефинитионс | нет | нет |
> | креатебиллингролеассигнмент | нет | нет |
> | departments | нет | нет |
> | enrollmentAccounts | нет | нет |
> | invoices | нет | нет |
> | transfers | нет | нет |
> | передачи/Акцепттрансфер | нет | нет |
> | передачи/Деклинетрансфер | нет | нет |
> | передачи/значение operationstatus | нет | нет |
> | передачи/Валидатетрансфер | нет | нет |
> | валидатеаддресс | нет | нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | mapApis | Да | Да |
> | updateCommunicationPreference | нет | нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | блоккчаинмемберс | Да | Да |
> | кордамемберс | Да | Да |
> | наблюдателей | Да | Да |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | нет | нет |
> | Блуепринтассигнментс/Ассигнментоператионс | нет | нет |
> | Блуепринтассигнментс/операции | нет | нет |
> | blueprints | нет | нет |
> | схемы и артефакты | нет | нет |
> | проекты и версии | нет | нет |
> | схемы, версии и артефакты | нет | нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | botServices | Да | Да |
> | Ботсервицес и каналы | нет | нет |
> | Ботсервицес и подключения | нет | нет |
> | языки | нет | нет |
> | шаблоны | нет | нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Redis | Да | Да |
> | RedisConfigDefinition | нет | нет |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | appliedReservations | нет | нет |
> | аутокуотаинкреасе | нет | нет |
> | калкулатиксчанже | нет | нет |
> | calculatePrice | нет | нет |
> | калкулатепурчасеприце | нет | нет |
> | catalogs | нет | нет |
> | commercialReservationOrders | нет | нет |
> | обмен валюты | нет | нет |
> | плацепурчасеордер | нет | нет |
> | reservationOrders | нет | нет |
> | Ресерватионордерс/Калкулатерефунд | нет | нет |
> | Ресерватионордерс/Merge | нет | нет |
> | Ресерватионордерс/резервирования | нет | нет |
> | Ресерватионордерс/резервирования/редакции | нет | нет |
> | Ресерватионордерс/Return | нет | нет |
> | Ресерватионордерс/Split | нет | нет |
> | Ресерватионордерс/swap | нет | нет |
> | reservations | нет | нет |
> | ресаурцепровидерс | нет | нет |
> | ресурсов | нет | нет |
> | validateReservationOrder | нет | нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | кднвебаппликатионфиреваллманажедрулесетс | нет | нет |
> | кднвебаппликатионфиреваллполиЦиес | Да | Да |
> | edgenodes | нет | нет |
> | профили | Да | Да |
> | профили и конечные точки | Да | Да |
> | профили, конечные точки/кустомдомаинс | нет | нет |
> | профили, конечные точки и источники | нет | нет |
> | validateProbe | нет | нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Да | Да |
> | Certificateorder и сертификаты | нет | нет |
> | validateCertificateRegistrationInformation | нет | нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capabilities | нет | нет |
> | domainNames | нет | нет |
> | имя_домена/возможности | нет | нет |
> | имя_домена/Интерналлоадбаланцерс | нет | нет |
> | имя_домена/Сервицецертификатес | нет | нет |
> | имя_домена/слоты | нет | нет |
> | Доменные имя, слоты и роли | нет | нет |
> | имя_домена/слоты/роли/metricDefinitions | нет | нет |
> | имя_домена/слоты/роли/метрики | нет | нет |
> | moveSubscriptionResources | нет | нет |
> | operatingSystemFamilies | нет | нет |
> | operatingSystems | нет | нет |
> | quotas | нет | нет |
> | resourceTypes | нет | нет |
> | validateSubscriptionMoveAvailability | нет | нет |
> | virtualMachines | нет | нет |
> | virtualMachines/diagnosticSettings | нет | нет |
> | virtualMachines/metricDefinitions | нет | нет |
> | virtualMachines/метрики | нет | нет |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | нет | нет |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capabilities | нет | нет |
> | expressRouteCrossConnections | нет | нет |
> | Експрессраутекроссконнектионс и пиринг | нет | нет |
> | gatewaySupportedDevices | нет | нет |
> | networkSecurityGroups | нет | нет |
> | quotas | нет | нет |
> | reservedIps | нет | нет |
> | virtualNetworks | нет | нет |
> | virtualNetworks/Ремотевиртуалнетворкпирингпроксиес | нет | нет |
> | virtualNetworks/Виртуалнетворкпирингс | нет | нет |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capabilities | нет | нет |
> | disks | нет | нет |
> | images | нет | нет |
> | osImages | нет | нет |
> | osPlatformImages | нет | нет |
> | publicImages | нет | нет |
> | quotas | нет | нет |
> | storageAccounts | нет | нет |
> | storageAccounts/Блобсервицес | нет | нет |
> | storageAccounts/Филесервицес | нет | нет |
> | storageAccounts/metricDefinitions | нет | нет |
> | storageAccounts/метрики | нет | нет |
> | storageAccounts/Куеуесервицес | нет | нет |
> | storageAccounts и службы | нет | нет |
> | storageAccounts/Services/diagnosticSettings | нет | нет |
> | storageAccounts/Services/metricDefinitions | нет | нет |
> | storageAccounts/Services/метрики | нет | нет |
> | storageAccounts/Таблесервицес | нет | нет |
> | storageAccounts/Вмимажес | нет | нет |
> | vmImages | нет | нет |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | RateCard | нет | нет |
> | UsageAggregates | нет | нет |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Да | Да |
> | дискенкриптионсетс | Да | Да |
> | disks | Да | Да |
> | galleries | Да | Да |
> | коллекции и приложения | нет | нет |
> | коллекции, приложения и версии | нет | нет |
> | коллекции и изображения | нет | нет |
> | коллекции, изображения и версии | нет | нет |
> | хостграупс | Да | Да |
> | Хостграупс и узлы | Да | Да |
> | images | Да | Да |
> | проксимитиплацементграупс | Да | Да |
> | restorePointCollections | Да | Да |
> | Ресторепоинтколлектионс/Ресторепоинтс | нет | нет |
> | sharedVMImages | Да | Да |
> | Шаредвмимажес и версии | нет | нет |
> | snapshots | Да | Да |
> | virtualMachines | Да | Да |
> | virtualMachines и расширения | Да | Да |
> | virtualMachines/metricDefinitions | нет | нет |
> | virtualMachineScaleSets | Да | Да |
> | virtualMachineScaleSets и расширения | нет | нет |
> | virtualMachineScaleSets/networkInterfaces | нет | нет |
> | virtualMachineScaleSets/publicIPAddresses | нет | нет |
> | virtualMachineScaleSets/virtualMachines | нет | нет |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | нет | нет |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | нет | нет |
> | сведения о балансе. | нет | нет |
> | сведения о бюджете; | нет | нет |
> | Расходы | нет | нет |
> | CostTags | нет | нет |
> | credits | нет | нет |
> | события | нет | нет |
> | Прогнозы | нет | нет |
> | lots | нет | нет |
> | Marketplace | нет | нет |
> | Pricesheets | нет | нет |
> | products | нет | нет |
> | ReservationDetails | нет | нет |
> | ReservationRecommendations | нет | нет |
> | ReservationSummaries | нет | нет |
> | ReservationTransactions | нет | нет |
> | Теги | нет | нет |
> | tenants | нет | нет |
> | Термины | нет | нет |
> | UsageDetails | нет | нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | containerGroups | Да | Да |
> | serviceAssociationLinks | нет | нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | registries | Да | Да |
> | реестры и сборки | нет | нет |
> | реестры/сборки/Отмена | нет | нет |
> | реестры/сборки/Жетлоглинк | нет | нет |
> | реестры и Буилдтаскс | Да | Да |
> | реестры, Буилдтаскс и шаги | нет | нет |
> | реестры и Евентгридфилтерс | нет | нет |
> | реестры и Женератекредентиалс | нет | нет |
> | реестры и Жетбуилдсаурцеуплоадурл | нет | нет |
> | реестры и учетные данные | нет | нет |
> | реестры и Импортимаже | нет | нет |
> | реестры и Куеуебуилд | нет | нет |
> | реестры и Реженератекредентиал | нет | нет |
> | реестры и Реженератекредентиалс | нет | нет |
> | реестры и репликации | Да | Да |
> | реестры и запуски | нет | нет |
> | реестры/запуски/Отмена | нет | нет |
> | реестры и Счедулерун | нет | нет |
> | реестры и Скопемапс | нет | нет |
> | реестры и Таскрунс | Да | Да |
> | реестры и задачи | Да | Да |
> | реестры и маркеры | нет | нет |
> | реестры и УпдатеполиЦиес | нет | нет |
> | реестры и веб-перехватчики | Да | Да |
> | реестры/веб-перехватчики/Жеткаллбаккконфиг | нет | нет |
> | реестры, веб-перехватчики и проверка связи | нет | нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | containerServices | Да | Да |
> | managedClusters | Да | Да |
> | опеншифтманажедклустерс | Да | Да |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | видны узлы | нет | нет |
> | BillingAccounts | нет | нет |
> | сведения о бюджете; | нет | нет |
> | клаудконнекторс | нет | нет |
> | Соединители | Да | Да |
> | Departments | нет | нет |
> | Измерения | нет | нет |
> | EnrollmentAccounts | нет | нет |
> | Экспорт | нет | нет |
> | екстерналбиллингаккаунтс | нет | нет |
> | Екстерналбиллингаккаунтс и оповещения | нет | нет |
> | Екстерналбиллингаккаунтс и измерения | нет | нет |
> | Екстерналбиллингаккаунтс/прогноз | нет | нет |
> | Екстерналбиллингаккаунтс/запрос | нет | нет |
> | екстерналсубскриптионс | нет | нет |
> | Екстерналсубскриптионс и оповещения | нет | нет |
> | Екстерналсубскриптионс и измерения | нет | нет |
> | Екстерналсубскриптионс/прогноз | нет | нет |
> | Екстерналсубскриптионс/запрос | нет | нет |
> | Прогноз | нет | нет |
> | Запрос | нет | нет |
> | регистрация | нет | нет |
> | Reportconfigs | нет | нет |
> | Отчеты | нет | нет |
> | Настройки | нет | нет |
> | шовбаккрулес | нет | нет |
> | Представления | нет | нет |

## <a name="microsoftcustomerlockbox"></a>Microsoft. Кустомерлоккбокс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | requests | нет | нет |

## <a name="microsoftcustomproviders"></a>Microsoft. Кустомпровидерс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | сопоставления | нет | нет |
> | ресаурцепровидерс | Да | Да |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | jobs | Да | Да |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Да | Да |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | workspaces | Да | нет |
> | рабочие области и Дбворкспацес | нет | нет |
> | рабочие области и storageEncryption | нет | нет |
> | рабочие области и Виртуалнетворкпирингс | нет | нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | catalogs | Да | Да |
> | каталоги | Да | Да |
> | каталоги данных и источники данных | нет | нет |
> | каталоги данных, источники данных и проверки | нет | нет |
> | каталоги данных/DataSources/scans/DataSets | нет | нет |
> | каталоги данных, источники данных, проверки и триггеры | нет | нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | dataFactories | Да | нет |
> | Коэффициенты и diagnosticSettings | нет | нет |
> | Коэффициенты и metricDefinitions | нет | нет |
> | dataFactorySchema | нет | нет |
> | factories | Да | нет |
> | фабрики/Интегратионрунтимес | нет | нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | Accounts/Даталакестореаккаунтс | нет | нет |
> | Accounts/storageAccounts | нет | нет |
> | Accounts/storageAccounts/Containers | нет | нет |
> | Accounts/Трансфераналитиксунитс | нет | нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | Accounts/Евентгридфилтерс | нет | нет |
> | Accounts/Фиреваллрулес | нет | нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | нет | нет |
> | службы и проекты | нет | нет |

## <a name="microsoftdatashare"></a>Общая папка Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | учетные записи и общие папки | нет | нет |
> | учетные записи, общие ресурсы и наборы данных | нет | нет |
> | учетные записи, общие ресурсы и приглашения | нет | нет |
> | Accounts/shares/провидершаресубскриптионс | нет | нет |
> | Accounts/shares/Синчронизатионсеттингс | нет | нет |
> | Accounts/шаресубскриптионс | нет | нет |
> | Accounts/шаресубскриптионс/Консумерсаурцедатасетс | нет | нет |
> | Accounts/шаресубскриптионс/датасетмаппингс | нет | нет |
> | учетные записи/шаресубскриптионс/триггеры | нет | нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | servers | Да | Да |
> | серверы и помощники | нет | нет |
> | серверы и ключи | нет | нет |
> | серверы и Приватиндпоинтконнектионпроксиес | нет | нет |
> | серверы и Приватиндпоинтконнектионс | нет | нет |
> | серверы и Привателинкресаурцес | нет | нет |
> | серверы и Куеритекстс | нет | нет |
> | серверы и Рековераблесерверс | нет | нет |
> | серверы и Топкуеристатистикс | нет | нет |
> | серверы и Виртуалнетворкрулес | нет | нет |
> | серверы и Ваитстатистикс | нет | нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | servers | Да | Да |
> | серверы и помощники | нет | нет |
> | серверы и ключи | нет | нет |
> | серверы и Приватиндпоинтконнектионпроксиес | нет | нет |
> | серверы и Приватиндпоинтконнектионс | нет | нет |
> | серверы и Привателинкресаурцес | нет | нет |
> | серверы и Куеритекстс | нет | нет |
> | серверы и Рековераблесерверс | нет | нет |
> | серверы и Топкуеристатистикс | нет | нет |
> | серверы и Виртуалнетворкрулес | нет | нет |
> | серверы и Ваитстатистикс | нет | нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | серверграупс | Да | Да |
> | servers | Да | Да |
> | серверы и помощники | нет | нет |
> | серверы и ключи | нет | нет |
> | серверы и Приватиндпоинтконнектионпроксиес | нет | нет |
> | серверы и Приватиндпоинтконнектионс | нет | нет |
> | серверы и Привателинкресаурцес | нет | нет |
> | серверы и Куеритекстс | нет | нет |
> | серверы и Рековераблесерверс | нет | нет |
> | серверы и Топкуеристатистикс | нет | нет |
> | серверы и Виртуалнетворкрулес | нет | нет |
> | серверы и Ваитстатистикс | нет | нет |
> | serversv2 | Да | Да |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | артифактсаурцес | Да | Да |
> | rollouts | Да | Да |
> | сервицетопологиес | Да | Да |
> | Сервицетопологиес и службы | Да | Да |
> | Сервицетопологиес/Services/Сервицеунитс | Да | Да |
> | steps | Да | Да |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. Десктопвиртуализатион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | аппликатионграупс | Да | Да |
> | аппликатионграупс и приложения | нет | нет |
> | аппликатионграупс и настольные системы | нет | нет |
> | аппликатионграупс/стартменуитемс | нет | нет |
> | хостпулс | Да | Да |
> | хостпулс/сессионхостс | нет | нет |
> | хостпулс/сессионхостс/усерсессионс | нет | нет |
> | хостпулс/усерсессионс | нет | нет |
> | workspaces | Да | Да |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | еластикпулс | Да | Да |
> | Еластикпулс/Иосубтенантс | Да | Да |
> | Еластикпулс/Иосубтенантс/securitySettings | нет | нет |
> | IotHubs | Да | Да |
> | IotHubs/Евентгридфилтерс | нет | нет |
> | IotHubs/securitySettings | нет | нет |
> | ProvisioningServices | Да | Да |
> | usages | нет | нет |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конвейеров | Да | Да |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | controllers | Да | Да |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | labcenters | Да | Да |
> | labs | Да | Да |
> | лаборатории и среды | Да | Да |
> | Labs и Сервицеруннерс | Да | Да |
> | Labs и virtualMachines | Да | Да |
> | schedules | Да | Да |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | нет | нет |
> | databaseAccounts | Да | Да |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | domains | Да | Да |
> | домены/Домаиновнершипидентифиерс | нет | нет |
> | generateSsoRequest | нет | нет |
> | topLevelDomains | нет | нет |
> | validateDomainRegistrationInformation | нет | нет |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | lcsprojects | нет | нет |
> | лкспрожектс/клауддеплойментс | нет | нет |
> | лкспрожектс и соединители | нет | нет |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | Да | Да |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | domains | Да | Да |
> | домены и темы | нет | нет |
> | eventSubscriptions | нет | нет |
> | extensionTopics | нет | нет |
> | партнернамеспацес | Да | Да |
> | Партнернамеспацес/eventChannels | нет | нет |
> | партнеррегистратионс | Да | Да |
> | партнертопикс | Да | Да |
> | системтопикс | Да | Да |
> | Системтопикс/eventSubscriptions | нет | нет |
> | topics | Да | Да |
> | topicTypes | нет | нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | пространства имен | Да | Да |
> | пространства имен/authorizationrules | нет | нет |
> | пространства имен/дисастеррековериконфигс | нет | нет |
> | пространства имен/eventhubs | нет | нет |
> | пространства имен/eventhubs/authorizationrules | нет | нет |
> | пространства имен/eventhubs/eventhub | нет | нет |
> | пространства имен/нетворкрулесетс | нет | нет |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | features | нет | нет |
> | providers | нет | нет |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | enroll | нет | нет |
> | galleryitems | нет | нет |
> | generateartifactaccessuri | нет | нет |
> | myareas | нет | нет |
> | мяреас/области | нет | нет |
> | мяреас/области/области | нет | нет |
> | мяреас/области/области/галлеритемс | нет | нет |
> | мяреас/области/галлеритемс | нет | нет |
> | мяреас/галлеритемс | нет | нет |
> | регистрация | нет | нет |
> | ресурсов | нет | нет |
> | retrieveresourcesbyid | нет | нет |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | аутоманажедаккаунтс | Да | Да |
> | аутоманажедвмконфигуратионпрофилес | Да | Да |
> | конфигуратионпрофилеассигнментс | нет | нет |
> | guestConfigurationAssignments | нет | нет |
> | software | нет | нет |
> | софтвареупдатепрофиле | нет | нет |
> | софтвареупдатес | нет | нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Да | Да |
> | сапмониторс | Да | Да |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | дедикатедхсмс | Да | Да |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | кластеры и приложения | нет | нет |

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | Да | Да |

## <a name="microsofthybridcompute"></a>Microsoft. Хибридкомпуте

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | виртуальных | Да | Да |
> | Компьютеры и расширения | Да | Да |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Диспетчеры | Да | Да |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | components | Да | Да |
> | нетворкскопес | Да | Да |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | jobs | Да | Да |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | нет | нет |
> | diagnosticSettingsCategories | нет | нет |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | апптемплатес | нет | нет |
> | IoTApps | Да | Да |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | График | Да | Да |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | deletedVaults | нет | нет |
> | хсмпулс | Да | Да |
> | vaults | Да | Да |
> | хранилища и accessPolicies | нет | нет |
> | хранилища и Евентгридфилтерс | нет | нет |
> | хранилища и секреты | нет | нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | кластеры/аттачеддатабасеконфигуратионс | нет | нет |
> | кластеры и базы данных | нет | нет |
> | кластеры/базы данных/подключения | нет | нет |
> | кластеры/базы данных/евенсубконнектионс | нет | нет |
> | кластеры/базы данных/принЦипалассигнментс | нет | нет |
> | кластеры/принЦипалассигнментс | нет | нет |
> | кластеры/шаредидентитиес | нет | нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | labaccounts | Да | Да |
> | users | нет | нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Да | Да |
> | integrationAccounts | Да | Да |
> | интегратионсервицеенвиронментс | Да | Да |
> | Интегратионсервицеенвиронментс/Манажедапис | Да | Да |
> | исолатеденвиронментс | Да | Да |
> | workflows | Да | Да |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Да | Да |
> | webServices | Да | Да |
> | Рабочие области | Да | Да |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | workspaces | Да | Да |
> | рабочие области и расчеты | нет | нет |
> | рабочие области и Евентгридфилтерс | нет | нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Identities | нет | нет |
> | userAssignedIdentities | Да | Да |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | маркетплацерегистратиондефинитионс | нет | нет |
> | регистратионассигнментс | нет | нет |
> | регистратиондефинитионс | нет | нет |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | getEntities | нет | нет |
> | managementGroups | нет | нет |
> | ресурсов | нет | нет |
> | startTenantBackfill | нет | нет |
> | tenantBackfillStatus | нет | нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | Accounts/Евентгридфилтерс | нет | нет |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | offers | нет | нет |
> | offerTypes | нет | нет |
> | Оффертипес и издатели | нет | нет |
> | Оффертипес, издатели и предложения | нет | нет |
> | Оффертипес, издатели, предложения и планы | нет | нет |
> | Оффертипес/Publishers/Offers/Plans/Agreement | нет | нет |
> | Оффертипес/Publishers/предложения, планы и конфигурации | нет | нет |
> | Оффертипес/Publishers/Offers/Plans/Configurations/Импортимаже | нет | нет |
> | privategalleryitems | нет | нет |
> | приватестореклиент | нет | нет |
> | products | нет | нет |
> | publishers | нет | нет |
> | издатели и предложения | нет | нет |
> | издатели, предложения и поправки | нет | нет |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Да | Да |
> | updateCommunicationPreference | нет | нет |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | agreements | нет | нет |
> | offertypes | нет | нет |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | mediaservices | Да | Да |
> | mediaservices/Аккаунтфилтерс | нет | нет |
> | mediaservices и активы | нет | нет |
> | mediaservices/активы/Ассетфилтерс | нет | нет |
> | mediaservices/КонтенткэйполиЦиес | нет | нет |
> | mediaservices/Евентгридфилтерс | нет | нет |
> | mediaservices/Лививентоператионс | нет | нет |
> | mediaservices/Лививентс | Да | Да |
> | mediaservices/Лививентс/Ливеаутпутс | нет | нет |
> | mediaservices/Ливеаутпутоператионс | нет | нет |
> | mediaservices/Медиаграфс | нет | нет |
> | mediaservices/Стреаминжендпоинтоператионс | нет | нет |
> | mediaservices/Streamingendpoint | Да | Да |
> | mediaservices/Стреаминглокаторс | нет | нет |
> | mediaservices/СтреамингполиЦиес | нет | нет |
> | mediaservices/преобразования | нет | нет |
> | mediaservices/преобразования/задания | нет | нет |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | аппклустерс | Да | Да |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | ассессментпрожектс | Да | Да |
> | migrateprojects | Да | Да |
> | мовеколлектионс | Да | Да |
> | projects | Да | Да |

## <a name="microsoftmixedreality"></a>Microsoft. Микседреалити

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | холографиксброадкастаккаунтс | Да | Да |
> | обжектундерстандингаккаунтс | Да | Да |
> | ремотерендерингаккаунтс | Да | Да |
> | спатиаланчорсаккаунтс | Да | Да |
> | сурфацереконструктионаккаунтс | Да | Да |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | нетаппаккаунтс | Да | нет |
> | Нетаппаккаунтс/КапаЦитипулс | Да | нет |
> | Нетаппаккаунтс/КапаЦитипулс/тома | Да | нет |
> | Нетаппаккаунтс/КапаЦитипулс/Volumes/Маунттаржетс | Да | нет |
> | Нетаппаккаунтс/КапаЦитипулс/тома/моментальные снимки | Да | нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Да | Да |
> | аппликатионгатевайвебаппликатионфиреваллполиЦиес | Да | Да |
> | applicationSecurityGroups | Да | Да |
> | azureFirewallFqdnTags | нет | нет |
> | azureFirewalls | Да | нет |
> | бастионхостс | Да | Да |
> | bgpServiceCommunities | нет | нет |
> | connections | Да | Да |
> | ddosCustomPolicies | Да | Да |
> | ddosProtectionPlans | Да | Да |
> | dnsOperationStatuses | нет | нет |
> | dnszones | Да | Да |
> | днсзонес/A | нет | нет |
> | днсзонес/AAAA | нет | нет |
> | днсзонес/все | нет | нет |
> | днсзонес/CAA | нет | нет |
> | днсзонес и CNAME | нет | нет |
> | днсзонес/MX | нет | нет |
> | днсзонес/NS | нет | нет |
> | днсзонес/PTR | нет | нет |
> | днсзонес и наборы записей | нет | нет |
> | днсзонес/SOA | нет | нет |
> | днсзонес/SRV | нет | нет |
> | днсзонес/TXT | нет | нет |
> | expressRouteCircuits | Да | Да |
> | expressRouteCrossConnections | Да | Да |
> | експрессраутегатевайс | Да | Да |
> | експрессраутепортс | Да | Да |
> | expressRouteServiceProviders | нет | нет |
> | фиреваллполиЦиес | Да | Да |
> | frontdoors | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | Да |
> | фронтдурвебаппликатионфиреваллманажедрулесетс | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | нет |
> | frontdoorWebApplicationFirewallPolicies | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | Да |
> | getDnsResourceReference | нет | нет |
> | internalNotify | нет | нет |
> | loadBalancers | Да | нет |
> | localNetworkGateways | Да | Да |
> | natGateways | Да | Да |
> | networkIntentPolicies | Да | Да |
> | networkInterfaces | Да | Да |
> | networkProfiles | Да | Да |
> | networkSecurityGroups | Да | Да |
> | networkWatchers | Да | нет |
> | Нетворкватчерс/Коннектионмониторс | Да | нет |
> | Нетворкватчерс/lenses | Да | нет |
> | Нетворкватчерс/Пингмешес | Да | нет |
> | p2sVpnGateways | Да | Да |
> | приватеднсоператионстатусес | нет | нет |
> | приватеднсзонес | Да | Да |
> | Приватеднсзонес/A | нет | нет |
> | Приватеднсзонес/AAAA | нет | нет |
> | Приватеднсзонес/все | нет | нет |
> | Приватеднсзонес и CNAME | нет | нет |
> | Приватеднсзонес/MX | нет | нет |
> | Приватеднсзонес/PTR | нет | нет |
> | Приватеднсзонес/SOA | нет | нет |
> | Приватеднсзонес/SRV | нет | нет |
> | Приватеднсзонес/TXT | нет | нет |
> | Приватеднсзонес/Виртуалнетворклинкс | Да | Да |
> | приватиндпоинтс | Да | Да |
> | privateLinkServices | Да | Да |
> | publicIPAddresses | Да | Да |
> | publicIPPrefixes | Да | Да |
> | routeFilters | Да | Да |
> | routeTables | Да | Да |
> | serviceEndpointPolicies | Да | Да |
> | trafficManagerGeographicHierarchies | нет | нет |
> | trafficmanagerprofiles | Да | Да |
> | trafficmanagerprofiles/heatMaps | нет | нет |
> | траффикманажерусерметрикскэйс | нет | нет |
> | virtualHubs | Да | Да |
> | virtualNetworkGateways | Да | Да |
> | virtualNetworks | Да | Да |
> | virtualNetworkTaps | Да | Да |
> | virtualWans | Да | Да |
> | vpnGateways | Да | нет |
> | vpnSites | Да | Да |
> | webApplicationFirewallPolicies | Да | Да |

<a id="frontdoor" />

> [!NOTE]
> Для службы "Передняя дверца Azure" можно применять теги при создании ресурса, но обновление или добавление тегов в настоящее время не поддерживается.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да | нет |
> | пространства имен/notificationHubs | Да | нет |

## <a name="microsoftobjectstore"></a>Microsoft. Обжектсторе

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | оснамеспацес | Да | Да |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хипервситес | Да | Да |
> | импортситес | Да | Да |
> | серверситес | Да | Да |
> | вмвареситес | Да | Да |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | устройства | нет | нет |
> | linkTargets | нет | нет |
> | storageInsightConfigs | нет | нет |
> | workspaces | Да | Да |
> | рабочие области и экспорты | нет | нет |
> | рабочие области и источники данных | нет | нет |
> | рабочие области и linkedServices | нет | нет |
> | рабочие области и Приватиндпоинтконнектионпроксиес | нет | нет |
> | рабочие области и Приватиндпоинтконнектионс | нет | нет |
> | рабочие области и Привателинкресаурцес | нет | нет |
> | рабочие области и запросы | нет | нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managementassociations | нет | нет |
> | managementconfigurations | Да | Да |
> | solutions | Да | Да |
> | узел "Представления" | Да | Да |

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | легаципирингс | нет | нет |
> | пираснс | нет | нет |
> | пиринги | Да | Да |
> | пирингсервицепровидерс | нет | нет |
> | пирингсервицес | Да | Да |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | policyEvents | нет | нет |
> | полициметадата | нет | нет |
> | policyStates | нет | нет |
> | policyTrackedResources | нет | нет |
> | remediations | нет | нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | consoles | нет | нет |
> | dashboards | Да | Да |
> | userSettings | нет | нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Да | Да |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capacities | Да | Да |

## <a name="microsoftprojectbabylon"></a>Microsoft. Прожектбабилон

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | нет | нет |
> | vaults | Да | Да |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да | Да |
> | пространства имен/authorizationrules | нет | нет |
> | пространства имен/хибридконнектионс | нет | нет |
> | пространства имен/хибридконнектионс/authorizationrules | нет | нет |
> | пространства имен/вкфрелайс | нет | нет |
> | пространства имен/вкфрелайс/authorizationrules | нет | нет |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | нет | нет |
> | коллекции | Да | Да |
> | коллекции и приложения | нет | нет |
> | Collections/секуритипринЦипалс | нет | нет |
> | темплатеимажес | нет | нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Запросы | Да | Да |
> | ресаурцечанжедетаилс | нет | нет |
> | ресаурцечанжес | нет | нет |
> | ресурсов | нет | нет |
> | ресаурцешистори | нет | нет |
> | subscriptionsStatus | нет | нет |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | нет | нет |
> | childAvailabilityStatuses | нет | нет |
> | childResources | нет | нет |
> | емергингиссуес | нет | нет |
> | события | нет | нет |
> | impactedResources | нет | нет |
> | метаданные | нет | нет |
> | Уведомления | нет | нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | deployments | Да | нет |
> | развертывания и операции | нет | нет |
> | deploymentScripts | Да | Да |
> | deploymentScripts и журналы | нет | нет |
> | ссылки | нет | нет |
> | notifyResourceJobs | нет | нет |
> | providers | нет | нет |
> | resourceGroups | Да | нет |
> | subscriptions | нет | нет |
> | tenants | нет | нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | веб-масштабированием; | Да | Да |
> | saasresources | нет | нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | jobcollections | Да | Да |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | нет | нет |
> | searchServices | Да | Да |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | адаптивенетворкхарденингс | нет | нет |
> | advancedThreatProtectionSettings | нет | нет |
> | оповещения | нет | нет |
> | allowedConnections | нет | нет |
> | applicationWhitelistings | нет | нет |
> | ассессментметадата | нет | нет |
> | оценки | нет | нет |
> | аутодисмиссалертсрулес | нет | нет |
> | инструментах автоматизации Composer | Да | Да |
> | AutoProvisioningSettings | нет | нет |
> | Compliances | нет | нет |
> | dataCollectionAgents | нет | нет |
> | девицесекуритиграупс | нет | нет |
> | discoveredSecuritySolutions | нет | нет |
> | externalSecuritySolutions | нет | нет |
> | InformationProtectionPolicies | нет | нет |
> | иотсекуритисолутионс | Да | Да |
> | Иотсекуритисолутионс/Аналитиксмоделс | нет | нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедалертс | нет | нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедрекоммендатионс | нет | нет |
> | jitNetworkAccessPolicies | нет | нет |
> | networkData | нет | нет |
> | политики | нет | нет |
> | pricings | нет | нет |
> | регулаторикомплианцестандардс | нет | нет |
> | Регулаторикомплианцестандардс/Регулаторикомплианцеконтролс | нет | нет |
> | Регулаторикомплианцестандардс/Регулаторикомплианцеконтролс/Регулаторикомплианцеассессментс | нет | нет |
> | securityContacts | нет | нет |
> | securitySolutions | нет | нет |
> | securitySolutionsReferenceData | нет | нет |
> | securityStatuses | нет | нет |
> | securityStatusesSummaries | нет | нет |
> | сервервулнерабилитяссессментс | нет | нет |
> | Параметры | нет | нет |
> | подоценка | нет | нет |
> | задачи | нет | нет |
> | topologies | нет | нет |
> | workspaceSettings | нет | нет |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | нет | нет |
> | diagnosticSettingsCategories | нет | нет |

## <a name="microsoftsecurityinsights"></a>Microsoft. Секуритинсигхтс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | aggregations | нет | нет |
> | alertRules | нет | нет |
> | алертрулетемплатес | нет | нет |
> | закладки | нет | нет |
> | cases | нет | нет |
> | Подключения к компонентам | нет | нет |
> | Сущности | нет | нет |
> | ентитикуериес | нет | нет |
> | оффицеконсентс | нет | нет |
> | Параметры | нет | нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да | нет |
> | пространства имен/authorizationrules | нет | нет |
> | пространства имен/дисастеррековериконфигс | нет | нет |
> | пространства имен/евентгридфилтерс | нет | нет |
> | пространства имен/нетворкрулесетс | нет | нет |
> | пространства имен и очереди | нет | нет |
> | пространства имен/очереди/authorizationrules | нет | нет |
> | пространства имен и разделы | нет | нет |
> | пространства имен/разделы/authorizationrules | нет | нет |
> | пространства имен, разделы и подписки | нет | нет |
> | пространства имен, разделы, подписки и правила | нет | нет |
> | premiumMessagingRegions | нет | нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | веб-масштабированием; | Да | Да |
> | clusters | Да | Да |
> | кластеры и приложения | нет | нет |
> | containerGroups | Да | Да |
> | контаинерграупсетс | Да | Да |
> | edgeclusters | Да | Да |
> | еджеклустерс и приложения | нет | нет |
> | networks | Да | Да |
> | secretstores | Да | Да |
> | секретсторес и сертификаты | нет | нет |
> | секретсторес/секреты | нет | нет |
> | volumes. | Да | Да |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | веб-масштабированием; | Да | Да |
> | containerGroups | Да | Да |
> | gateways | Да | Да |
> | networks | Да | Да |
> | секретные коды | Да | Да |
> | volumes. | Да | Да |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | нет | нет |
> | Провидеррегистратионс/Ресаурцетиперегистратионс | нет | нет |
> | rollouts | Да | Да |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SignalR | Да | Да |
> | SignalR/Евентгридфилтерс | нет | нет |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Да | Да |

## <a name="microsoftsoftwareplan"></a>Microsoft. Софтвареплан

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хибридусебенефитс | нет | нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Да | Да |
> | веб-масштабированием; | Да | Да |
> | jitRequests | Да | Да |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managedInstances | Да | Да |
> | Манажединстанцес и базы данных | Да (см. [Примечание ниже](#sqlnote)) | Да |
> | Манажединстанцес/databases/БаккупшорттермретентионполиЦиес | нет | нет |
> | Манажединстанцес/базы данных, схемы, таблицы, столбцы и Сенситивитилабелс | нет | нет |
> | Манажединстанцес/databases/Вулнерабилитяссессментс | нет | нет |
> | Манажединстанцес/базы данных/Вулнерабилитяссессментс/правила и базовые планы | нет | нет |
> | Манажединстанцес/Енкриптионпротектор | нет | нет |
> | Манажединстанцес и ключи | нет | нет |
> | Манажединстанцес/Ресторабледроппеддатабасес/БаккупшорттермретентионполиЦиес | нет | нет |
> | Манажединстанцес/Вулнерабилитяссессментс | нет | нет |
> | servers | Да | Да |
> | серверы и администраторы | нет | нет |
> | серверы и Коммуникатионлинкс | нет | нет |
> | серверы и базы данных | Да (см. [Примечание ниже](#sqlnote)) | Да |
> | серверы и Енкриптионпротектор | нет | нет |
> | серверы и Фиреваллрулес | нет | нет |
> | серверы и ключи | нет | нет |
> | серверы и Ресторабледроппеддатабасес | нет | нет |
> | серверы и сервицеобжективес | нет | нет |
> | серверы и Тдецертификатес | нет | нет |
> | виртуалклустерс | нет | нет |

<a id="sqlnote" />

> [!NOTE]
> База данных Master не поддерживает теги, но другие базы данных, в том числе базы данных хранилища данных Azure SQL, поддерживают их. Базы данных Хранилища данных SQL Azure должны быть в активном состоянии (работа не приостановлена).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Да | Да |
> | Склвиртуалмачинеграупс/Аваилабилитиграуплистенерс | нет | нет |
> | SqlVirtualMachines | Да | Да |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Да | Да |
> | storageAccounts/Блобсервицес | нет | нет |
> | storageAccounts/Филесервицес | нет | нет |
> | storageAccounts/Куеуесервицес | нет | нет |
> | storageAccounts и службы | нет | нет |
> | storageAccounts/Services/metricDefinitions | нет | нет |
> | storageAccounts/Таблесервицес | нет | нет |
> | usages | нет | нет |

## <a name="microsoftstoragecache"></a>Microsoft. Сторажекаче

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | кэширует | Да | Да |
> | кэши/Сторажетаржетс | нет | нет |
> | усажемоделс | нет | нет |

## <a name="microsoftstoragereplication"></a>Microsoft. Сторажерепликатион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | репликатионграупс | нет | нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да | Да |
> | Сторажесинксервицес/Регистередсерверс | нет | нет |
> | Сторажесинксервицес/Синкграупс | нет | нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | нет | нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | нет | нет |
> | Сторажесинксервицес и рабочие процессы | нет | нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да | Да |
> | Сторажесинксервицес/Регистередсерверс | нет | нет |
> | Сторажесинксервицес/Синкграупс | нет | нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | нет | нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | нет | нет |
> | Сторажесинксервицес и рабочие процессы | нет | нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да | Да |
> | Сторажесинксервицес/Регистередсерверс | нет | нет |
> | Сторажесинксервицес/Синкграупс | нет | нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | нет | нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | нет | нет |
> | Сторажесинксервицес и рабочие процессы | нет | нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managers | Да | Да |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Да (см. примечание ниже) | Да |

> [!NOTE]
> Невозможно добавить тег при выполнении streamingjobs. Остановите ресурс, чтобы добавить тег.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | cancel | нет | нет |
> | CreateSubscription | нет | нет |
> | enable | нет | нет |
> | переименовать | нет | нет |
> | SubscriptionDefinitions | нет | нет |
> | SubscriptionOperations | нет | нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | environments | Да | нет |
> | среды и accessPolicies | нет | нет |
> | среды и классов EventSource | Да | нет |
> | среды и Референцедатасетс | Да | нет |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | дедикатедклауднодес | Да | Да |
> | дедикатедклаудсервицес | Да | Да |
> | virtualMachines | Да | Да |

## <a name="microsoftvnfmanager"></a>Microsoft. Внфманажер

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | устройства | Да | Да |
> | разработчиков | нет | нет |
> | поставщики и номера SKU | нет | нет |
> | внфс | Да | Да |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | нет | нет |
> | Апиманажементаккаунтс/списков ACL для | нет | нет |
> | Апиманажементаккаунтс/API | нет | нет |
> | Апиманажементаккаунтс/API/списков ACL для | нет | нет |
> | Апиманажементаккаунтс/API/списков ACL для | нет | нет |
> | Апиманажементаккаунтс/API/подключения | нет | нет |
> | Апиманажементаккаунтс/API/Connections/списков ACL для | нет | нет |
> | Апиманажементаккаунтс/API/Локализеддефинитионс | нет | нет |
> | Апиманажементаккаунтс/списков ACL для | нет | нет |
> | Апиманажементаккаунтс и подключения | нет | нет |
> | billingMeters | нет | нет |
> | certificates | Да | Да |
> | connectionGateways | Да | Да |
> | connections | Да | Да |
> | customApis | Да | Да |
> | deletedSites | нет | нет |
> | hostingEnvironments | Да | Да |
> | hostingEnvironments/Евентгридфилтерс | нет | нет |
> | hostingEnvironments/Мултиролепулс | нет | нет |
> | hostingEnvironments/внешнего размещения | нет | нет |
> | publishingUsers | нет | нет |
> | к просмотру фильмов | нет | нет |
> | resourceHealthMetadata | нет | нет |
> | runtimes | нет | нет |
> | serverFarms | Да | Да |
> | serverFarms/Евентгридфилтерс | нет | нет |
> | sites | Да | Да |
> | сайты и конфигурация  | нет | нет |
> | Sites/Евентгридфилтерс | нет | нет |
> | Sites/Хостнамебиндингс | нет | нет |
> | Sites/файл networkconfig | нет | нет |
> | Sites/премиераддонс | Да | Да |
> | сайты и слоты | Да | Да |
> | сайты/слоты/Евентгридфилтерс | нет | нет |
> | сайты/слоты/Хостнамебиндингс | нет | нет |
> | сайты/слоты/файл networkconfig | нет | нет |
> | sourceControls | нет | нет |
> | статикситес | Да | Да |
> | validate | нет | нет |
> | verifyHostingEnvironmentVnet | нет | нет |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | нет | нет |
> | diagnosticSettingsCategories | нет | нет |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Да | Да |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | components | нет | нет |
> | componentsSummary | нет | нет |
> | monitorInstances | нет | нет |
> | monitorInstancesSummary | нет | нет |
> | monitors | нет | нет |
> | notificationSettings | нет | нет |

## <a name="next-steps"></a>Дальнейшие действия

Сведения о том, как применить теги к ресурсам, см. в статье, посвященной [использованию тегов для организации ресурсов Azure](tag-resources.md).
