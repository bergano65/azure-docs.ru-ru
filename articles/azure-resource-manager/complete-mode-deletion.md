---
title: Удаление ресурсов в полном режиме в Azure Resource Manager по типу ресурса
description: Здесь показано, как происходит удаление ресурсов в полном режиме в шаблонах Azure Resource Manager по типу ресурса.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/04/2019
ms.author: tomfitz
ms.openlocfilehash: 7f804e61f77b745aa654852f4c1413a8f4bf53b4
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779950"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Удаление ресурсов Azure для развертываний в полном режиме

В этой статье описывается, как процесс удаления будет обрабатываться различными типами, когда он не происходит в шаблоне, развернутом в завершенном режиме.

Типы ресурсов, помеченные **Да** , удаляются, если тип не находится в шаблоне, развернутом в полном режиме.

Типы ресурсов, помеченные как " **нет** ", не удаляются автоматически при отсутствии в шаблоне. Однако они удаляются, если родительский ресурс удаляется. Чтобы получить полное описание поведения см. статью [Режимы развертывания в Azure Resource Manager](deployment-modes.md).

Переход к пространству имен поставщика ресурсов:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft. Ааддомаинсервицес](#microsoftaaddomainservices)
> - [Надстройки Microsoft.](#microsoftaddons)
> - [Microsoft. Адхибридхеалссервице](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. Алертсманажемент](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft. Аппконфигуратион](#microsoftappconfiguration)
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
> - [Microsoft. Бизталксервицес](#microsoftbiztalkservices)
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
> - [Microsoft. Контентмодератор](#microsoftcontentmoderator)
> - [Microsoft. Кортанааналитикс](#microsoftcortanaanalytics)
> - [Microsoft. Костманажемент](#microsoftcostmanagement)
> - [Microsoft. Кустомеринсигхтс](#microsoftcustomerinsights)
> - [Microsoft. Кустомерлоккбокс](#microsoftcustomerlockbox)
> - [Microsoft. Кустомпровидерс](#microsoftcustomproviders)
> - [Microsoft. Датабокс](#microsoftdatabox)
> - [Microsoft. Датабокседже](#microsoftdataboxedge)
> - [Microsoft. кирпичы](#microsoftdatabricks)
> - [Каталог Microsoft.](#microsoftdatacatalog)
> - [Microsoft. соединение](#microsoftdataconnect)
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
> - [Microsoft. Манажедлаб](#microsoftmanagedlab)
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
> | DomainServices | Да |
> | DomainServices/oucontainer | Нет |
> | DomainServices/Репликасетс | Да |

## <a name="microsoftaaddomainservices"></a>Microsoft. Ааддомаинсервицес

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | domains | Нет |

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
> | настройка | Нет |
> | logs | Нет |
> | Отчеты | Нет |
> | servicehealthmetrics | Нет |
> | services; | Нет |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | конфигурации | Нет |
> | generateRecommendations | Нет |
> | метаданные | Нет |
> | рекомендации | Нет |
> | suppressions | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | actionRules | Да |
> | оповещений | Нет |
> | alertsList | Нет |
> | алертсметадата | Нет |
> | alertsSummary | Нет |
> | alertsSummaryList | Нет |
> | оставлять | Нет |
> | smartDetectorAlertRules | Нет |
> | smartDetectorRuntimeEnvironments | Нет |
> | smartGroups | Нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | серверы | Да |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | reportFeedback | Нет |
> | служба | Да |
> | validateServiceName | Нет |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | конфигуратионсторес | Да |
> | Конфигуратионсторес/Евентгридфилтерс | Нет |

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
> | locks | Нет |
> | разрешения | Нет |
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
> | automationAccounts | Да |
> | automationAccounts/configurations | Да |
> | automationAccounts/jobs | Нет |
> | automationAccounts/runbooks | Да |
> | automationAccounts/softwareUpdateConfigurations | Нет |
> | automationAccounts/webhooks | Нет |

## <a name="microsoftazconfig"></a>Microsoft. Азконфиг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | конфигуратионсторес | Да |
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
> | b2cDirectories | Да |
> | b2ctenants | Нет |

## <a name="microsoftazuredata"></a>Microsoft. Азуредата

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | склсерверрегистратионс | Да |
> | Склсерверрегистратионс и sqlServer | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | registrations | Да |
> | registrations/customerSubscriptions | Нет |
> | registrations/products | Нет |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | batchAccounts | Да |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | billingAccounts | Нет |
> | Биллингаккаунтс и соглашения | Нет |
> | billingAccounts/billingProfiles | Нет |
> | billingAccounts/billingProfiles/billingSubscriptions | Нет |
> | billingAccounts/billingProfiles/invoices | Нет |
> | billingAccounts/billingProfiles/invoices/pricesheet | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Патчоператионс | Нет |
> | billingAccounts/billingProfiles/paymentMethods | Нет |
> | billingAccounts/billingProfiles/policies | Нет |
> | billingAccounts/billingProfiles/pricesheet | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Прицешитдовнлоадоператионс | Нет |
> | billingAccounts/billingProfiles/products | Нет |
> | billingAccounts/billingProfiles/transactions | Нет |
> | billingAccounts/billingSubscriptions | Нет |
> | Биллингаккаунтс/Креатеинвоицесектионоператионс | Нет |
> | Биллингаккаунтс и клиенты | Нет |
> | Биллингаккаунтс/Customers/Биллингсубскриптионс | Нет |
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
> | валидатеаддресс | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | mapApis | Да |
> | updateCommunicationPreference | Нет |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | BizTalk | Да |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | блоккчаинмемберс | Да |
> | наблюдатели | Да |

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
> | botServices | Да |
> | botServices/channels | Нет |
> | botServices/connections | Нет |
> | языки | Нет |
> | шаблоны | Нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Redis | Да |
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
> | обмен валюты | Нет |
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
> | ресурсы | Нет |
> | validateReservationOrder | Нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | кднвебаппликатионфиреваллманажедрулесетс | Нет |
> | кднвебаппликатионфиреваллполиЦиес | Да |
> | edgenodes | Нет |
> | profiles | Да |
> | profiles/endpoints | Да |
> | profiles/endpoints/customdomains | Нет |
> | profiles/endpoints/origins | Нет |
> | validateProbe | Нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | certificateOrders | Да |
> | certificateOrders/certificates | Нет |
> | validateCertificateRegistrationInformation | Нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capabilities | Нет |
> | domainNames | Да |
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
> | virtualMachines | Да |
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
> | networkSecurityGroups | Да |
> | quotas | Нет |
> | reservedIps | Да |
> | virtualNetworks | Да |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Нет |
> | virtualNetworks/virtualNetworkPeerings | Нет |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capabilities | Нет |
> | диски | Нет |
> | образы | Нет |
> | osImages | Нет |
> | osPlatformImages | Нет |
> | publicImages | Нет |
> | quotas | Нет |
> | storageAccounts | Да |
> | storageAccounts/metricDefinitions | Нет |
> | storageAccounts/метрики | Нет |
> | storageAccounts/services | Нет |
> | storageAccounts/services/diagnosticSettings | Нет |
> | storageAccounts/services/metricDefinitions | Нет |
> | storageAccounts/Services/метрики | Нет |
> | storageAccounts/vmImages | Нет |
> | vmImages | Нет |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | учетные записи | Да |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | RateCard | Нет |
> | UsageAggregates | Нет |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | availabilitySets | Да |
> | дискенкриптионсетс | Да |
> | диски | Да |
> | galleries | Да |
> | коллекции и приложения | Да |
> | коллекции, приложения и версии | Да |
> | galleries/images | Да |
> | galleries/images/versions | Да |
> | хостграупс | Да |
> | Хостграупс и узлы | Да |
> | образы | Да |
> | проксимитиплацементграупс | Да |
> | restorePointCollections | Да |
> | restorePointCollections/restorePoints | Нет |
> | sharedVMImages | Да |
> | sharedVMImages/versions | Да |
> | моментальные снимки | Да |
> | virtualMachines | Да |
> | virtualMachines/extensions | Да |
> | virtualMachines/metricDefinitions | Нет |
> | virtualMachines/Скриптжобс | Нет |
> | virtualMachines/Софтвареупдатедеплойментс | Нет |
> | virtualMachineScaleSets | Да |
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
> | Бюджеты | Нет |
> | Расходы | Нет |
> | CostTags | Нет |
> | credits | Нет |
> | события | Нет |
> | Прогнозы | Нет |
> | lots | Нет |
> | Marketplace | Нет |
> | Pricesheets | Нет |
> | products | Нет |
> | ReservationDetails | Нет |
> | ReservationRecommendations | Нет |
> | ReservationSummaries | Нет |
> | ReservationTransactions | Нет |
> | Tags | Нет |
> | tenants | Нет |
> | Термины | Нет |
> | UsageDetails | Нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | containerGroups | Да |
> | serviceAssociationLinks | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | registries | Да |
> | registries/builds | Нет |
> | registries/builds/cancel | Нет |
> | registries/builds/getLogLink | Нет |
> | registries/buildTasks | Да |
> | registries/buildTasks/steps | Нет |
> | registries/eventGridFilters | Нет |
> | registries/getBuildSourceUploadUrl | Нет |
> | registries/GetCredentials | Нет |
> | registries/importImage | Нет |
> | registries/queueBuild | Нет |
> | registries/regenerateCredential | Нет |
> | registries/regenerateCredentials | Нет |
> | registries/replications | Да |
> | registries/runs | Нет |
> | registries/runs/cancel | Нет |
> | registries/scheduleRun | Нет |
> | registries/tasks | Да |
> | registries/updatePolicies | Нет |
> | registries/webhooks | Да |
> | registries/webhooks/getCallbackConfig | Нет |
> | registries/webhooks/ping | Нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | containerServices | Да |
> | managedClusters | Да |
> | опеншифтманажедклустерс | Да |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | приложения | Да |
> | updateCommunicationPreference | Нет |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | учетные записи | Да |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Предупреждения | Нет |
> | BillingAccounts | Нет |
> | Бюджеты | Нет |
> | клаудконнекторс | Нет |
> | Соединители | Да |
> | Departments | Нет |
> | Размеры | Нет |
> | EnrollmentAccounts | Нет |
> | Экспортируемые элементы | Нет |
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
> | Прогноз | Нет |
> | Запрос | Нет |
> | регистрация | Нет |
> | Reportconfigs | Нет |
> | Отчеты | Нет |
> | Настройки | Нет |
> | шовбаккрулес | Нет |
> | Представления | Нет |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | hubs | Да |
> | hubs/authorizationPolicies | Нет |
> | hubs/connectors | Нет |
> | hubs/connectors/mappings | Нет |
> | hubs/interactions | Нет |
> | hubs/kpi | Нет |
> | hubs/links | Нет |
> | hubs/profiles | Нет |
> | hubs/roleAssignments | Нет |
> | hubs/roles | Нет |
> | hubs/suggestTypeSchema | Нет |
> | hubs/views | Нет |
> | hubs/widgetTypes | Нет |

## <a name="microsoftcustomerlockbox"></a>Microsoft. Кустомерлоккбокс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | запросы | Нет |

## <a name="microsoftcustomproviders"></a>Microsoft. Кустомпровидерс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | связи | Нет |
> | ресаурцепровидерс | Да |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | задания | Да |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Да |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | рабочие области | Да |
> | workspaces/virtualNetworkPeerings | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | catalogs | Да |
> | каталоги | Да |
> | каталоги и скантаржетс | Нет |
> | каталоги данных/скантаржетс/DataSets | Нет |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | connectionManagers | Да |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | dataFactories | Да |
> | dataFactories/diagnosticSettings | Нет |
> | dataFactories/metricDefinitions | Нет |
> | dataFactorySchema | Нет |
> | factories | Да |
> | factories/integrationRuntimes | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | учетные записи | Да |
> | accounts/dataLakeStoreAccounts | Нет |
> | accounts/storageAccounts | Нет |
> | accounts/storageAccounts/containers | Нет |
> | Accounts/Трансфераналитиксунитс | Нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | учетные записи | Да |
> | accounts/eventGridFilters | Нет |
> | accounts/firewallRules | Нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | services; | Да |
> | services/projects | Да |
> | slots | Да |

## <a name="microsoftdatashare"></a>Общая папка Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | учетные записи | Да |
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
> | серверы | Да |
> | servers/advisors | Нет |
> | servers/queryTexts | Нет |
> | servers/recoverableServers | Нет |
> | servers/topQueryStatistics | Нет |
> | servers/virtualNetworkRules | Нет |
> | servers/waitStatistics | Нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | серверы | Да |
> | servers/advisors | Нет |
> | servers/queryTexts | Нет |
> | servers/recoverableServers | Нет |
> | servers/topQueryStatistics | Нет |
> | servers/virtualNetworkRules | Нет |
> | servers/waitStatistics | Нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | серверграупс | Да |
> | серверы | Да |
> | servers/advisors | Нет |
> | servers/queryTexts | Нет |
> | servers/recoverableServers | Нет |
> | servers/topQueryStatistics | Нет |
> | servers/virtualNetworkRules | Нет |
> | servers/waitStatistics | Нет |
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
> | аппликатионграупс и приложения | Нет |
> | аппликатионграупс/ассигнедусерс | Нет |
> | аппликатионграупс/стартменуитемс | Нет |
> | хостпулс | Да |
> | хостпулс/сессионхостс | Нет |
> | хостпулс/сессионхостс/усерсессионс | Нет |
> | хостпулс/усерсессионс | Нет |
> | рабочие области | Да |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | еластикпулс | Да |
> | Еластикпулс/Иосубтенантс | Да |
> | IotHubs | Да |
> | IotHubs/eventGridFilters | Нет |
> | ProvisioningServices | Да |
> | usages | Нет |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | конвейеры | Да |

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
> | labs/serviceRunners | Да |
> | labs/virtualMachines | Да |
> | schedules | Да |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | databaseAccountNames | Нет |
> | databaseAccounts | Да |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | domains | Да |
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
> | services; | Да |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | domains | Да |
> | domains/topics | Нет |
> | eventSubscriptions | Нет |
> | extensionTopics | Нет |
> | topics | Да |
> | topicTypes | Нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | Да |
> | пространства имен | Да |
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
> | features | Нет |
> | providers | Нет |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | зарегистрировать | Нет |
> | galleryitems | Нет |
> | generateartifactaccessuri | Нет |
> | myareas | Нет |
> | myareas/areas | Нет |
> | myareas/areas/areas | Нет |
> | myareas/areas/areas/galleryitems | Нет |
> | myareas/areas/galleryitems | Нет |
> | myareas/galleryitems | Нет |
> | регистрация | Нет |
> | ресурсы | Нет |
> | retrieveresourcesbyid | Нет |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | учетные записи | Да |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | guestConfigurationAssignments | Нет |
> | software | Нет |
> | софтвареупдатепрофиле | Нет |
> | софтвареупдатес | Нет |

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
> | clusters/applications | Нет |

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | services; | Да |

## <a name="microsofthybridcompute"></a>Microsoft. Хибридкомпуте

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Компьютеры | Да |

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
> | задания | Да |

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
> | IoTApps | Да |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Граф | Да |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | deletedVaults | Нет |
> | хсмпулс | Да |
> | vaults | Да |
> | vaults/accessPolicies | Нет |
> | хранилища и Евентгридфилтерс | Нет |
> | vaults/secrets | Нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | Да |
> | кластеры/аттачеддатабасеконфигуратионс | Нет |
> | clusters/databases | Нет |
> | clusters/databases/dataconnections | Нет |
> | clusters/databases/eventhubconnections | Нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | labaccounts | Да |
> | пользователей | Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | hostingEnvironments | Да |
> | integrationAccounts | Да |
> | интегратионсервицеенвиронментс | Да |
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
> | рабочие области | Да |
> | workspaces/computes | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Удостоверения | Нет |
> | userAssignedIdentities | Да |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | labaccounts | Да |

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
> | ресурсы | Нет |
> | startTenantBackfill | Нет |
> | tenantBackfillStatus | Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | учетные записи | Да |
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
> | издатели | Нет |
> | издатели и предложения | Нет |
> | издатели, предложения и поправки | Нет |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | classicDevServices | Да |
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
> | mediaservices | Да |
> | mediaservices/accountFilters | Нет |
> | mediaservices/assets | Нет |
> | mediaservices/assets/assetFilters | Нет |
> | mediaservices/contentKeyPolicies | Нет |
> | mediaservices/eventGridFilters | Нет |
> | mediaservices/liveEventOperations | Нет |
> | mediaservices/liveEvents | Да |
> | mediaservices/liveEvents/liveOutputs | Нет |
> | mediaservices/liveOutputOperations | Нет |
> | mediaservices/streamingEndpointOperations | Нет |
> | mediaservices/streamingEndpoints | Да |
> | mediaservices/streamingLocators | Нет |
> | mediaservices/streamingPolicies | Нет |
> | mediaservices/transforms | Нет |
> | mediaservices/transforms/jobs | Нет |

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
> | projects | Да |

## <a name="microsoftmixedreality"></a>Microsoft. Микседреалити

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | ремотерендерингаккаунтс | Да |
> | спатиаланчорсаккаунтс | Да |

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
> | azureFirewallFqdnTags | Нет |
> | azureFirewalls | Да |
> | бастионхостс | Да |
> | bgpServiceCommunities | Нет |
> | подключения | Да |
> | ddosCustomPolicies | Да |
> | ddosProtectionPlans | Да |
> | dnsOperationStatuses | Нет |
> | dnszones | Да |
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
> | expressRouteCircuits | Да |
> | expressRouteCrossConnections | Да |
> | експрессраутегатевайс | Да |
> | експрессраутепортс | Да |
> | expressRouteServiceProviders | Нет |
> | фиреваллполиЦиес | Да |
> | frontdoor | Да |
> | фронтдурвебаппликатионфиреваллманажедрулесетс | Нет |
> | frontdoorWebApplicationFirewallPolicies | Да |
> | getDnsResourceReference | Нет |
> | internalNotify | Нет |
> | loadBalancers | Да |
> | localNetworkGateways | Да |
> | natGateways | Да |
> | networkIntentPolicies | Да |
> | networkInterfaces | Да |
> | networkProfiles | Да |
> | networkSecurityGroups | Да |
> | networkWatchers | Да |
> | networkWatchers/connectionMonitors | Да |
> | networkWatchers/lenses | Да |
> | networkWatchers/pingMeshes | Да |
> | p2sVpnGateways | Да |
> | приватеднсоператионстатусес | Нет |
> | приватеднсзонес | Да |
> | Приватеднсзонес/A | Нет |
> | Приватеднсзонес/AAAA | Нет |
> | Приватеднсзонес/все | Нет |
> | Приватеднсзонес и CNAME | Нет |
> | Приватеднсзонес/MX | Нет |
> | Приватеднсзонес/PTR | Нет |
> | Приватеднсзонес/SOA | Нет |
> | Приватеднсзонес/SRV | Нет |
> | Приватеднсзонес/TXT | Нет |
> | Приватеднсзонес/Виртуалнетворклинкс | Да |
> | приватиндпоинтс | Да |
> | privateLinkServices | Да |
> | publicIPAddresses | Да |
> | publicIPPrefixes | Да |
> | routeFilters | Да |
> | routeTables | Да |
> | секурегатевайс | Да |
> | serviceEndpointPolicies | Да |
> | trafficManagerGeographicHierarchies | Нет |
> | trafficmanagerprofiles | Да |
> | trafficmanagerprofiles/heatMaps | Нет |
> | траффикманажерусерметрикскэйс | Нет |
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
> | namespaces/notificationHubs | Да |

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
> | устройства | Нет |
> | linkTargets | Нет |
> | storageInsightConfigs | Нет |
> | рабочие области | Да |
> | workspaces/dataSources | Нет |
> | workspaces/linkedServices | Нет |
> | workspaces/query | Нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managementassociations | Нет |
> | managementconfigurations | Да |
> | решения | Да |
> | просмотры | Да |

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | легаципирингс | Нет |
> | пираснс | Нет |
> | пиринги | Да |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | policyEvents | Нет |
> | policyStates | Нет |
> | policyTrackedResources | Нет |
> | remediations | Нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | consoles | Нет |
> | dashboards | Да |
> | userSettings | Нет |

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

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | backupProtectedItems | Нет |
> | vaults | Да |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | пространства имен | Да |
> | namespaces/authorizationrules | Нет |
> | namespaces/hybridconnections | Нет |
> | namespaces/hybridconnections/authorizationrules | Нет |
> | namespaces/wcfrelays | Нет |
> | namespaces/wcfrelays/authorizationrules | Нет |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | учетные записи | Нет |
> | семейства | Да |
> | коллекции и приложения | Нет |
> | Collections/секуритипринЦипалс | Нет |
> | темплатеимажес | Нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Запросы | Да |
> | ресаурцечанжедетаилс | Нет |
> | ресаурцечанжес | Нет |
> | ресурсы | Нет |
> | ресаурцешистори | Нет |
> | subscriptionsStatus | Нет |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | availabilityStatuses | Нет |
> | childAvailabilityStatuses | Нет |
> | childResources | Нет |
> | события | Нет |
> | impactedResources | Нет |
> | метаданные | Нет |
> | уведомления | Нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | развертывания | Нет |
> | deployments/operations | Нет |
> | links | Нет |
> | notifyResourceJobs | Нет |
> | providers | Нет |
> | resourceGroups | Нет |
> | ресурсы | Нет |
> | — подписки | Нет |
> | subscriptions/providers | Нет |
> | subscriptions/resourceGroups | Нет |
> | subscriptions/resourcegroups/resources | Нет |
> | subscriptions/resources | Нет |
> | subscriptions/tagnames | Нет |
> | subscriptions/tagNames/tagValues | Нет |
> | теги | Нет |
> | tenants | Нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | приложения | Да |
> | saasresources | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Потоки | Да |
> | jobcollections | Да |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | resourceHealthMetadata | Нет |
> | searchServices | Да |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | адаптивенетворкхарденингс | Нет |
> | advancedThreatProtectionSettings | Нет |
> | оповещений | Нет |
> | allowedConnections | Нет |
> | applicationWhitelistings | Нет |
> | ассессментметадата | Нет |
> | оценки | Нет |
> | AutoProvisioningSettings | Нет |
> | Compliances | Нет |
> | dataCollectionAgents | Нет |
> | девицесекуритиграупс | Нет |
> | discoveredSecuritySolutions | Нет |
> | externalSecuritySolutions | Нет |
> | InformationProtectionPolicies | Нет |
> | иотсекуритисолутионс | Да |
> | Иотсекуритисолутионс/Аналитиксмоделс | Нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедалертс | Нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедрекоммендатионс | Нет |
> | jitNetworkAccessPolicies | Нет |
> | плайбукконфигуратионс | Да |
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
> | параметры | Нет |
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
> | агрегатов | Нет |
> | alertRules | Нет |
> | закладки | Нет |
> | cases | Нет |
> | Подключения к компонентам | Нет |
> | сущности | Нет |
> | ентитикуериес | Нет |
> | оффицеконсентс | Нет |
> | параметры | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | пространства имен | Да |
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
> | приложения | Да |
> | clusters | Да |
> | clusters/applications | Нет |
> | containerGroups | Да |
> | контаинерграупсетс | Да |
> | edgeclusters | Да |
> | еджеклустерс и приложения | Нет |
> | Сети | Да |
> | secretstores | Да |
> | секретсторес и сертификаты | Нет |
> | секретсторес/секреты | Нет |
> | тома | Да |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | приложения | Да |
> | containerGroups | Да |
> | gateways | Да |
> | Сети | Да |
> | секреты | Да |
> | тома | Да |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | providerRegistrations | Нет |
> | Провидеррегистратионс/Ресаурцетиперегистратионс | Нет |
> | rollouts | Да |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SignalR | Да |
> | SignalR/Евентгридфилтерс | Нет |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SiteRecoveryVault | Да |

## <a name="microsoftsoftwareplan"></a>Microsoft. Софтвареплан

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | хибридусебенефитс | Нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | applicationDefinitions | Да |
> | приложения | Да |
> | jitRequests | Да |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managedInstances | Да |
> | managedInstances/databases | Да |
> | managedInstances/databases/backupShortTermRetentionPolicies | Нет |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Нет |
> | managedInstances/databases/vulnerabilityAssessments | Нет |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Нет |
> | managedInstances/encryptionProtector | Нет |
> | managedInstances/keys | Нет |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Нет |
> | managedInstances/vulnerabilityAssessments | Нет |
> | серверы | Да |
> | servers/administrators | Нет |
> | servers/communicationLinks | Нет |
> | servers/databases | Да |
> | servers/encryptionProtector | Нет |
> | servers/firewallRules | Нет |
> | servers/keys | Нет |
> | servers/restorableDroppedDatabases | Нет |
> | servers/serviceobjectives | Нет |
> | servers/tdeCertificates | Нет |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Да |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Нет |
> | SqlVirtualMachines | Да |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageAccounts | Да |
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
> | кэширует | Да |
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
> | storageSyncServices | Да |
> | storageSyncServices/registeredServers | Нет |
> | storageSyncServices/syncGroups | Нет |
> | storageSyncServices/syncGroups/cloudEndpoints | Нет |
> | storageSyncServices/syncGroups/serverEndpoints | Нет |
> | storageSyncServices/workflows | Нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | Да |
> | storageSyncServices/registeredServers | Нет |
> | storageSyncServices/syncGroups | Нет |
> | storageSyncServices/syncGroups/cloudEndpoints | Нет |
> | storageSyncServices/syncGroups/serverEndpoints | Нет |
> | storageSyncServices/workflows | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | Да |
> | storageSyncServices/registeredServers | Нет |
> | storageSyncServices/syncGroups | Нет |
> | storageSyncServices/syncGroups/cloudEndpoints | Нет |
> | storageSyncServices/syncGroups/serverEndpoints | Нет |
> | storageSyncServices/workflows | Нет |

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
> | отмена | Нет |
> | CreateSubscription | Нет |
> | переименовать | Нет |
> | SubscriptionDefinitions | Нет |
> | SubscriptionOperations | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | environments | Да |
> | environments/accessPolicies | Нет |
> | environments/eventsources | Да |
> | environments/referenceDataSets | Да |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | дедикатедклауднодес | Да |
> | дедикатедклаудсервицес | Да |
> | virtualMachines | Да |

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
> | сертификаты | Да |
> | connectionGateways | Да |
> | подключения | Да |
> | customApis | Да |
> | deletedSites | Нет |
> | функц. | Нет |
> | hostingEnvironments | Да |
> | hostingEnvironments/multiRolePools | Нет |
> | hostingEnvironments/workerPools | Нет |
> | publishingUsers | Нет |
> | рекомендации | Нет |
> | resourceHealthMetadata | Нет |
> | runtimes | Нет |
> | serverFarms | Да |
> | serverFarms/Евентгридфилтерс | Нет |
> | сайты | Да |
> | сайты и конфигурация  | Нет |
> | Sites/Евентгридфилтерс | Нет |
> | sites/hostNameBindings | Нет |
> | Sites/файл networkconfig | Нет |
> | sites/premieraddons | Да |
> | sites/slots | Да |
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
> | DeviceServices | Да |

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

## <a name="next-steps"></a>Следующие шаги

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
