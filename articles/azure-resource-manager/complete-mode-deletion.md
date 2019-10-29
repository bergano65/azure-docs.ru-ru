---
title: Azure Resource Manager полного удаления режима
description: Здесь показано, как происходит удаление ресурсов в полном режиме в шаблонах Azure Resource Manager по типу ресурса.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/27/2019
ms.author: tomfitz
ms.openlocfilehash: 03e91e4be967e822d17144af848f51e73851b1e6
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969178"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Удаление ресурсов Azure для развертываний в полном режиме

В этой статье описывается, как процесс удаления будет обрабатываться различными типами, когда он не происходит в шаблоне, развернутом в завершенном режиме.

Типы ресурсов, помеченные **Да** , удаляются, если тип не находится в шаблоне, развернутом в полном режиме.

Типы ресурсов, помеченные как " **нет** ", не удаляются автоматически при отсутствии в шаблоне. Однако они удаляются, если родительский ресурс удаляется. Чтобы получить полное описание поведения см. статью [Режимы развертывания в Azure Resource Manager](deployment-modes.md).

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
> - [Microsoft. Гуестконфигуратион](#microsoftguestconfiguration)
> - [Microsoft. Ханаоназуре](#microsofthanaonazure)
> - [Microsoft. Хардваресекуритимодулес](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft. Хеалскареапис](#microsofthealthcareapis)
> - [Microsoft. Хибридкомпуте](#microsofthybridcompute)
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
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | DomainServices | ДА |
> | DomainServices/oucontainer | Нет |
> | DomainServices/Репликасетс | ДА |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | supportProviders | Нет |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | aadsupportcases | Нет |
> | addsservices | Нет |
> | agents | Нет |
> | anonymousapiusers | Нет |
> | Конфигурация | Нет |
> | журналы | Нет |
> | reports | Нет |
> | servicehealthmetrics | Нет |
> | services; | Нет |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | конфигурации | Нет |
> | generateRecommendations | Нет |
> | metadata | Нет |
> | к просмотру фильмов | Нет |
> | suppressions | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | actionRules | ДА |
> | оповещения | Нет |
> | alertsList | Нет |
> | алертсметадата | Нет |
> | alertsSummary | Нет |
> | alertsSummaryList | Нет |
> | Оставлять | Нет |
> | smartDetectorAlertRules | ДА |
> | smartDetectorRuntimeEnvironments | Нет |
> | smartGroups | Нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | серверы | ДА |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | reportFeedback | Нет |
> | Служба | ДА |
> | validateServiceName | Нет |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | конфигуратионсторес | ДА |
> | Конфигуратионсторес/Евентгридфилтерс | Нет |

## <a name="microsoftappplatform"></a>Microsoft. Аппплатформ

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Spring | ДА |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | attestationProviders | Нет |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | classicAdministrators | Нет |
> | Псевдонимы | Нет |
> | denyAssignments | Нет |
> | elevateAccess | Нет |
> | финдорфанролеассигнментс | Нет |
> | locks | Нет |
> | permissions | Нет |
> | policyAssignments | Нет |
> | policyDefinitions | Нет |
> | policySetDefinitions | Нет |
> | providerOperations | Нет |
> | roleAssignments | Нет |
> | roleDefinitions | Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | automationAccounts | ДА |
> | automationAccounts/configurations | ДА |
> | automationAccounts/jobs | Нет |
> | automationAccounts/runbooks | ДА |
> | automationAccounts/softwareUpdateConfigurations | Нет |
> | automationAccounts/webhooks | Нет |

## <a name="microsoftazconfig"></a>Microsoft. Азконфиг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | конфигуратионсторес | ДА |
> | Конфигуратионсторес/Евентгридфилтерс | Нет |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | environments | Нет |
> | environments/accounts | Нет |
> | environments/accounts/namespaces | Нет |
> | environments/accounts/namespaces/configurations | Нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | b2cDirectories | ДА |
> | b2ctenants | Нет |

## <a name="microsoftazuredata"></a>Microsoft. Азуредата

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | хибриддатаманажерс | ДА |
> | постгресинстанцес | ДА |
> | склбигдатаклустерс | ДА |
> | склинстанцес | ДА |
> | склсерверрегистратионс | ДА |
> | Склсерверрегистратионс и sqlServer | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | registrations | ДА |
> | registrations/customerSubscriptions | Нет |
> | registrations/products | Нет |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | batchAccounts | ДА |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | billingAccounts | Нет |
> | Биллингаккаунтс и соглашения | Нет |
> | Биллингаккаунтс/Биллингпермиссионс | Нет |
> | billingAccounts/billingProfiles | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингпермиссионс | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингролеассигнментс | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингроледефинитионс | Нет |
> | billingAccounts/billingProfiles/billingSubscriptions | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Креатебиллингролеассигнмент | Нет |
> | Биллингаккаунтс/Биллингпрофилес/клиенты | Нет |
> | billingAccounts/billingProfiles/invoices | Нет |
> | billingAccounts/billingProfiles/invoices/pricesheet | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингпермиссионс | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингролеассигнментс | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингроледефинитионс | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингсубскриптионс | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Креатебиллингролеассигнмент | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Инитиатетрансфер | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products/перемещение | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products/Упдатеауторенев | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/транзакции | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/передача | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Патчоператионс | Нет |
> | billingAccounts/billingProfiles/paymentMethods | Нет |
> | billingAccounts/billingProfiles/policies | Нет |
> | billingAccounts/billingProfiles/pricesheet | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Прицешитдовнлоадоператионс | Нет |
> | billingAccounts/billingProfiles/products | Нет |
> | billingAccounts/billingProfiles/transactions | Нет |
> | Биллингаккаунтс/Биллингролеассигнментс | Нет |
> | Биллингаккаунтс/Биллингроледефинитионс | Нет |
> | billingAccounts/billingSubscriptions | Нет |
> | Биллингаккаунтс/Креатебиллингролеассигнмент | Нет |
> | Биллингаккаунтс/Креатеинвоицесектионоператионс | Нет |
> | Биллингаккаунтс и клиенты | Нет |
> | Биллингаккаунтс/Customers/Биллингпермиссионс | Нет |
> | Биллингаккаунтс/Customers/Биллингсубскриптионс | Нет |
> | Биллингаккаунтс/Customers/Инитиатетрансфер | Нет |
> | Биллингаккаунтс, клиенты и политики | Нет |
> | Биллингаккаунтс/Customers/Products | Нет |
> | Биллингаккаунтс/Customers/Transactions | Нет |
> | Биллингаккаунтс/Customers/Transfers | Нет |
> | billingAccounts/departments | Нет |
> | billingAccounts/enrollmentAccounts | Нет |
> | billingAccounts/invoices | Нет |
> | billingAccounts/invoiceSections | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионмовеоператионс | Нет |
> | billingAccounts/invoiceSections/billingSubscriptions | Нет |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Нет |
> | Биллингаккаунтс/Инвоицесектионс/повышение прав | Нет |
> | billingAccounts/invoiceSections/initiateTransfer | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Патчоператионс | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Продуктмовеоператионс | Нет |
> | billingAccounts/invoiceSections/products | Нет |
> | Биллингаккаунтс/Инвоицесектионс/продукты/перемещение | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Products/Упдатеауторенев | Нет |
> | Биллингаккаунтс/Инвоицесектионс/транзакции | Нет |
> | billingAccounts/invoiceSections/transfers | Нет |
> | Биллингаккаунтс/Линеофкредит | Нет |
> | Биллингаккаунтс/Патчоператионс | Нет |
> | Биллингаккаунтс/Пайментмесодс | Нет |
> | billingAccounts/products | Нет |
> | billingAccounts/transactions | Нет |
> | billingPeriods | Нет |
> | биллингпермиссионс | Нет |
> | billingProperty | Нет |
> | биллингролеассигнментс | Нет |
> | биллингроледефинитионс | Нет |
> | креатебиллингролеассигнмент | Нет |
> | departments | Нет |
> | enrollmentAccounts | Нет |
> | invoices | Нет |
> | transfers | Нет |
> | transfers/acceptTransfer | Нет |
> | transfers/declineTransfer | Нет |
> | transfers/operationStatus | Нет |
> | передачи/Валидатетрансфер | Нет |
> | валидатеаддресс | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | mapApis | ДА |
> | updateCommunicationPreference | Нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | блоккчаинмемберс | ДА |
> | кордамемберс | ДА |
> | наблюдатели | ДА |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | blueprintAssignments | Нет |
> | blueprintAssignments/assignmentOperations | Нет |
> | blueprintAssignments/operations | Нет |
> | blueprints | Нет |
> | blueprints/artifacts | Нет |
> | blueprints/versions | Нет |
> | blueprints/versions/artifacts | Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | botServices | ДА |
> | botServices/channels | Нет |
> | botServices/connections | Нет |
> | языки | Нет |
> | шаблоны | Нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Redis | ДА |
> | RedisConfigDefinition | Нет |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | appliedReservations | Нет |
> | калкулатиксчанже | Нет |
> | calculatePrice | Нет |
> | калкулатепурчасеприце | Нет |
> | catalogs | Нет |
> | commercialReservationOrders | Нет |
> | exchange | Нет |
> | плацепурчасеордер | Нет |
> | reservationOrders | Нет |
> | reservationOrders/calculateRefund | Нет |
> | reservationOrders/merge | Нет |
> | reservationOrders/reservations | Нет |
> | reservationOrders/reservations/revisions | Нет |
> | reservationOrders/return | Нет |
> | reservationOrders/split | Нет |
> | reservationOrders/swap | Нет |
> | reservations | Нет |
> | ресурсов | Нет |
> | validateReservationOrder | Нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | кднвебаппликатионфиреваллманажедрулесетс | Нет |
> | кднвебаппликатионфиреваллполиЦиес | ДА |
> | edgenodes | Нет |
> | профили | ДА |
> | profiles/endpoints | ДА |
> | profiles/endpoints/customdomains | Нет |
> | profiles/endpoints/origins | Нет |
> | validateProbe | Нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | certificateOrders | ДА |
> | certificateOrders/certificates | Нет |
> | validateCertificateRegistrationInformation | Нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capabilities | Нет |
> | domainNames | ДА |
> | domainNames/capabilities | Нет |
> | domainNames/internalLoadBalancers | Нет |
> | domainNames/serviceCertificates | Нет |
> | domainNames/slots | Нет |
> | domainNames/slots/roles | Нет |
> | имя_домена/слоты/роли/metricDefinitions | Нет |
> | имя_домена/слоты/роли/метрики | Нет |
> | moveSubscriptionResources | Нет |
> | operatingSystemFamilies | Нет |
> | operatingSystems | Нет |
> | quotas | Нет |
> | resourceTypes | Нет |
> | validateSubscriptionMoveAvailability | Нет |
> | virtualMachines | ДА |
> | virtualMachines/diagnosticSettings | Нет |
> | virtualMachines/metricDefinitions | Нет |
> | virtualMachines/metrics | Нет |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | classicInfrastructureResources | Нет |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capabilities | Нет |
> | expressRouteCrossConnections | Нет |
> | expressRouteCrossConnections/peerings | Нет |
> | gatewaySupportedDevices | Нет |
> | networkSecurityGroups | ДА |
> | quotas | Нет |
> | reservedIps | ДА |
> | virtualNetworks | ДА |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Нет |
> | virtualNetworks/virtualNetworkPeerings | Нет |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capabilities | Нет |
> | disks | Нет |
> | images | Нет |
> | osImages | Нет |
> | osPlatformImages | Нет |
> | publicImages | Нет |
> | quotas | Нет |
> | storageAccounts | ДА |
> | storageAccounts/blobServices | Нет |
> | storageAccounts/fileServices | Нет |
> | storageAccounts/metricDefinitions | Нет |
> | storageAccounts/метрики | Нет |
> | storageAccounts/queueServices | Нет |
> | storageAccounts/services | Нет |
> | storageAccounts/services/diagnosticSettings | Нет |
> | storageAccounts/services/metricDefinitions | Нет |
> | storageAccounts/Services/метрики | Нет |
> | storageAccounts/tableServices | Нет |
> | storageAccounts/vmImages | Нет |
> | vmImages | Нет |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | ДА |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | RateCard | Нет |
> | UsageAggregates | Нет |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | availabilitySets | ДА |
> | дискенкриптионсетс | ДА |
> | disks | ДА |
> | galleries | ДА |
> | коллекции и приложения | Нет |
> | коллекции, приложения и версии | Нет |
> | galleries/images | Нет |
> | galleries/images/versions | Нет |
> | хостграупс | ДА |
> | Хостграупс и узлы | ДА |
> | images | ДА |
> | проксимитиплацементграупс | ДА |
> | restorePointCollections | ДА |
> | restorePointCollections/restorePoints | Нет |
> | шаредвмекстенсионс | ДА |
> | Шаредвмекстенсионс и версии | Нет |
> | sharedVMImages | ДА |
> | sharedVMImages/versions | Нет |
> | snapshots | ДА |
> | virtualMachines | ДА |
> | virtualMachines/extensions | ДА |
> | virtualMachines/metricDefinitions | Нет |
> | virtualMachineScaleSets | ДА |
> | virtualMachineScaleSets/extensions | Нет |
> | virtualMachineScaleSets/networkInterfaces | Нет |
> | virtualMachineScaleSets/publicIPAddresses | Нет |
> | virtualMachineScaleSets/virtualMachines | Нет |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Нет |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | AggregatedCost | Нет |
> | сведения о балансе. | Нет |
> | Расходы | Нет |
> | Расходы | Нет |
> | CostTags | Нет |
> | credits | Нет |
> | events | Нет |
> | Прогнозы | Нет |
> | lots | Нет |
> | Marketplace | Нет |
> | Pricesheets | Нет |
> | products | Нет |
> | ReservationDetails | Нет |
> | ReservationRecommendations | Нет |
> | ReservationSummaries | Нет |
> | ReservationTransactions | Нет |
> | Теги | Нет |
> | tenants | Нет |
> | Термины | Нет |
> | UsageDetails | Нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | containerGroups | ДА |
> | serviceAssociationLinks | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | registries | ДА |
> | registries/builds | Нет |
> | registries/builds/cancel | Нет |
> | registries/builds/getLogLink | Нет |
> | registries/buildTasks | ДА |
> | registries/buildTasks/steps | Нет |
> | registries/eventGridFilters | Нет |
> | реестры и Женератекредентиалс | Нет |
> | registries/getBuildSourceUploadUrl | Нет |
> | registries/GetCredentials | Нет |
> | registries/importImage | Нет |
> | registries/queueBuild | Нет |
> | registries/regenerateCredential | Нет |
> | registries/regenerateCredentials | Нет |
> | registries/replications | ДА |
> | registries/runs | Нет |
> | registries/runs/cancel | Нет |
> | registries/scheduleRun | Нет |
> | реестры и Скопемапс | Нет |
> | registries/tasks | ДА |
> | реестры и маркеры | Нет |
> | registries/updatePolicies | Нет |
> | registries/webhooks | ДА |
> | registries/webhooks/getCallbackConfig | Нет |
> | registries/webhooks/ping | Нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | containerServices | ДА |
> | managedClusters | ДА |
> | опеншифтманажедклустерс | ДА |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | ДА |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Оповещения | Нет |
> | BillingAccounts | Нет |
> | Расходы | Нет |
> | клаудконнекторс | Нет |
> | Коннекторы | ДА |
> | Departments | Нет |
> | Измерения | Нет |
> | EnrollmentAccounts | Нет |
> | Экспорт | Нет |
> | екстерналбиллингаккаунтс | Нет |
> | Екстерналбиллингаккаунтс и оповещения | Нет |
> | Екстерналбиллингаккаунтс и измерения | Нет |
> | Екстерналбиллингаккаунтс/прогноз | Нет |
> | Екстерналбиллингаккаунтс/запрос | Нет |
> | екстерналсубскриптионс | Нет |
> | Екстерналсубскриптионс и оповещения | Нет |
> | Екстерналсубскриптионс и измерения | Нет |
> | Екстерналсубскриптионс/прогноз | Нет |
> | Екстерналсубскриптионс/запрос | Нет |
> | Грешно | Нет |
> | Запрос | Нет |
> | регистрация | Нет |
> | Reportconfigs | Нет |
> | Отчеты | Нет |
> | Настройки | Нет |
> | шовбаккрулес | Нет |
> | Представления | Нет |

## <a name="microsoftcustomerlockbox"></a>Microsoft. Кустомерлоккбокс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | requests | Нет |

## <a name="microsoftcustomproviders"></a>Microsoft. Кустомпровидерс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | связи | Нет |
> | ресаурцепровидерс | ДА |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | jobs | ДА |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | ДА |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | workspaces | ДА |
> | workspaces/virtualNetworkPeerings | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | catalogs | ДА |
> | каталоги | ДА |
> | каталоги данных и источники данных | Нет |
> | каталоги данных, источники данных и проверки | Нет |
> | каталоги данных/DataSources/scans/DataSets | Нет |
> | каталоги данных, источники данных, проверки и триггеры | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | dataFactories | ДА |
> | dataFactories/diagnosticSettings | Нет |
> | dataFactories/metricDefinitions | Нет |
> | dataFactorySchema | Нет |
> | factories | ДА |
> | factories/integrationRuntimes | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | ДА |
> | accounts/dataLakeStoreAccounts | Нет |
> | accounts/storageAccounts | Нет |
> | accounts/storageAccounts/containers | Нет |
> | Accounts/Трансфераналитиксунитс | Нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | ДА |
> | accounts/eventGridFilters | Нет |
> | accounts/firewallRules | Нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | services; | ДА |
> | services/projects | ДА |

## <a name="microsoftdatashare"></a>Общая папка Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | ДА |
> | учетные записи и общие папки | Нет |
> | учетные записи, общие ресурсы и наборы данных | Нет |
> | учетные записи, общие ресурсы и приглашения | Нет |
> | Accounts/shares/провидершаресубскриптионс | Нет |
> | Accounts/shares/Синчронизатионсеттингс | Нет |
> | Accounts/шаресубскриптионс | Нет |
> | Accounts/шаресубскриптионс/Консумерсаурцедатасетс | Нет |
> | Accounts/шаресубскриптионс/датасетмаппингс | Нет |
> | учетные записи/шаресубскриптионс/триггеры | Нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | серверы | ДА |
> | servers/advisors | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет |
> | серверы и Привателинкресаурцес | Нет |
> | servers/queryTexts | Нет |
> | servers/recoverableServers | Нет |
> | servers/topQueryStatistics | Нет |
> | servers/virtualNetworkRules | Нет |
> | servers/waitStatistics | Нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | серверы | ДА |
> | servers/advisors | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет |
> | серверы и Привателинкресаурцес | Нет |
> | servers/queryTexts | Нет |
> | servers/recoverableServers | Нет |
> | servers/topQueryStatistics | Нет |
> | servers/virtualNetworkRules | Нет |
> | servers/waitStatistics | Нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | серверграупс | ДА |
> | серверы | ДА |
> | servers/advisors | Нет |
> | servers/keys | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет |
> | серверы и Привателинкресаурцес | Нет |
> | servers/queryTexts | Нет |
> | servers/recoverableServers | Нет |
> | servers/topQueryStatistics | Нет |
> | servers/virtualNetworkRules | Нет |
> | servers/waitStatistics | Нет |
> | serversv2 | ДА |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | артифактсаурцес | ДА |
> | rollouts | ДА |
> | сервицетопологиес | ДА |
> | Сервицетопологиес и службы | ДА |
> | Сервицетопологиес/Services/Сервицеунитс | ДА |
> | steps | ДА |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. Десктопвиртуализатион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | аппликатионграупс | ДА |
> | аппликатионграупс и приложения | Нет |
> | аппликатионграупс и настольные системы | Нет |
> | аппликатионграупс/стартменуитемс | Нет |
> | хостпулс | ДА |
> | хостпулс/сессионхостс | Нет |
> | хостпулс/сессионхостс/усерсессионс | Нет |
> | хостпулс/усерсессионс | Нет |
> | workspaces | ДА |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | еластикпулс | ДА |
> | Еластикпулс/Иосубтенантс | ДА |
> | IotHubs | ДА |
> | IotHubs/eventGridFilters | Нет |
> | ProvisioningServices | ДА |
> | usages | Нет |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Конвейеров | ДА |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | controllers | ДА |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | labcenters | ДА |
> | labs | ДА |
> | лаборатории и среды | ДА |
> | labs/serviceRunners | ДА |
> | labs/virtualMachines | ДА |
> | schedules | ДА |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | databaseAccountNames | Нет |
> | databaseAccounts | ДА |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | domains | ДА |
> | domains/domainOwnershipIdentifiers | Нет |
> | generateSsoRequest | Нет |
> | topLevelDomains | Нет |
> | validateDomainRegistrationInformation | Нет |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | lcsprojects | Нет |
> | lcsprojects/clouddeployments | Нет |
> | lcsprojects/connectors | Нет |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | services; | ДА |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | domains | ДА |
> | domains/topics | Нет |
> | eventSubscriptions | Нет |
> | extensionTopics | Нет |
> | topics | ДА |
> | topicTypes | Нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | ДА |
> | namespaces | ДА |
> | namespaces/authorizationrules | Нет |
> | namespaces/disasterrecoveryconfigs | Нет |
> | namespaces/eventhubs | Нет |
> | namespaces/eventhubs/authorizationrules | Нет |
> | namespaces/eventhubs/consumergroups | Нет |
> | пространства имен/нетворкрулесетс | Нет |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | эластичной базы данных | Нет |
> | providers | Нет |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | enroll | Нет |
> | galleryitems | Нет |
> | generateartifactaccessuri | Нет |
> | myareas | Нет |
> | myareas/areas | Нет |
> | myareas/areas/areas | Нет |
> | myareas/areas/areas/galleryitems | Нет |
> | myareas/areas/galleryitems | Нет |
> | myareas/galleryitems | Нет |
> | регистрация | Нет |
> | ресурсов | Нет |
> | retrieveresourcesbyid | Нет |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | ДА |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | аутоманажедвмконфигуратионпрофилес | ДА |
> | конфигуратионпрофилеассигнментс | Нет |
> | guestConfigurationAssignments | Нет |
> | software | Нет |
> | софтвареупдатепрофиле | Нет |
> | софтвареупдатес | Нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | hanaInstances | ДА |
> | сапмониторс | ДА |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | дедикатедхсмс | ДА |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | ДА |
> | clusters/applications | Нет |

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | services; | ДА |

## <a name="microsofthybridcompute"></a>Microsoft. Хибридкомпуте

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | виртуальных | ДА |
> | Компьютеры и расширения | ДА |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Диспетчеры | ДА |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | components | ДА |
> | нетворкскопес | ДА |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | jobs | ДА |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | diagnosticSettings | Нет |
> | diagnosticSettingsCategories | Нет |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | апптемплатес | Нет |
> | IoTApps | ДА |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Граф | ДА |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | deletedVaults | Нет |
> | хсмпулс | ДА |
> | vaults | ДА |
> | vaults/accessPolicies | Нет |
> | хранилища и Евентгридфилтерс | Нет |
> | vaults/secrets | Нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | ДА |
> | кластеры/аттачеддатабасеконфигуратионс | Нет |
> | clusters/databases | Нет |
> | clusters/databases/dataconnections | Нет |
> | clusters/databases/eventhubconnections | Нет |
> | кластеры/шаредидентитиес | Нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | labaccounts | ДА |
> | пользователей | Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | hostingEnvironments | ДА |
> | integrationAccounts | ДА |
> | интегратионсервицеенвиронментс | ДА |
> | Интегратионсервицеенвиронментс/Манажедапис | ДА |
> | исолатеденвиронментс | ДА |
> | workflows | ДА |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | commitmentPlans | ДА |
> | webServices | ДА |
> | Рабочие области | ДА |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | workspaces | ДА |
> | workspaces/computes | Нет |
> | рабочие области и Евентгридфилтерс | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Identities | Нет |
> | userAssignedIdentities | ДА |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | маркетплацерегистратиондефинитионс | Нет |
> | регистратионассигнментс | Нет |
> | регистратиондефинитионс | Нет |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | getEntities | Нет |
> | managementGroups | Нет |
> | ресурсов | Нет |
> | startTenantBackfill | Нет |
> | tenantBackfillStatus | Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | ДА |
> | accounts/eventGridFilters | Нет |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | offers | Нет |
> | offerTypes | Нет |
> | offerTypes/publishers | Нет |
> | offerTypes/publishers/offers | Нет |
> | offerTypes/publishers/offers/plans | Нет |
> | offerTypes/publishers/offers/plans/agreements | Нет |
> | offerTypes/publishers/offers/plans/configs | Нет |
> | offerTypes/publishers/offers/plans/configs/importImage | Нет |
> | privategalleryitems | Нет |
> | products | Нет |
> | издателя | Нет |
> | издатели и предложения | Нет |
> | издатели, предложения и поправки | Нет |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | classicDevServices | ДА |
> | updateCommunicationPreference | Нет |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | agreements | Нет |
> | offertypes | Нет |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | mediaservices | ДА |
> | mediaservices/accountFilters | Нет |
> | mediaservices/assets | Нет |
> | mediaservices/assets/assetFilters | Нет |
> | mediaservices/contentKeyPolicies | Нет |
> | mediaservices/eventGridFilters | Нет |
> | mediaservices/liveEventOperations | Нет |
> | mediaservices/liveEvents | ДА |
> | mediaservices/liveEvents/liveOutputs | Нет |
> | mediaservices/liveOutputOperations | Нет |
> | mediaservices/Медиаграфс | Нет |
> | mediaservices/streamingEndpointOperations | Нет |
> | mediaservices/streamingEndpoints | ДА |
> | mediaservices/streamingLocators | Нет |
> | mediaservices/streamingPolicies | Нет |
> | mediaservices/transforms | Нет |
> | mediaservices/transforms/jobs | Нет |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | аппклустерс | ДА |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | ассессментпрожектс | ДА |
> | migrateprojects | ДА |
> | projects | ДА |

## <a name="microsoftmixedreality"></a>Microsoft. Микседреалити

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | холографиксброадкастаккаунтс | ДА |
> | обжектундерстандингаккаунтс | ДА |
> | ремотерендерингаккаунтс | ДА |
> | спатиаланчорсаккаунтс | ДА |
> | сурфацереконструктионаккаунтс | ДА |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | нетаппаккаунтс | ДА |
> | Нетаппаккаунтс/БаккупполиЦиес | ДА |
> | Нетаппаккаунтс/КапаЦитипулс | ДА |
> | Нетаппаккаунтс/КапаЦитипулс/тома | ДА |
> | Нетаппаккаунтс/КапаЦитипулс/тома/резервные копии | Нет |
> | Нетаппаккаунтс/КапаЦитипулс/Volumes/Маунттаржетс | ДА |
> | Нетаппаккаунтс/КапаЦитипулс/тома/моментальные снимки | ДА |
> | Нетаппаккаунтс и хранилища | Нет |
## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | applicationGateways | ДА |
> | аппликатионгатевайвебаппликатионфиреваллполиЦиес | ДА |
> | applicationSecurityGroups | ДА |
> | azureFirewallFqdnTags | Нет |
> | azureFirewalls | ДА |
> | бастионхостс | ДА |
> | bgpServiceCommunities | Нет |
> | connections | ДА |
> | ddosCustomPolicies | ДА |
> | ddosProtectionPlans | ДА |
> | dnsOperationStatuses | Нет |
> | dnszones | ДА |
> | dnszones/A | Нет |
> | dnszones/AAAA | Нет |
> | dnszones/all | Нет |
> | dnszones/CAA | Нет |
> | dnszones/CNAME | Нет |
> | dnszones/MX | Нет |
> | dnszones/NS | Нет |
> | dnszones/PTR | Нет |
> | dnszones/recordsets | Нет |
> | dnszones/SOA | Нет |
> | dnszones/SRV | Нет |
> | dnszones/TXT | Нет |
> | expressRouteCircuits | ДА |
> | expressRouteCrossConnections | ДА |
> | експрессраутегатевайс | ДА |
> | експрессраутепортс | ДА |
> | expressRouteServiceProviders | Нет |
> | фиреваллполиЦиес | ДА |
> | frontdoors | ДА |
> | фронтдурвебаппликатионфиреваллманажедрулесетс | Нет |
> | frontdoorWebApplicationFirewallPolicies | ДА |
> | getDnsResourceReference | Нет |
> | internalNotify | Нет |
> | loadBalancers | ДА |
> | localNetworkGateways | ДА |
> | natGateways | ДА |
> | networkIntentPolicies | ДА |
> | networkInterfaces | ДА |
> | networkProfiles | ДА |
> | networkSecurityGroups | ДА |
> | networkWatchers | ДА |
> | networkWatchers/connectionMonitors | ДА |
> | networkWatchers/lenses | ДА |
> | networkWatchers/pingMeshes | ДА |
> | p2sVpnGateways | ДА |
> | приватеднсоператионстатусес | Нет |
> | приватеднсзонес | ДА |
> | Приватеднсзонес/A | Нет |
> | Приватеднсзонес/AAAA | Нет |
> | Приватеднсзонес/все | Нет |
> | Приватеднсзонес и CNAME | Нет |
> | Приватеднсзонес/MX | Нет |
> | Приватеднсзонес/PTR | Нет |
> | Приватеднсзонес/SOA | Нет |
> | Приватеднсзонес/SRV | Нет |
> | Приватеднсзонес/TXT | Нет |
> | Приватеднсзонес/Виртуалнетворклинкс | ДА |
> | приватиндпоинтс | ДА |
> | privateLinkServices | ДА |
> | publicIPAddresses | ДА |
> | publicIPPrefixes | ДА |
> | routeFilters | ДА |
> | routeTables | ДА |
> | секурегатевайс | ДА |
> | serviceEndpointPolicies | ДА |
> | trafficManagerGeographicHierarchies | Нет |
> | trafficmanagerprofiles | ДА |
> | trafficmanagerprofiles/heatMaps | Нет |
> | траффикманажерусерметрикскэйс | Нет |
> | virtualHubs | ДА |
> | virtualNetworkGateways | ДА |
> | virtualNetworks | ДА |
> | virtualNetworkTaps | ДА |
> | virtualWans | ДА |
> | vpnGateways | ДА |
> | vpnSites | ДА |
> | webApplicationFirewallPolicies | ДА |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | namespaces | ДА |
> | namespaces/notificationHubs | ДА |

## <a name="microsoftobjectstore"></a>Microsoft. Обжектсторе

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | оснамеспацес | ДА |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | хипервситес | ДА |
> | импортситес | ДА |
> | серверситес | ДА |
> | вмвареситес | ДА |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | ДА |
> | устройства | Нет |
> | linkTargets | Нет |
> | storageInsightConfigs | Нет |
> | workspaces | ДА |
> | workspaces/dataSources | Нет |
> | workspaces/linkedServices | Нет |
> | workspaces/query | Нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managementassociations | Нет |
> | managementconfigurations | ДА |
> | solutions | ДА |
> | узел "Представления" | ДА |

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | легаципирингс | Нет |
> | пираснс | Нет |
> | пиринги | ДА |
> | пирингсервицепровидерс | Нет |
> | пирингсервицес | ДА |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | policyEvents | Нет |
> | полициметадата | Нет |
> | policyStates | Нет |
> | policyTrackedResources | Нет |
> | remediations | Нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | consoles | Нет |
> | dashboards | ДА |
> | userSettings | Нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | workspaceCollections | ДА |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capacities | ДА |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | backupProtectedItems | Нет |
> | vaults | ДА |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | namespaces | ДА |
> | namespaces/authorizationrules | Нет |
> | namespaces/hybridconnections | Нет |
> | namespaces/hybridconnections/authorizationrules | Нет |
> | namespaces/wcfrelays | Нет |
> | namespaces/wcfrelays/authorizationrules | Нет |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Нет |
> | Семейства | ДА |
> | коллекции и приложения | Нет |
> | Collections/секуритипринЦипалс | Нет |
> | темплатеимажес | Нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | запросы | ДА |
> | ресаурцечанжедетаилс | Нет |
> | ресаурцечанжес | Нет |
> | ресурсов | Нет |
> | ресаурцешистори | Нет |
> | subscriptionsStatus | Нет |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | availabilityStatuses | Нет |
> | childAvailabilityStatuses | Нет |
> | childResources | Нет |
> | events | Нет |
> | impactedResources | Нет |
> | metadata | Нет |
> | Уведомления | Нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | deployments | Нет |
> | deployments/operations | Нет |
> | deploymentScripts | ДА |
> | deploymentScripts и журналы | Нет |
> | links | Нет |
> | notifyResourceJobs | Нет |
> | providers | Нет |
> | resourceGroups | Нет |
> | ресурсов | Нет |
> | subscriptions | Нет |
> | subscriptions/providers | Нет |
> | subscriptions/resourceGroups | Нет |
> | subscriptions/resourcegroups/resources | Нет |
> | subscriptions/resources | Нет |
> | subscriptions/tagnames | Нет |
> | subscriptions/tagNames/tagValues | Нет |
> | tenants | Нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | приложений | ДА |
> | saasresources | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | jobcollections | ДА |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | resourceHealthMetadata | Нет |
> | searchServices | ДА |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | адаптивенетворкхарденингс | Нет |
> | advancedThreatProtectionSettings | Нет |
> | оповещения | Нет |
> | allowedConnections | Нет |
> | applicationWhitelistings | Нет |
> | ассессментметадата | Нет |
> | оценки | Нет |
> | инструментах автоматизации Composer | ДА |
> | AutoProvisioningSettings | Нет |
> | Compliances | Нет |
> | dataCollectionAgents | Нет |
> | девицесекуритиграупс | Нет |
> | discoveredSecuritySolutions | Нет |
> | externalSecuritySolutions | Нет |
> | InformationProtectionPolicies | Нет |
> | иотсекуритисолутионс | ДА |
> | Иотсекуритисолутионс/Аналитиксмоделс | Нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедалертс | Нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедрекоммендатионс | Нет |
> | jitNetworkAccessPolicies | Нет |
> | networkData | Нет |
> | плайбукконфигуратионс | ДА |
> | политики | Нет |
> | pricings | Нет |
> | регулаторикомплианцестандардс | Нет |
> | Регулаторикомплианцестандардс/Регулаторикомплианцеконтролс | Нет |
> | Регулаторикомплианцестандардс/Регулаторикомплианцеконтролс/Регулаторикомплианцеассессментс | Нет |
> | securityContacts | Нет |
> | securitySolutions | Нет |
> | securitySolutionsReferenceData | Нет |
> | securityStatuses | Нет |
> | securityStatusesSummaries | Нет |
> | сервервулнерабилитяссессментс | Нет |
> | Параметры | Нет |
> | подоценка | Нет |
> | задачи; | Нет |
> | topologies | Нет |
> | workspaceSettings | Нет |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | diagnosticSettings | Нет |
> | diagnosticSettingsCategories | Нет |

## <a name="microsoftsecurityinsights"></a>Microsoft. Секуритинсигхтс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Агрегаты | Нет |
> | alertRules | Нет |
> | алертрулетемплатес | Нет |
> | закладки | Нет |
> | cases | Нет |
> | Подключения к компонентам | Нет |
> | Сущности | Нет |
> | ентитикуериес | Нет |
> | оффицеконсентс | Нет |
> | Параметры | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | namespaces | ДА |
> | namespaces/authorizationrules | Нет |
> | namespaces/disasterrecoveryconfigs | Нет |
> | namespaces/eventgridfilters | Нет |
> | пространства имен/нетворкрулесетс | Нет |
> | namespaces/queues | Нет |
> | namespaces/queues/authorizationrules | Нет |
> | namespaces/topics | Нет |
> | namespaces/topics/authorizationrules | Нет |
> | namespaces/topics/subscriptions | Нет |
> | namespaces/topics/subscriptions/rules | Нет |
> | premiumMessagingRegions | Нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | приложений | ДА |
> | clusters | ДА |
> | clusters/applications | Нет |
> | containerGroups | ДА |
> | контаинерграупсетс | ДА |
> | edgeclusters | ДА |
> | еджеклустерс и приложения | Нет |
> | networks | ДА |
> | secretstores | ДА |
> | секретсторес и сертификаты | Нет |
> | секретсторес/секреты | Нет |
> | volumes. | ДА |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | приложений | ДА |
> | containerGroups | ДА |
> | gateways | ДА |
> | networks | ДА |
> | секретные коды | ДА |
> | volumes. | ДА |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | providerRegistrations | Нет |
> | Провидеррегистратионс/Ресаурцетиперегистратионс | Нет |
> | rollouts | ДА |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SignalR | ДА |
> | SignalR/Евентгридфилтерс | Нет |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SiteRecoveryVault | ДА |

## <a name="microsoftsoftwareplan"></a>Microsoft. Софтвареплан

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | хибридусебенефитс | Нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | applicationDefinitions | ДА |
> | приложений | ДА |
> | jitRequests | ДА |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managedInstances | ДА |
> | managedInstances/databases | ДА |
> | managedInstances/databases/backupShortTermRetentionPolicies | Нет |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Нет |
> | managedInstances/databases/vulnerabilityAssessments | Нет |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Нет |
> | managedInstances/encryptionProtector | Нет |
> | managedInstances/keys | Нет |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Нет |
> | managedInstances/vulnerabilityAssessments | Нет |
> | серверы | ДА |
> | servers/administrators | Нет |
> | servers/communicationLinks | Нет |
> | servers/databases | ДА |
> | servers/encryptionProtector | Нет |
> | servers/firewallRules | Нет |
> | servers/keys | Нет |
> | servers/restorableDroppedDatabases | Нет |
> | servers/serviceobjectives | Нет |
> | servers/tdeCertificates | Нет |
> | виртуалклустерс | Нет |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | ДА |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Нет |
> | SqlVirtualMachines | ДА |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageAccounts | ДА |
> | storageAccounts/blobServices | Нет |
> | storageAccounts/fileServices | Нет |
> | storageAccounts/queueServices | Нет |
> | storageAccounts/services | Нет |
> | storageAccounts/services/metricDefinitions | Нет |
> | storageAccounts/tableServices | Нет |
> | usages | Нет |

## <a name="microsoftstoragecache"></a>Microsoft. Сторажекаче

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | кэширует | ДА |
> | кэши/Сторажетаржетс | Нет |
> | усажемоделс | Нет |

## <a name="microsoftstoragereplication"></a>Microsoft. Сторажерепликатион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | репликатионграупс | Нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | ДА |
> | storageSyncServices/registeredServers | Нет |
> | storageSyncServices/syncGroups | Нет |
> | storageSyncServices/syncGroups/cloudEndpoints | Нет |
> | storageSyncServices/syncGroups/serverEndpoints | Нет |
> | storageSyncServices/workflows | Нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | ДА |
> | storageSyncServices/registeredServers | Нет |
> | storageSyncServices/syncGroups | Нет |
> | storageSyncServices/syncGroups/cloudEndpoints | Нет |
> | storageSyncServices/syncGroups/serverEndpoints | Нет |
> | storageSyncServices/workflows | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | ДА |
> | storageSyncServices/registeredServers | Нет |
> | storageSyncServices/syncGroups | Нет |
> | storageSyncServices/syncGroups/cloudEndpoints | Нет |
> | storageSyncServices/syncGroups/serverEndpoints | Нет |
> | storageSyncServices/workflows | Нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managers | ДА |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | streamingjobs | ДА |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Отмена | Нет |
> | CreateSubscription | Нет |
> | enable | Нет |
> | Имени | Нет |
> | SubscriptionDefinitions | Нет |
> | SubscriptionOperations | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | environments | ДА |
> | environments/accessPolicies | Нет |
> | environments/eventsources | ДА |
> | environments/referenceDataSets | ДА |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | дедикатедклауднодес | ДА |
> | дедикатедклаудсервицес | ДА |
> | virtualMachines | ДА |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | apiManagementAccounts | Нет |
> | apiManagementAccounts/apiAcls | Нет |
> | apiManagementAccounts/apis | Нет |
> | apiManagementAccounts/apis/apiAcls | Нет |
> | apiManagementAccounts/apis/connectionAcls | Нет |
> | apiManagementAccounts/apis/connections | Нет |
> | apiManagementAccounts/apis/connections/connectionAcls | Нет |
> | apiManagementAccounts/apis/localizedDefinitions | Нет |
> | apiManagementAccounts/connectionAcls | Нет |
> | apiManagementAccounts/connections | Нет |
> | billingMeters | Нет |
> | certificates | ДА |
> | connectionGateways | ДА |
> | connections | ДА |
> | customApis | ДА |
> | deletedSites | Нет |
> | функции | Нет |
> | hostingEnvironments | ДА |
> | hostingEnvironments/multiRolePools | Нет |
> | hostingEnvironments/workerPools | Нет |
> | publishingUsers | Нет |
> | к просмотру фильмов | Нет |
> | resourceHealthMetadata | Нет |
> | runtimes | Нет |
> | serverFarms | ДА |
> | serverFarms/Евентгридфилтерс | Нет |
> | sites | ДА |
> | сайты и конфигурация  | Нет |
> | Sites/Евентгридфилтерс | Нет |
> | sites/hostNameBindings | Нет |
> | Sites/файл networkconfig | Нет |
> | sites/premieraddons | ДА |
> | sites/slots | ДА |
> | сайты/слоты/Евентгридфилтерс | Нет |
> | sites/slots/hostNameBindings | Нет |
> | сайты/слоты/файл networkconfig | Нет |
> | sourceControls | Нет |
> | validate | Нет |
> | verifyHostingEnvironmentVnet | Нет |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | diagnosticSettings | Нет |
> | diagnosticSettingsCategories | Нет |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | DeviceServices | ДА |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | components | Нет |
> | componentsSummary | Нет |
> | monitorInstances | Нет |
> | monitorInstancesSummary | Нет |
> | monitors | Нет |
> | notificationSettings | Нет |

## <a name="next-steps"></a>Дальнейшие действия

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
