---
title: Поддержка тегов для ресурсов
description: В этой статье описываются типы ресурсов Azure, поддерживающие теги, а также приводятся сведения о всех службах Azure.
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 5b3e26d914887496eedde609404eaf0b380dbcc0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422139"
---
# <a name="tag-support-for-azure-resources"></a>Поддержка тегов для ресурсов Azure
В этой статье приводятся сведения о поддержке типом ресурса функции [тегов](resource-group-using-tags.md). Столбец с подписью **поддерживает теги** . указывает, имеет ли тип ресурса свойство для тега. Столбец с меткой **тег в отчете о затратах** указывает, передает ли этот тип ресурса тег в отчет о затратах. Вы можете просматривать затраты по тегам в [анализе затрат на управление затратами](../cost-management/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) , а также в [счетах на счета и данные о ежедневном использовании в Azure](../billing/billing-download-azure-invoice-daily-usage-date.md).

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
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft. Виндовсиот](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | DomainServices | Yes | Yes |
> | DomainServices/ауконтаинер | Нет | Нет |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | supportProviders | Нет | Нет |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Нет | Нет |
> | addsservices | Нет | Нет |
> | agents | Нет | Нет |
> | anonymousapiusers | Нет | Нет |
> | Конфигурация | Нет | Нет |
> | журналы | Нет | Нет |
> | reports | Нет | Нет |
> | servicehealthmetrics | Нет | Нет |
> | services; | Нет | Нет |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конфигурации | Нет | Нет |
> | generateRecommendations | Нет | Нет |
> | metadata | Нет | Нет |
> | к просмотру фильмов | Нет | Нет |
> | suppressions | Нет | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | actionRules | Yes | Yes |
> | оповещения | Нет | Нет |
> | alertsList | Нет | Нет |
> | алертсметадата | Нет | Нет |
> | alertsSummary | Нет | Нет |
> | alertsSummaryList | Нет | Нет |
> | Оставлять | Нет | Нет |
> | smartDetectorAlertRules | Yes | Yes |
> | smartDetectorRuntimeEnvironments | Нет | Нет |
> | smartGroups | Нет | Нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | servers | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Нет | Нет |
> | Служба | Yes | Yes |
> | validateServiceName | Нет | Нет |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конфигуратионсторес | Yes | Yes |
> | Конфигуратионсторес/Евентгридфилтерс | Нет | Нет |

## <a name="microsoftappplatform"></a>Microsoft. Аппплатформ

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Spring | Yes | Yes |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Нет | Нет |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Нет | Нет |
> | Псевдонимы | Нет | Нет |
> | denyAssignments | Нет | Нет |
> | elevateAccess | Нет | Нет |
> | финдорфанролеассигнментс | Нет | Нет |
> | locks | Нет | Нет |
> | permissions | Нет | Нет |
> | policyAssignments | Нет | Нет |
> | policyDefinitions | Нет | Нет |
> | policySetDefinitions | Нет | Нет |
> | providerOperations | Нет | Нет |
> | roleAssignments | Нет | Нет |
> | roleDefinitions | Нет | Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Yes | Yes |
> | automationAccounts и конфигурации | Yes | Yes |
> | automationAccounts и задания | Нет | Нет |
> | automationAccounts и модули Runbook | Yes | Yes |
> | automationAccounts/Софтвареупдатеконфигуратионс | Нет | Нет |
> | automationAccounts/веб-перехватчики | Нет | Нет |

## <a name="microsoftazconfig"></a>Microsoft. Азконфиг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конфигуратионсторес | Yes | Yes |
> | Конфигуратионсторес/Евентгридфилтерс | Нет | Нет |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | environments | Нет | Нет |
> | среды и учетные записи | Нет | Нет |
> | среды, учетные записи и пространства имен | Нет | Нет |
> | среды, учетные записи, пространства имен и конфигурации | Нет | Нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Yes | Нет |
> | b2ctenants | Нет | Нет |

## <a name="microsoftazuredata"></a>Microsoft. Азуредата

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хибриддатаманажерс | Yes | Yes |
> | постгресинстанцес | Yes | Yes |
> | склбигдатаклустерс | Yes | Yes |
> | склинстанцес | Yes | Yes |
> | склсерверрегистратионс | Yes | Yes |
> | Склсерверрегистратионс и sqlServer | Нет | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | registrations | Yes | Yes |
> | регистрации и Кустомерсубскриптионс | Нет | Нет |
> | регистрации и продукты | Нет | Нет |
> | верификатионкэйс | Нет | Нет |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Yes | Yes |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Нет | Нет |
> | Биллингаккаунтс и соглашения | Нет | Нет |
> | Биллингаккаунтс/Биллингпермиссионс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингпермиссионс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингролеассигнментс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингроледефинитионс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингсубскриптионс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Креатебиллингролеассигнмент | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/клиенты | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/счета | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/счета/прайс-лист | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингпермиссионс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингролеассигнментс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингроледефинитионс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингсубскриптионс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Креатебиллингролеассигнмент | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Инитиатетрансфер | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products/перемещение | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products/Упдатеауторенев | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/транзакции | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/передача | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Патчоператионс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Пайментмесодс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/политики | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/прайс-лист | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Прицешитдовнлоадоператионс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Products | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/транзакции | Нет | Нет |
> | Биллингаккаунтс/Биллингролеассигнментс | Нет | Нет |
> | Биллингаккаунтс/Биллингроледефинитионс | Нет | Нет |
> | Биллингаккаунтс/Биллингсубскриптионс | Нет | Нет |
> | Биллингаккаунтс/Биллингсубскриптионс/счета | Нет | Нет |
> | Биллингаккаунтс/Креатебиллингролеассигнмент | Нет | Нет |
> | Биллингаккаунтс/Креатеинвоицесектионоператионс | Нет | Нет |
> | Биллингаккаунтс и клиенты | Нет | Нет |
> | Биллингаккаунтс/Customers/Биллингпермиссионс | Нет | Нет |
> | Биллингаккаунтс/Customers/Биллингсубскриптионс | Нет | Нет |
> | Биллингаккаунтс/Customers/Инитиатетрансфер | Нет | Нет |
> | Биллингаккаунтс, клиенты и политики | Нет | Нет |
> | Биллингаккаунтс/Customers/Products | Нет | Нет |
> | Биллингаккаунтс/Customers/Transactions | Нет | Нет |
> | Биллингаккаунтс/Customers/Transfers | Нет | Нет |
> | Биллингаккаунтс и отделы | Нет | Нет |
> | Биллингаккаунтс/Енроллментаккаунтс | Нет | Нет |
> | Биллингаккаунтс/счета | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионмовеоператионс | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионс | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионс/перемещение | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/повышение прав | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Инитиатетрансфер | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Патчоператионс | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Продуктмовеоператионс | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Products | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/продукты/перемещение | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Products/Упдатеауторенев | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/транзакции | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/передача | Нет | Нет |
> | Биллингаккаунтс/Линеофкредит | Нет | Нет |
> | Биллингаккаунтс/Патчоператионс | Нет | Нет |
> | Биллингаккаунтс/Пайментмесодс | Нет | Нет |
> | Биллингаккаунтс и продукты | Нет | Нет |
> | Биллингаккаунтс/транзакции | Нет | Нет |
> | billingPeriods | Нет | Нет |
> | биллингпермиссионс | Нет | Нет |
> | billingProperty | Нет | Нет |
> | биллингролеассигнментс | Нет | Нет |
> | биллингроледефинитионс | Нет | Нет |
> | креатебиллингролеассигнмент | Нет | Нет |
> | departments | Нет | Нет |
> | enrollmentAccounts | Нет | Нет |
> | invoices | Нет | Нет |
> | transfers | Нет | Нет |
> | передачи/Акцепттрансфер | Нет | Нет |
> | передачи/Деклинетрансфер | Нет | Нет |
> | передачи/значение operationstatus | Нет | Нет |
> | передачи/Валидатетрансфер | Нет | Нет |
> | валидатеаддресс | Нет | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | mapApis | Yes | Yes |
> | updateCommunicationPreference | Нет | Нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | блоккчаинмемберс | Yes | Yes |
> | кордамемберс | Yes | Yes |
> | наблюдатели | Yes | Yes |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Нет | Нет |
> | Блуепринтассигнментс/Ассигнментоператионс | Нет | Нет |
> | Блуепринтассигнментс/операции | Нет | Нет |
> | blueprints | Нет | Нет |
> | схемы и артефакты | Нет | Нет |
> | проекты и версии | Нет | Нет |
> | схемы, версии и артефакты | Нет | Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | botServices | Yes | Yes |
> | Ботсервицес и каналы | Нет | Нет |
> | Ботсервицес и подключения | Нет | Нет |
> | языки | Нет | Нет |
> | шаблоны | Нет | Нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Redis | Yes | Yes |
> | RedisConfigDefinition | Нет | Нет |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Нет | Нет |
> | калкулатиксчанже | Нет | Нет |
> | calculatePrice | Нет | Нет |
> | калкулатепурчасеприце | Нет | Нет |
> | catalogs | Нет | Нет |
> | commercialReservationOrders | Нет | Нет |
> | обмен валюты | Нет | Нет |
> | плацепурчасеордер | Нет | Нет |
> | reservationOrders | Нет | Нет |
> | Ресерватионордерс/Калкулатерефунд | Нет | Нет |
> | Ресерватионордерс/Merge | Нет | Нет |
> | Ресерватионордерс/резервирования | Нет | Нет |
> | Ресерватионордерс/резервирования/редакции | Нет | Нет |
> | Ресерватионордерс/Return | Нет | Нет |
> | Ресерватионордерс/Split | Нет | Нет |
> | Ресерватионордерс/swap | Нет | Нет |
> | reservations | Нет | Нет |
> | ресурсов | Нет | Нет |
> | validateReservationOrder | Нет | Нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | кднвебаппликатионфиреваллманажедрулесетс | Нет | Нет |
> | кднвебаппликатионфиреваллполиЦиес | Yes | Yes |
> | edgenodes | Нет | Нет |
> | профили | Yes | Yes |
> | профили и конечные точки | Yes | Yes |
> | профили, конечные точки/кустомдомаинс | Нет | Нет |
> | профили, конечные точки и источники | Нет | Нет |
> | validateProbe | Нет | Нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Yes | Yes |
> | Certificateorder и сертификаты | Нет | Нет |
> | validateCertificateRegistrationInformation | Нет | Нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capabilities | Нет | Нет |
> | domainNames | Нет | Нет |
> | имя_домена/возможности | Нет | Нет |
> | имя_домена/Интерналлоадбаланцерс | Нет | Нет |
> | имя_домена/Сервицецертификатес | Нет | Нет |
> | имя_домена/слоты | Нет | Нет |
> | Доменные имя, слоты и роли | Нет | Нет |
> | имя_домена/слоты/роли/metricDefinitions | Нет | Нет |
> | имя_домена/слоты/роли/метрики | Нет | Нет |
> | moveSubscriptionResources | Нет | Нет |
> | operatingSystemFamilies | Нет | Нет |
> | operatingSystems | Нет | Нет |
> | quotas | Нет | Нет |
> | resourceTypes | Нет | Нет |
> | validateSubscriptionMoveAvailability | Нет | Нет |
> | virtualMachines | Нет | Нет |
> | virtualMachines/diagnosticSettings | Нет | Нет |
> | virtualMachines/metricDefinitions | Нет | Нет |
> | virtualMachines/метрики | Нет | Нет |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Нет | Нет |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capabilities | Нет | Нет |
> | expressRouteCrossConnections | Нет | Нет |
> | Експрессраутекроссконнектионс и пиринг | Нет | Нет |
> | gatewaySupportedDevices | Нет | Нет |
> | networkSecurityGroups | Нет | Нет |
> | quotas | Нет | Нет |
> | reservedIps | Нет | Нет |
> | virtualNetworks | Нет | Нет |
> | virtualNetworks/Ремотевиртуалнетворкпирингпроксиес | Нет | Нет |
> | virtualNetworks/Виртуалнетворкпирингс | Нет | Нет |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capabilities | Нет | Нет |
> | disks | Нет | Нет |
> | images | Нет | Нет |
> | osImages | Нет | Нет |
> | osPlatformImages | Нет | Нет |
> | publicImages | Нет | Нет |
> | quotas | Нет | Нет |
> | storageAccounts | Нет | Нет |
> | storageAccounts/Блобсервицес | Нет | Нет |
> | storageAccounts/Филесервицес | Нет | Нет |
> | storageAccounts/metricDefinitions | Нет | Нет |
> | storageAccounts/метрики | Нет | Нет |
> | storageAccounts/Куеуесервицес | Нет | Нет |
> | storageAccounts и службы | Нет | Нет |
> | storageAccounts/Services/diagnosticSettings | Нет | Нет |
> | storageAccounts/Services/metricDefinitions | Нет | Нет |
> | storageAccounts/Services/метрики | Нет | Нет |
> | storageAccounts/Таблесервицес | Нет | Нет |
> | storageAccounts/Вмимажес | Нет | Нет |
> | vmImages | Нет | Нет |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | RateCard | Нет | Нет |
> | UsageAggregates | Нет | Нет |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Yes | Yes |
> | дискенкриптионсетс | Yes | Yes |
> | disks | Yes | Yes |
> | galleries | Yes | Yes |
> | коллекции и приложения | Нет | Нет |
> | коллекции, приложения и версии | Нет | Нет |
> | коллекции и изображения | Нет | Нет |
> | коллекции, изображения и версии | Нет | Нет |
> | хостграупс | Yes | Yes |
> | Хостграупс и узлы | Yes | Yes |
> | images | Yes | Yes |
> | проксимитиплацементграупс | Yes | Yes |
> | restorePointCollections | Yes | Yes |
> | Ресторепоинтколлектионс/Ресторепоинтс | Нет | Нет |
> | sharedVMImages | Yes | Yes |
> | Шаредвмимажес и версии | Нет | Нет |
> | snapshots | Yes | Yes |
> | virtualMachines | Yes | Yes |
> | virtualMachines и расширения | Yes | Yes |
> | virtualMachines/metricDefinitions | Нет | Нет |
> | virtualMachineScaleSets | Yes | Yes |
> | virtualMachineScaleSets и расширения | Нет | Нет |
> | virtualMachineScaleSets/networkInterfaces | Нет | Нет |
> | virtualMachineScaleSets/publicIPAddresses | Нет | Нет |
> | virtualMachineScaleSets/virtualMachines | Нет | Нет |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Нет | Нет |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Нет | Нет |
> | сведения о балансе. | Нет | Нет |
> | Бюджеты | Нет | Нет |
> | Расходы | Нет | Нет |
> | CostTags | Нет | Нет |
> | credits | Нет | Нет |
> | events | Нет | Нет |
> | Прогнозы | Нет | Нет |
> | lots | Нет | Нет |
> | Marketplace | Нет | Нет |
> | Pricesheets | Нет | Нет |
> | products | Нет | Нет |
> | ReservationDetails | Нет | Нет |
> | ReservationRecommendations | Нет | Нет |
> | ReservationSummaries | Нет | Нет |
> | ReservationTransactions | Нет | Нет |
> | Теги | Нет | Нет |
> | tenants | Нет | Нет |
> | Термины | Нет | Нет |
> | UsageDetails | Нет | Нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | containerGroups | Yes | Yes |
> | serviceAssociationLinks | Нет | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | registries | Yes | Yes |
> | реестры и сборки | Нет | Нет |
> | реестры/сборки/Отмена | Нет | Нет |
> | реестры/сборки/Жетлоглинк | Нет | Нет |
> | реестры и Буилдтаскс | Yes | Yes |
> | реестры, Буилдтаскс и шаги | Нет | Нет |
> | реестры и Евентгридфилтерс | Нет | Нет |
> | реестры и Женератекредентиалс | Нет | Нет |
> | реестры и Жетбуилдсаурцеуплоадурл | Нет | Нет |
> | реестры и учетные данные | Нет | Нет |
> | реестры и Импортимаже | Нет | Нет |
> | реестры и Куеуебуилд | Нет | Нет |
> | реестры и Реженератекредентиал | Нет | Нет |
> | реестры и Реженератекредентиалс | Нет | Нет |
> | реестры и репликации | Yes | Yes |
> | реестры и запуски | Нет | Нет |
> | реестры/запуски/Отмена | Нет | Нет |
> | реестры и Счедулерун | Нет | Нет |
> | реестры и Скопемапс | Нет | Нет |
> | реестры и Таскрунс | Yes | Yes |
> | реестры и задачи | Yes | Yes |
> | реестры и маркеры | Нет | Нет |
> | реестры и УпдатеполиЦиес | Нет | Нет |
> | реестры и веб-перехватчики | Yes | Yes |
> | реестры/веб-перехватчики/Жеткаллбаккконфиг | Нет | Нет |
> | реестры, веб-перехватчики и проверка связи | Нет | Нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | containerServices | Yes | Yes |
> | managedClusters | Yes | Yes |
> | опеншифтманажедклустерс | Yes | Yes |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Оповещения | Нет | Нет |
> | BillingAccounts | Нет | Нет |
> | Бюджеты | Нет | Нет |
> | клаудконнекторс | Нет | Нет |
> | Соединители | Yes | Yes |
> | Departments | Нет | Нет |
> | Измерения | Нет | Нет |
> | EnrollmentAccounts | Нет | Нет |
> | Экспорт | Нет | Нет |
> | екстерналбиллингаккаунтс | Нет | Нет |
> | Екстерналбиллингаккаунтс и оповещения | Нет | Нет |
> | Екстерналбиллингаккаунтс и измерения | Нет | Нет |
> | Екстерналбиллингаккаунтс/прогноз | Нет | Нет |
> | Екстерналбиллингаккаунтс/запрос | Нет | Нет |
> | екстерналсубскриптионс | Нет | Нет |
> | Екстерналсубскриптионс и оповещения | Нет | Нет |
> | Екстерналсубскриптионс и измерения | Нет | Нет |
> | Екстерналсубскриптионс/прогноз | Нет | Нет |
> | Екстерналсубскриптионс/запрос | Нет | Нет |
> | Грешно | Нет | Нет |
> | Запрос | Нет | Нет |
> | регистрация | Нет | Нет |
> | Reportconfigs | Нет | Нет |
> | Отчеты | Нет | Нет |
> | данных | Нет | Нет |
> | шовбаккрулес | Нет | Нет |
> | Views | Нет | Нет |

## <a name="microsoftcustomerlockbox"></a>Microsoft. Кустомерлоккбокс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | requests | Нет | Нет |

## <a name="microsoftcustomproviders"></a>Microsoft. Кустомпровидерс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | связи | Нет | Нет |
> | ресаурцепровидерс | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | jobs | Yes | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Yes | Yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | workspaces | Yes | Нет |
> | рабочие области и Виртуалнетворкпирингс | Нет | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | catalogs | Yes | Yes |
> | каталоги | Yes | Yes |
> | каталоги данных и источники данных | Нет | Нет |
> | каталоги данных, источники данных и проверки | Нет | Нет |
> | каталоги данных/DataSources/scans/DataSets | Нет | Нет |
> | каталоги данных, источники данных, проверки и триггеры | Нет | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | dataFactories | Yes | Нет |
> | Коэффициенты и diagnosticSettings | Нет | Нет |
> | Коэффициенты и metricDefinitions | Нет | Нет |
> | dataFactorySchema | Нет | Нет |
> | factories | Yes | Нет |
> | фабрики/Интегратионрунтимес | Нет | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | Accounts/Даталакестореаккаунтс | Нет | Нет |
> | Accounts/storageAccounts | Нет | Нет |
> | Accounts/storageAccounts/Containers | Нет | Нет |
> | Accounts/Трансфераналитиксунитс | Нет | Нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | Accounts/Евентгридфилтерс | Нет | Нет |
> | Accounts/Фиреваллрулес | Нет | Нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | Нет | Нет |
> | службы и проекты | Нет | Нет |

## <a name="microsoftdatashare"></a>Общая папка Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | учетные записи и общие папки | Нет | Нет |
> | учетные записи, общие ресурсы и наборы данных | Нет | Нет |
> | учетные записи, общие ресурсы и приглашения | Нет | Нет |
> | Accounts/shares/провидершаресубскриптионс | Нет | Нет |
> | Accounts/shares/Синчронизатионсеттингс | Нет | Нет |
> | Accounts/шаресубскриптионс | Нет | Нет |
> | Accounts/шаресубскриптионс/Консумерсаурцедатасетс | Нет | Нет |
> | Accounts/шаресубскриптионс/датасетмаппингс | Нет | Нет |
> | учетные записи/шаресубскриптионс/триггеры | Нет | Нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | servers | Yes | Yes |
> | серверы и помощники | Нет | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет | Нет |
> | серверы и Привателинкресаурцес | Нет | Нет |
> | серверы и Куеритекстс | Нет | Нет |
> | серверы и Рековераблесерверс | Нет | Нет |
> | серверы и Топкуеристатистикс | Нет | Нет |
> | серверы и Виртуалнетворкрулес | Нет | Нет |
> | серверы и Ваитстатистикс | Нет | Нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | servers | Yes | Yes |
> | серверы и помощники | Нет | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет | Нет |
> | серверы и Привателинкресаурцес | Нет | Нет |
> | серверы и Куеритекстс | Нет | Нет |
> | серверы и Рековераблесерверс | Нет | Нет |
> | серверы и Топкуеристатистикс | Нет | Нет |
> | серверы и Виртуалнетворкрулес | Нет | Нет |
> | серверы и Ваитстатистикс | Нет | Нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | серверграупс | Yes | Yes |
> | servers | Yes | Yes |
> | серверы и помощники | Нет | Нет |
> | серверы и ключи | Нет | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет | Нет |
> | серверы и Привателинкресаурцес | Нет | Нет |
> | серверы и Куеритекстс | Нет | Нет |
> | серверы и Рековераблесерверс | Нет | Нет |
> | серверы и Топкуеристатистикс | Нет | Нет |
> | серверы и Виртуалнетворкрулес | Нет | Нет |
> | серверы и Ваитстатистикс | Нет | Нет |
> | serversv2 | Yes | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | артифактсаурцес | Yes | Yes |
> | rollouts | Yes | Yes |
> | сервицетопологиес | Yes | Yes |
> | Сервицетопологиес и службы | Yes | Yes |
> | Сервицетопологиес/Services/Сервицеунитс | Yes | Yes |
> | steps | Yes | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. Десктопвиртуализатион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | аппликатионграупс | Yes | Yes |
> | аппликатионграупс и приложения | Нет | Нет |
> | аппликатионграупс и настольные системы | Нет | Нет |
> | аппликатионграупс/стартменуитемс | Нет | Нет |
> | хостпулс | Yes | Yes |
> | хостпулс/сессионхостс | Нет | Нет |
> | хостпулс/сессионхостс/усерсессионс | Нет | Нет |
> | хостпулс/усерсессионс | Нет | Нет |
> | workspaces | Yes | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | еластикпулс | Yes | Yes |
> | Еластикпулс/Иосубтенантс | Yes | Yes |
> | IotHubs | Yes | Yes |
> | IotHubs/Евентгридфилтерс | Нет | Нет |
> | ProvisioningServices | Yes | Yes |
> | usages | Нет | Нет |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конвейеров | Yes | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | controllers | Yes | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | labcenters | Yes | Yes |
> | labs | Yes | Yes |
> | лаборатории и среды | Yes | Yes |
> | Labs и Сервицеруннерс | Yes | Yes |
> | Labs и virtualMachines | Yes | Yes |
> | schedules | Yes | Yes |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Нет | Нет |
> | databaseAccounts | Yes | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | domains | Yes | Yes |
> | домены/Домаиновнершипидентифиерс | Нет | Нет |
> | generateSsoRequest | Нет | Нет |
> | topLevelDomains | Нет | Нет |
> | validateDomainRegistrationInformation | Нет | Нет |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Нет | Нет |
> | лкспрожектс/клауддеплойментс | Нет | Нет |
> | лкспрожектс и соединители | Нет | Нет |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | domains | Yes | Yes |
> | домены и темы | Нет | Нет |
> | eventSubscriptions | Нет | Нет |
> | extensionTopics | Нет | Нет |
> | topics | Yes | Yes |
> | topicTypes | Нет | Нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Yes | Yes |
> | namespaces | Yes | Yes |
> | пространства имен/authorizationrules | Нет | Нет |
> | пространства имен/дисастеррековериконфигс | Нет | Нет |
> | пространства имен/eventhubs | Нет | Нет |
> | пространства имен/eventhubs/authorizationrules | Нет | Нет |
> | пространства имен/eventhubs/eventhub | Нет | Нет |
> | пространства имен/нетворкрулесетс | Нет | Нет |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | features | Нет | Нет |
> | providers | Нет | Нет |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | enroll | Нет | Нет |
> | galleryitems | Нет | Нет |
> | generateartifactaccessuri | Нет | Нет |
> | myareas | Нет | Нет |
> | мяреас/области | Нет | Нет |
> | мяреас/области/области | Нет | Нет |
> | мяреас/области/области/галлеритемс | Нет | Нет |
> | мяреас/области/галлеритемс | Нет | Нет |
> | мяреас/галлеритемс | Нет | Нет |
> | регистрация | Нет | Нет |
> | ресурсов | Нет | Нет |
> | retrieveresourcesbyid | Нет | Нет |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конфигуратионпрофилеассигнментс | Нет | Нет |
> | guestConfigurationAssignments | Нет | Нет |
> | software | Нет | Нет |
> | софтвареупдатепрофиле | Нет | Нет |
> | софтвареупдатес | Нет | Нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Yes | Yes |
> | сапмониторс | Yes | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | дедикатедхсмс | Yes | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Yes | Yes |
> | кластеры и приложения | Нет | Нет |

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | Yes | Yes |

## <a name="microsofthybridcompute"></a>Microsoft. Хибридкомпуте

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | виртуальных | Yes | Yes |
> | Компьютеры и расширения | Yes | Yes |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Диспетчеры | Yes | Yes |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | components | Yes | Yes |
> | нетворкскопес | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | jobs | Yes | Yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Нет | Нет |
> | diagnosticSettingsCategories | Нет | Нет |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | апптемплатес | Нет | Нет |
> | IoTApps | Yes | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | График | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Нет | Нет |
> | хсмпулс | Yes | Yes |
> | vaults | Yes | Yes |
> | хранилища и accessPolicies | Нет | Нет |
> | хранилища и Евентгридфилтерс | Нет | Нет |
> | хранилища и секреты | Нет | Нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Yes | Yes |
> | кластеры/аттачеддатабасеконфигуратионс | Нет | Нет |
> | кластеры и базы данных | Нет | Нет |
> | кластеры/базы данных/подключения | Нет | Нет |
> | кластеры/базы данных/евенсубконнектионс | Нет | Нет |
> | кластеры/шаредидентитиес | Нет | Нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | labaccounts | Yes | Yes |
> | пользователи | Нет | Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Yes | Yes |
> | integrationAccounts | Yes | Yes |
> | интегратионсервицеенвиронментс | Yes | Yes |
> | Интегратионсервицеенвиронментс/Манажедапис | Yes | Yes |
> | исолатеденвиронментс | Yes | Yes |
> | workflows | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Yes | Yes |
> | webServices | Yes | Yes |
> | Рабочие области | Yes | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | workspaces | Yes | Yes |
> | рабочие области и расчеты | Нет | Нет |
> | рабочие области и Евентгридфилтерс | Нет | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Identities | Нет | Нет |
> | userAssignedIdentities | Yes | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | маркетплацерегистратиондефинитионс | Нет | Нет |
> | регистратионассигнментс | Нет | Нет |
> | регистратиондефинитионс | Нет | Нет |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | getEntities | Нет | Нет |
> | managementGroups | Нет | Нет |
> | ресурсов | Нет | Нет |
> | startTenantBackfill | Нет | Нет |
> | tenantBackfillStatus | Нет | Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | Accounts/Евентгридфилтерс | Нет | Нет |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | offers | Нет | Нет |
> | offerTypes | Нет | Нет |
> | Оффертипес и издатели | Нет | Нет |
> | Оффертипес, издатели и предложения | Нет | Нет |
> | Оффертипес, издатели, предложения и планы | Нет | Нет |
> | Оффертипес/Publishers/Offers/Plans/Agreement | Нет | Нет |
> | Оффертипес/Publishers/предложения, планы и конфигурации | Нет | Нет |
> | Оффертипес/Publishers/Offers/Plans/Configurations/Импортимаже | Нет | Нет |
> | privategalleryitems | Нет | Нет |
> | products | Нет | Нет |
> | издателя | Нет | Нет |
> | издатели и предложения | Нет | Нет |
> | издатели, предложения и поправки | Нет | Нет |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Yes | Yes |
> | updateCommunicationPreference | Нет | Нет |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | agreements | Нет | Нет |
> | offertypes | Нет | Нет |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | mediaservices | Yes | Yes |
> | mediaservices/Аккаунтфилтерс | Нет | Нет |
> | mediaservices и активы | Нет | Нет |
> | mediaservices/активы/Ассетфилтерс | Нет | Нет |
> | mediaservices/КонтенткэйполиЦиес | Нет | Нет |
> | mediaservices/Евентгридфилтерс | Нет | Нет |
> | mediaservices/Лививентоператионс | Нет | Нет |
> | mediaservices/Лививентс | Yes | Yes |
> | mediaservices/Лививентс/Ливеаутпутс | Нет | Нет |
> | mediaservices/Ливеаутпутоператионс | Нет | Нет |
> | mediaservices/Медиаграфс | Нет | Нет |
> | mediaservices/Стреаминжендпоинтоператионс | Нет | Нет |
> | mediaservices/Streamingendpoint | Yes | Yes |
> | mediaservices/Стреаминглокаторс | Нет | Нет |
> | mediaservices/СтреамингполиЦиес | Нет | Нет |
> | mediaservices/преобразования | Нет | Нет |
> | mediaservices/преобразования/задания | Нет | Нет |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | аппклустерс | Yes | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | ассессментпрожектс | Yes | Yes |
> | migrateprojects | Yes | Yes |
> | projects | Yes | Yes |

## <a name="microsoftmixedreality"></a>Microsoft. Микседреалити

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | холографиксброадкастаккаунтс | Yes | Yes |
> | обжектундерстандингаккаунтс | Yes | Yes |
> | ремотерендерингаккаунтс | Yes | Yes |
> | спатиаланчорсаккаунтс | Yes | Yes |
> | сурфацереконструктионаккаунтс | Yes | Yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | нетаппаккаунтс | Yes | Нет |
> | Нетаппаккаунтс/КапаЦитипулс | Yes | Нет |
> | Нетаппаккаунтс/КапаЦитипулс/тома | Yes | Нет |
> | Нетаппаккаунтс/КапаЦитипулс/Volumes/Маунттаржетс | Yes | Нет |
> | Нетаппаккаунтс/КапаЦитипулс/тома/моментальные снимки | Yes | Нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Yes | Yes |
> | аппликатионгатевайвебаппликатионфиреваллполиЦиес | Yes | Yes |
> | applicationSecurityGroups | Yes | Yes |
> | azureFirewallFqdnTags | Нет | Нет |
> | azureFirewalls | Yes | Нет |
> | бастионхостс | Yes | Yes |
> | bgpServiceCommunities | Нет | Нет |
> | connections | Yes | Yes |
> | ddosCustomPolicies | Yes | Yes |
> | ddosProtectionPlans | Yes | Yes |
> | dnsOperationStatuses | Нет | Нет |
> | dnszones | Yes | Yes |
> | днсзонес/A | Нет | Нет |
> | днсзонес/AAAA | Нет | Нет |
> | днсзонес/все | Нет | Нет |
> | днсзонес/CAA | Нет | Нет |
> | днсзонес и CNAME | Нет | Нет |
> | днсзонес/MX | Нет | Нет |
> | днсзонес/NS | Нет | Нет |
> | днсзонес/PTR | Нет | Нет |
> | днсзонес и наборы записей | Нет | Нет |
> | днсзонес/SOA | Нет | Нет |
> | днсзонес/SRV | Нет | Нет |
> | днсзонес/TXT | Нет | Нет |
> | expressRouteCircuits | Yes | Yes |
> | expressRouteCrossConnections | Yes | Yes |
> | експрессраутегатевайс | Yes | Yes |
> | експрессраутепортс | Yes | Yes |
> | expressRouteServiceProviders | Нет | Нет |
> | фиреваллполиЦиес | Yes | Yes |
> | frontdoors | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | Yes |
> | фронтдурвебаппликатионфиреваллманажедрулесетс | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | Нет |
> | frontdoorWebApplicationFirewallPolicies | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | Yes |
> | getDnsResourceReference | Нет | Нет |
> | internalNotify | Нет | Нет |
> | loadBalancers | Yes | Нет |
> | localNetworkGateways | Yes | Yes |
> | natGateways | Yes | Yes |
> | networkIntentPolicies | Yes | Yes |
> | networkInterfaces | Yes | Yes |
> | networkProfiles | Yes | Yes |
> | networkSecurityGroups | Yes | Yes |
> | networkWatchers | Yes | Нет |
> | Нетворкватчерс/Коннектионмониторс | Yes | Нет |
> | Нетворкватчерс/lenses | Yes | Нет |
> | Нетворкватчерс/Пингмешес | Yes | Нет |
> | p2sVpnGateways | Yes | Yes |
> | приватеднсоператионстатусес | Нет | Нет |
> | приватеднсзонес | Yes | Yes |
> | Приватеднсзонес/A | Нет | Нет |
> | Приватеднсзонес/AAAA | Нет | Нет |
> | Приватеднсзонес/все | Нет | Нет |
> | Приватеднсзонес и CNAME | Нет | Нет |
> | Приватеднсзонес/MX | Нет | Нет |
> | Приватеднсзонес/PTR | Нет | Нет |
> | Приватеднсзонес/SOA | Нет | Нет |
> | Приватеднсзонес/SRV | Нет | Нет |
> | Приватеднсзонес/TXT | Нет | Нет |
> | Приватеднсзонес/Виртуалнетворклинкс | Yes | Yes |
> | приватиндпоинтс | Yes | Yes |
> | privateLinkServices | Yes | Yes |
> | publicIPAddresses | Yes | Yes |
> | publicIPPrefixes | Yes | Yes |
> | routeFilters | Yes | Yes |
> | routeTables | Yes | Yes |
> | serviceEndpointPolicies | Yes | Yes |
> | trafficManagerGeographicHierarchies | Нет | Нет |
> | trafficmanagerprofiles | Yes | Yes |
> | trafficmanagerprofiles/heatMaps | Нет | Нет |
> | траффикманажерусерметрикскэйс | Нет | Нет |
> | virtualHubs | Yes | Yes |
> | virtualNetworkGateways | Yes | Yes |
> | virtualNetworks | Yes | Yes |
> | virtualNetworkTaps | Yes | Yes |
> | virtualWans | Yes | Yes |
> | vpnGateways | Yes | Нет |
> | vpnSites | Yes | Yes |
> | webApplicationFirewallPolicies | Yes | Yes |

<a id="frontdoor" />

> [!NOTE]
> Для службы "Передняя дверца Azure" можно применять теги при создании ресурса, но обновление или добавление тегов в настоящее время не поддерживается.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | namespaces | Yes | Нет |
> | пространства имен/notificationHubs | Yes | Нет |

## <a name="microsoftobjectstore"></a>Microsoft. Обжектсторе

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | оснамеспацес | Yes | Yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хипервситес | Yes | Yes |
> | импортситес | Yes | Yes |
> | серверситес | Yes | Yes |
> | вмвареситес | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Yes | Yes |
> | устройства | Нет | Нет |
> | linkTargets | Нет | Нет |
> | storageInsightConfigs | Нет | Нет |
> | workspaces | Yes | Yes |
> | рабочие области и источники данных | Нет | Нет |
> | рабочие области и linkedServices | Нет | Нет |
> | рабочие области и запросы | Нет | Нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managementassociations | Нет | Нет |
> | managementconfigurations | Yes | Yes |
> | solutions | Yes | Yes |
> | узел "Представления" | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | легаципирингс | Нет | Нет |
> | пираснс | Нет | Нет |
> | пиринги | Yes | Yes |
> | пирингсервицепровидерс | Нет | Нет |
> | пирингсервицес | Yes | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | policyEvents | Нет | Нет |
> | полициметадата | Нет | Нет |
> | policyStates | Нет | Нет |
> | policyTrackedResources | Нет | Нет |
> | remediations | Нет | Нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | consoles | Нет | Нет |
> | dashboards | Yes | Yes |
> | userSettings | Нет | Нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capacities | Yes | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Нет | Нет |
> | vaults | Yes | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | namespaces | Yes | Yes |
> | пространства имен/authorizationrules | Нет | Нет |
> | пространства имен/хибридконнектионс | Нет | Нет |
> | пространства имен/хибридконнектионс/authorizationrules | Нет | Нет |
> | пространства имен/вкфрелайс | Нет | Нет |
> | пространства имен/вкфрелайс/authorizationrules | Нет | Нет |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Нет | Нет |
> | семейства | Yes | Yes |
> | коллекции и приложения | Нет | Нет |
> | Collections/секуритипринЦипалс | Нет | Нет |
> | темплатеимажес | Нет | Нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Запросы | Yes | Yes |
> | ресаурцечанжедетаилс | Нет | Нет |
> | ресаурцечанжес | Нет | Нет |
> | ресурсов | Нет | Нет |
> | ресаурцешистори | Нет | Нет |
> | subscriptionsStatus | Нет | Нет |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Нет | Нет |
> | childAvailabilityStatuses | Нет | Нет |
> | childResources | Нет | Нет |
> | events | Нет | Нет |
> | impactedResources | Нет | Нет |
> | metadata | Нет | Нет |
> | Уведомления | Нет | Нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | deployments | Yes | Нет |
> | развертывания и операции | Нет | Нет |
> | deploymentScripts | Yes | Yes |
> | deploymentScripts и журналы | Нет | Нет |
> | links | Нет | Нет |
> | notifyResourceJobs | Нет | Нет |
> | providers | Нет | Нет |
> | resourceGroups | Yes | Нет |
> | subscriptions | Нет | Нет |
> | tenants | Нет | Нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | веб-масштабированием; | Yes | Yes |
> | saasresources | Нет | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | jobcollections | Yes | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Нет | Нет |
> | searchServices | Yes | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | адаптивенетворкхарденингс | Нет | Нет |
> | advancedThreatProtectionSettings | Нет | Нет |
> | оповещения | Нет | Нет |
> | allowedConnections | Нет | Нет |
> | applicationWhitelistings | Нет | Нет |
> | ассессментметадата | Нет | Нет |
> | оценки | Нет | Нет |
> | аутодисмиссалертсрулес | Нет | Нет |
> | инструментах автоматизации Composer | Yes | Yes |
> | AutoProvisioningSettings | Нет | Нет |
> | Compliances | Нет | Нет |
> | dataCollectionAgents | Нет | Нет |
> | девицесекуритиграупс | Нет | Нет |
> | discoveredSecuritySolutions | Нет | Нет |
> | externalSecuritySolutions | Нет | Нет |
> | InformationProtectionPolicies | Нет | Нет |
> | иотсекуритисолутионс | Yes | Yes |
> | Иотсекуритисолутионс/Аналитиксмоделс | Нет | Нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедалертс | Нет | Нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедрекоммендатионс | Нет | Нет |
> | jitNetworkAccessPolicies | Нет | Нет |
> | networkData | Нет | Нет |
> | политики | Нет | Нет |
> | pricings | Нет | Нет |
> | регулаторикомплианцестандардс | Нет | Нет |
> | Регулаторикомплианцестандардс/Регулаторикомплианцеконтролс | Нет | Нет |
> | Регулаторикомплианцестандардс/Регулаторикомплианцеконтролс/Регулаторикомплианцеассессментс | Нет | Нет |
> | securityContacts | Нет | Нет |
> | securitySolutions | Нет | Нет |
> | securitySolutionsReferenceData | Нет | Нет |
> | securityStatuses | Нет | Нет |
> | securityStatusesSummaries | Нет | Нет |
> | сервервулнерабилитяссессментс | Нет | Нет |
> | Параметры | Нет | Нет |
> | подоценка | Нет | Нет |
> | задачи; | Нет | Нет |
> | topologies | Нет | Нет |
> | workspaceSettings | Нет | Нет |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Нет | Нет |
> | diagnosticSettingsCategories | Нет | Нет |

## <a name="microsoftsecurityinsights"></a>Microsoft. Секуритинсигхтс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Агрегаты | Нет | Нет |
> | alertRules | Нет | Нет |
> | алертрулетемплатес | Нет | Нет |
> | закладки | Нет | Нет |
> | cases | Нет | Нет |
> | Подключения к компонентам | Нет | Нет |
> | Сущности | Нет | Нет |
> | ентитикуериес | Нет | Нет |
> | оффицеконсентс | Нет | Нет |
> | Параметры | Нет | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | namespaces | Yes | Нет |
> | пространства имен/authorizationrules | Нет | Нет |
> | пространства имен/дисастеррековериконфигс | Нет | Нет |
> | пространства имен/евентгридфилтерс | Нет | Нет |
> | пространства имен/нетворкрулесетс | Нет | Нет |
> | пространства имен и очереди | Нет | Нет |
> | пространства имен/очереди/authorizationrules | Нет | Нет |
> | пространства имен и разделы | Нет | Нет |
> | пространства имен/разделы/authorizationrules | Нет | Нет |
> | пространства имен, разделы и подписки | Нет | Нет |
> | пространства имен, разделы, подписки и правила | Нет | Нет |
> | premiumMessagingRegions | Нет | Нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | веб-масштабированием; | Yes | Yes |
> | clusters | Yes | Yes |
> | кластеры и приложения | Нет | Нет |
> | containerGroups | Yes | Yes |
> | контаинерграупсетс | Yes | Yes |
> | edgeclusters | Yes | Yes |
> | еджеклустерс и приложения | Нет | Нет |
> | networks | Yes | Yes |
> | secretstores | Yes | Yes |
> | секретсторес и сертификаты | Нет | Нет |
> | секретсторес/секреты | Нет | Нет |
> | volumes. | Yes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | веб-масштабированием; | Yes | Yes |
> | containerGroups | Yes | Yes |
> | gateways | Yes | Yes |
> | networks | Yes | Yes |
> | секретные коды | Yes | Yes |
> | volumes. | Yes | Yes |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Нет | Нет |
> | Провидеррегистратионс/Ресаурцетиперегистратионс | Нет | Нет |
> | rollouts | Yes | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SignalR | Yes | Yes |
> | SignalR/Евентгридфилтерс | Нет | Нет |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Yes | Yes |

## <a name="microsoftsoftwareplan"></a>Microsoft. Софтвареплан

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хибридусебенефитс | Нет | Нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Yes | Yes |
> | веб-масштабированием; | Yes | Yes |
> | jitRequests | Yes | Yes |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managedInstances | Yes | Yes |
> | Манажединстанцес и базы данных | Нет | Нет |
> | Манажединстанцес/databases/БаккупшорттермретентионполиЦиес | Нет | Нет |
> | Манажединстанцес/базы данных, схемы, таблицы, столбцы и Сенситивитилабелс | Нет | Нет |
> | Манажединстанцес/databases/Вулнерабилитяссессментс | Нет | Нет |
> | Манажединстанцес/базы данных/Вулнерабилитяссессментс/правила и базовые планы | Нет | Нет |
> | Манажединстанцес/Енкриптионпротектор | Нет | Нет |
> | Манажединстанцес и ключи | Нет | Нет |
> | Манажединстанцес/Ресторабледроппеддатабасес/БаккупшорттермретентионполиЦиес | Нет | Нет |
> | Манажединстанцес/Вулнерабилитяссессментс | Нет | Нет |
> | servers | Yes | Yes |
> | серверы и администраторы | Нет | Нет |
> | серверы и Коммуникатионлинкс | Нет | Нет |
> | серверы и базы данных | Да (см. [Примечание ниже](#sqlnote)) | Yes |
> | серверы и Енкриптионпротектор | Нет | Нет |
> | серверы и Фиреваллрулес | Нет | Нет |
> | серверы и ключи | Нет | Нет |
> | серверы и Ресторабледроппеддатабасес | Нет | Нет |
> | серверы и сервицеобжективес | Нет | Нет |
> | серверы и Тдецертификатес | Нет | Нет |
> | виртуалклустерс | Нет | Нет |

<a id="sqlnote" />

> [!NOTE]
> База данных Master не поддерживает теги, но другие базы данных, в том числе базы данных хранилища данных Azure SQL, поддерживают их. Базы данных Хранилища данных SQL Azure должны быть в активном состоянии (работа не приостановлена).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Yes | Yes |
> | Склвиртуалмачинеграупс/Аваилабилитиграуплистенерс | Нет | Нет |
> | SqlVirtualMachines | Yes | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Yes | Yes |
> | storageAccounts/Блобсервицес | Нет | Нет |
> | storageAccounts/Филесервицес | Нет | Нет |
> | storageAccounts/Куеуесервицес | Нет | Нет |
> | storageAccounts и службы | Нет | Нет |
> | storageAccounts/Services/metricDefinitions | Нет | Нет |
> | storageAccounts/Таблесервицес | Нет | Нет |
> | usages | Нет | Нет |

## <a name="microsoftstoragecache"></a>Microsoft. Сторажекаче

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | кэширует | Yes | Yes |
> | кэши/Сторажетаржетс | Нет | Нет |
> | усажемоделс | Нет | Нет |

## <a name="microsoftstoragereplication"></a>Microsoft. Сторажерепликатион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | репликатионграупс | Нет | Нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Yes | Yes |
> | Сторажесинксервицес/Регистередсерверс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | Нет | Нет |
> | Сторажесинксервицес и рабочие процессы | Нет | Нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Yes | Yes |
> | Сторажесинксервицес/Регистередсерверс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | Нет | Нет |
> | Сторажесинксервицес и рабочие процессы | Нет | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Yes | Yes |
> | Сторажесинксервицес/Регистередсерверс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | Нет | Нет |
> | Сторажесинксервицес и рабочие процессы | Нет | Нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managers | Yes | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Да (см. примечание ниже) | Yes |

> [!NOTE]
> Невозможно добавить тег при выполнении streamingjobs. Остановите ресурс, чтобы добавить тег.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Отмена | Нет | Нет |
> | CreateSubscription | Нет | Нет |
> | enable | Нет | Нет |
> | имени | Нет | Нет |
> | SubscriptionDefinitions | Нет | Нет |
> | SubscriptionOperations | Нет | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | environments | Yes | Нет |
> | среды и accessPolicies | Нет | Нет |
> | среды и классов EventSource | Yes | Нет |
> | среды и Референцедатасетс | Yes | Нет |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | дедикатедклауднодес | Yes | Yes |
> | дедикатедклаудсервицес | Yes | Yes |
> | virtualMachines | Yes | Yes |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Нет | Нет |
> | Апиманажементаккаунтс/списков ACL для | Нет | Нет |
> | Апиманажементаккаунтс/API | Нет | Нет |
> | Апиманажементаккаунтс/API/списков ACL для | Нет | Нет |
> | Апиманажементаккаунтс/API/списков ACL для | Нет | Нет |
> | Апиманажементаккаунтс/API/подключения | Нет | Нет |
> | Апиманажементаккаунтс/API/Connections/списков ACL для | Нет | Нет |
> | Апиманажементаккаунтс/API/Локализеддефинитионс | Нет | Нет |
> | Апиманажементаккаунтс/списков ACL для | Нет | Нет |
> | Апиманажементаккаунтс и подключения | Нет | Нет |
> | billingMeters | Нет | Нет |
> | certificates | Yes | Yes |
> | connectionGateways | Yes | Yes |
> | connections | Yes | Yes |
> | customApis | Yes | Yes |
> | deletedSites | Нет | Нет |
> | functions | Нет | Нет |
> | hostingEnvironments | Yes | Yes |
> | hostingEnvironments/Мултиролепулс | Нет | Нет |
> | hostingEnvironments/внешнего размещения | Нет | Нет |
> | publishingUsers | Нет | Нет |
> | к просмотру фильмов | Нет | Нет |
> | resourceHealthMetadata | Нет | Нет |
> | runtimes | Нет | Нет |
> | serverFarms | Yes | Yes |
> | serverFarms/Евентгридфилтерс | Нет | Нет |
> | sites | Yes | Yes |
> | сайты и конфигурация  | Нет | Нет |
> | Sites/Евентгридфилтерс | Нет | Нет |
> | Sites/Хостнамебиндингс | Нет | Нет |
> | Sites/файл networkconfig | Нет | Нет |
> | Sites/премиераддонс | Yes | Yes |
> | сайты и слоты | Yes | Yes |
> | сайты/слоты/Евентгридфилтерс | Нет | Нет |
> | сайты/слоты/Хостнамебиндингс | Нет | Нет |
> | сайты/слоты/файл networkconfig | Нет | Нет |
> | sourceControls | Нет | Нет |
> | validate | Нет | Нет |
> | verifyHostingEnvironmentVnet | Нет | Нет |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Нет | Нет |
> | diagnosticSettingsCategories | Нет | Нет |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Yes | Yes |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | components | Нет | Нет |
> | componentsSummary | Нет | Нет |
> | monitorInstances | Нет | Нет |
> | monitorInstancesSummary | Нет | Нет |
> | monitors | Нет | Нет |
> | notificationSettings | Нет | Нет |

## <a name="next-steps"></a>Дополнительная информация

Сведения о том, как применить теги к ресурсам, см. в статье, посвященной [использованию тегов для организации ресурсов Azure](resource-group-using-tags.md).
