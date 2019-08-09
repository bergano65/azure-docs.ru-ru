---
title: Поддержка тегов Azure Resource Manager для ресурсов
description: В этой статье описываются типы ресурсов Azure, поддерживающие теги, а также приводятся сведения о всех службах Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/05/2019
ms.author: tomfitz
ms.openlocfilehash: 452230a263e623f53346fcdb8cf310e73f171aee
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848604"
---
# <a name="tag-support-for-azure-resources"></a>Поддержка тегов для ресурсов Azure
В этой статье приводятся сведения о поддержке типом ресурса функции [тегов](resource-group-using-tags.md). Столбец с подписью **поддерживает теги** . указывает, имеет ли тип ресурса свойство для тега. Столбец с меткой **тег в отчете о затратах** указывает, передает ли этот тип ресурса тег в отчет о затратах.

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

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
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | DomainServices | Да |Да |
> | DomainServices/oucontainer | Нет |Нет |
> | DomainServices/Репликасетс | Да |Да |

## <a name="microsoftaaddomainservices"></a>Microsoft. Ааддомаинсервицес

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | domains | Нет |Нет |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | supportProviders | Нет |Нет |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Нет |Нет |
> | addsservices | Нет |Нет |
> | agents | Нет |Нет |
> | anonymousapiusers | Нет |Нет |
> | настройка | Нет |Нет |
> | logs | Нет |Нет |
> | Отчеты | Нет |Нет |
> | servicehealthmetrics | Нет |Нет |
> | services; | Нет |Нет |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конфигурации | Нет |Нет |
> | generateRecommendations | Нет |Нет |
> | метаданные | Нет |Нет |
> | рекомендации | Нет |Нет |
> | suppressions | Нет |Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | actionRules | Да |Да |
> | оповещений | Нет |Нет |
> | alertsList | Нет |Нет |
> | алертсметадата | Нет |Нет |
> | alertsSummary | Нет |Нет |
> | alertsSummaryList | Нет |Нет |
> | оставлять | Нет |Нет |
> | smartDetectorAlertRules | Нет |Нет |
> | smartDetectorRuntimeEnvironments | Нет |Нет |
> | smartGroups | Нет |Нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | серверы | Да |Да |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Нет |Нет |
> | служба | Да |Да |
> | validateServiceName | Нет |Нет |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конфигуратионсторес | Да |Да |
> | Конфигуратионсторес/Евентгридфилтерс | Нет |Нет |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Нет |Нет |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Нет |Нет |
> | Псевдонимы | Нет |Нет |
> | denyAssignments | Нет |Нет |
> | elevateAccess | Нет |Нет |
> | locks | Нет |Нет |
> | разрешения | Нет |Нет |
> | policyAssignments | Нет |Нет |
> | policyDefinitions | Нет |Нет |
> | policySetDefinitions | Нет |Нет |
> | providerOperations | Нет |Нет |
> | roleAssignments | Нет |Нет |
> | roleDefinitions | Нет |Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Да |Да |
> | automationAccounts/configurations | Да |Да |
> | automationAccounts/jobs | Нет |Нет |
> | automationAccounts/runbooks | Да |Да |
> | automationAccounts/softwareUpdateConfigurations | Нет |Нет |
> | automationAccounts/webhooks | Нет |Нет |

## <a name="microsoftazconfig"></a>Microsoft. Азконфиг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конфигуратионсторес | Да |Да |
> | Конфигуратионсторес/Евентгридфилтерс | Нет |Нет |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | environments | Нет |Нет |
> | environments/accounts | Нет |Нет |
> | environments/accounts/namespaces | Нет |Нет |
> | environments/accounts/namespaces/configurations | Нет |Нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Да |Нет |
> | b2ctenants | Нет |Нет |

## <a name="microsoftazuredata"></a>Microsoft. Азуредата

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | склсерверрегистратионс | Да |Да |
> | Склсерверрегистратионс и sqlServer | Нет |Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | registrations | Да |Да |
> | registrations/customerSubscriptions | Нет |Нет |
> | registrations/products | Нет |Нет |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Да |Да |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Нет |Нет |
> | Биллингаккаунтс и соглашения | Нет |Нет |
> | billingAccounts/billingProfiles | Нет |Нет |
> | billingAccounts/billingProfiles/billingSubscriptions | Нет |Нет |
> | billingAccounts/billingProfiles/invoices | Нет |Нет |
> | billingAccounts/billingProfiles/invoices/pricesheet | Нет |Нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс | Нет |Нет |
> | Биллингаккаунтс/Биллингпрофилес/Патчоператионс | Нет |Нет |
> | billingAccounts/billingProfiles/paymentMethods | Нет |Нет |
> | billingAccounts/billingProfiles/policies | Нет |Нет |
> | billingAccounts/billingProfiles/pricesheet | Нет |Нет |
> | Биллингаккаунтс/Биллингпрофилес/Прицешитдовнлоадоператионс | Нет |Нет |
> | billingAccounts/billingProfiles/products | Нет |Нет |
> | billingAccounts/billingProfiles/transactions | Нет |Нет |
> | billingAccounts/billingSubscriptions | Нет |Нет |
> | Биллингаккаунтс/Креатеинвоицесектионоператионс | Нет |Нет |
> | Биллингаккаунтс и клиенты | Нет |Нет |
> | Биллингаккаунтс/Customers/Биллингсубскриптионс | Нет |Нет |
> | billingAccounts/departments | Нет |Нет |
> | billingAccounts/enrollmentAccounts | Нет |Нет |
> | billingAccounts/invoices | Нет |Нет |
> | billingAccounts/invoiceSections | Нет |Нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионмовеоператионс | Нет |Нет |
> | billingAccounts/invoiceSections/billingSubscriptions | Нет |Нет |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Нет |Нет |
> | Биллингаккаунтс/Инвоицесектионс/повышение прав | Нет |Нет |
> | billingAccounts/invoiceSections/initiateTransfer | Нет |Нет |
> | Биллингаккаунтс/Инвоицесектионс/Патчоператионс | Нет |Нет |
> | Биллингаккаунтс/Инвоицесектионс/Продуктмовеоператионс | Нет |Нет |
> | billingAccounts/invoiceSections/products | Нет |Нет |
> | Биллингаккаунтс/Инвоицесектионс/продукты/перемещение | Нет |Нет |
> | Биллингаккаунтс/Инвоицесектионс/Products/Упдатеауторенев | Нет |Нет |
> | Биллингаккаунтс/Инвоицесектионс/транзакции | Нет |Нет |
> | billingAccounts/invoiceSections/transfers | Нет |Нет |
> | Биллингаккаунтс/Линеофкредит | Нет |Нет |
> | Биллингаккаунтс/Патчоператионс | Нет |Нет |
> | Биллингаккаунтс/Пайментмесодс | Нет |Нет |
> | billingAccounts/products | Нет |Нет |
> | billingAccounts/transactions | Нет |Нет |
> | billingPeriods | Нет |Нет |
> | биллингпермиссионс | Нет |Нет |
> | billingProperty | Нет |Нет |
> | биллингролеассигнментс | Нет |Нет |
> | биллингроледефинитионс | Нет |Нет |
> | креатебиллингролеассигнмент | Нет |Нет |
> | departments | Нет |Нет |
> | enrollmentAccounts | Нет |Нет |
> | invoices | Нет |Нет |
> | transfers | Нет |Нет |
> | transfers/acceptTransfer | Нет |Нет |
> | transfers/declineTransfer | Нет |Нет |
> | transfers/operationStatus | Нет |Нет |
> | валидатеаддресс | Нет |Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | mapApis | Да |Да |
> | updateCommunicationPreference | Нет |Нет |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | BizTalk | Да |Да |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | блоккчаинмемберс | Да |Да |
> | наблюдатели | Да |Да |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Нет |Нет |
> | blueprintAssignments/assignmentOperations | Нет |Нет |
> | blueprintAssignments/operations | Нет |Нет |
> | blueprints | Нет |Нет |
> | blueprints/artifacts | Нет |Нет |
> | blueprints/versions | Нет |Нет |
> | blueprints/versions/artifacts | Нет |Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | botServices | Да |Да |
> | botServices/channels | Нет |Нет |
> | botServices/connections | Нет |Нет |
> | языки | Нет |Нет |
> | шаблоны | Нет |Нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Redis | Да |Да |
> | RedisConfigDefinition | Нет |Нет |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Нет |Нет |
> | калкулатиксчанже | Нет |Нет |
> | calculatePrice | Нет |Нет |
> | калкулатепурчасеприце | Нет |Нет |
> | catalogs | Нет |Нет |
> | commercialReservationOrders | Нет |Нет |
> | обмен валюты | Нет |Нет |
> | плацепурчасеордер | Нет |Нет |
> | reservationOrders | Нет |Нет |
> | reservationOrders/calculateRefund | Нет |Нет |
> | reservationOrders/merge | Нет |Нет |
> | reservationOrders/reservations | Нет |Нет |
> | reservationOrders/reservations/revisions | Нет |Нет |
> | reservationOrders/return | Нет |Нет |
> | reservationOrders/split | Нет |Нет |
> | reservationOrders/swap | Нет |Нет |
> | reservations | Нет |Нет |
> | ресурсы | Нет |Нет |
> | validateReservationOrder | Нет |Нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | кднвебаппликатионфиреваллманажедрулесетс | Нет |Нет |
> | кднвебаппликатионфиреваллполиЦиес | Да |Да |
> | edgenodes | Нет |Нет |
> | profiles | Да |Да |
> | profiles/endpoints | Да |Да |
> | profiles/endpoints/customdomains | Нет |Нет |
> | profiles/endpoints/origins | Нет |Нет |
> | validateProbe | Нет |Нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Да |Да |
> | certificateOrders/certificates | Нет |Нет |
> | validateCertificateRegistrationInformation | Нет |Нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capabilities | Нет |Нет |
> | domainNames | Да |Да |
> | domainNames/capabilities | Нет |Нет |
> | domainNames/internalLoadBalancers | Нет |Нет |
> | domainNames/serviceCertificates | Нет |Нет |
> | domainNames/slots | Нет |Нет |
> | domainNames/slots/roles | Нет |Нет |
> | имя_домена/слоты/роли/metricDefinitions | Нет |Нет |
> | имя_домена/слоты/роли/метрики | Нет |Нет |
> | moveSubscriptionResources | Нет |Нет |
> | operatingSystemFamilies | Нет |Нет |
> | operatingSystems | Нет |Нет |
> | quotas | Нет |Нет |
> | resourceTypes | Нет |Нет |
> | validateSubscriptionMoveAvailability | Нет |Нет |
> | virtualMachines | Да |Да |
> | virtualMachines/diagnosticSettings | Нет |Нет |
> | virtualMachines/metricDefinitions | Нет |Нет |
> | virtualMachines/metrics | Нет |Нет |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Нет |Нет |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capabilities | Нет |Нет |
> | expressRouteCrossConnections | Нет |Нет |
> | expressRouteCrossConnections/peerings | Нет |Нет |
> | gatewaySupportedDevices | Нет |Нет |
> | networkSecurityGroups | Да |Да |
> | quotas | Нет |Нет |
> | reservedIps | Да |Да |
> | virtualNetworks | Да |Да |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Нет |Нет |
> | virtualNetworks/virtualNetworkPeerings | Нет |Нет |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capabilities | Нет |Нет |
> | диски | Нет |Нет |
> | образы | Нет |Нет |
> | osImages | Нет |Нет |
> | osPlatformImages | Нет |Нет |
> | publicImages | Нет |Нет |
> | quotas | Нет |Нет |
> | storageAccounts | Да |Да |
> | storageAccounts/metricDefinitions | Нет |Нет |
> | storageAccounts/метрики | Нет |Нет |
> | storageAccounts/services | Нет |Нет |
> | storageAccounts/services/diagnosticSettings | Нет |Нет |
> | storageAccounts/services/metricDefinitions | Нет |Нет |
> | storageAccounts/Services/метрики | Нет |Нет |
> | storageAccounts/vmImages | Нет |Нет |
> | vmImages | Нет |Нет |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | учетные записи | Да |Да |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | RateCard | Нет |Нет |
> | UsageAggregates | Нет |Нет |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Да |Да |
> | дискенкриптионсетс | Да |Да |
> | диски | Да |Да |
> | galleries | Да |Да |
> | коллекции и приложения | Да |Да |
> | коллекции, приложения и версии | Да |Да |
> | galleries/images | Да |Да |
> | galleries/images/versions | Да |Да |
> | хостграупс | Да |Да |
> | Хостграупс и узлы | Да |Да |
> | образы | Да |Да |
> | проксимитиплацементграупс | Да |Да |
> | restorePointCollections | Да |Да |
> | restorePointCollections/restorePoints | Нет |Нет |
> | sharedVMImages | Да |Да |
> | sharedVMImages/versions | Да |Да |
> | моментальные снимки | Да |Да |
> | virtualMachines | Да |Да |
> | virtualMachines/extensions | Да |Да |
> | virtualMachines/metricDefinitions | Нет |Нет |
> | virtualMachines/Скриптжобс | Нет |Нет |
> | virtualMachines/Софтвареупдатедеплойментс | Нет |Нет |
> | virtualMachineScaleSets | Да |Да |
> | virtualMachineScaleSets/extensions | Нет |Нет |
> | virtualMachineScaleSets/networkInterfaces | Нет |Нет |
> | virtualMachineScaleSets/publicIPAddresses | Нет |Нет |
> | virtualMachineScaleSets/virtualMachines | Нет |Нет |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Нет |Нет |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Нет |Нет |
> | сведения о балансе. | Нет |Нет |
> | Бюджеты | Нет |Нет |
> | Расходы | Нет |Нет |
> | CostTags | Нет |Нет |
> | credits | Нет |Нет |
> | события | Нет |Нет |
> | Прогнозы | Нет |Нет |
> | lots | Нет |Нет |
> | Marketplace | Нет |Нет |
> | Pricesheets | Нет |Нет |
> | products | Нет |Нет |
> | ReservationDetails | Нет |Нет |
> | ReservationRecommendations | Нет |Нет |
> | ReservationSummaries | Нет |Нет |
> | ReservationTransactions | Нет |Нет |
> | Tags | Нет |Нет |
> | tenants | Нет |Нет |
> | Термины | Нет |Нет |
> | UsageDetails | Нет |Нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | containerGroups | Да |Да |
> | serviceAssociationLinks | Нет |Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | registries | Да |Да |
> | registries/builds | Нет |Нет |
> | registries/builds/cancel | Нет |Нет |
> | registries/builds/getLogLink | Нет |Нет |
> | registries/buildTasks | Да |Да |
> | registries/buildTasks/steps | Нет |Нет |
> | registries/eventGridFilters | Нет |Нет |
> | registries/getBuildSourceUploadUrl | Нет |Нет |
> | registries/GetCredentials | Нет |Нет |
> | registries/importImage | Нет |Нет |
> | registries/queueBuild | Нет |Нет |
> | registries/regenerateCredential | Нет |Нет |
> | registries/regenerateCredentials | Нет |Нет |
> | registries/replications | Да |Да |
> | registries/runs | Нет |Нет |
> | registries/runs/cancel | Нет |Нет |
> | registries/scheduleRun | Нет |Нет |
> | registries/tasks | Да |Да |
> | registries/updatePolicies | Нет |Нет |
> | registries/webhooks | Да |Да |
> | registries/webhooks/getCallbackConfig | Нет |Нет |
> | registries/webhooks/ping | Нет |Нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | containerServices | Да |Да |
> | managedClusters | Да |Да |
> | опеншифтманажедклустерс | Да |Да |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | приложения | Да |Да |
> | updateCommunicationPreference | Нет |Нет |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | учетные записи | Да |Да |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Предупреждения | Нет |Нет |
> | BillingAccounts | Нет |Нет |
> | Бюджеты | Нет |Нет |
> | клаудконнекторс | Нет |Нет |
> | Соединители | Да |Да |
> | Departments | Нет |Нет |
> | Размеры | Нет |Нет |
> | EnrollmentAccounts | Нет |Нет |
> | Экспортируемые элементы | Нет |Нет |
> | екстерналбиллингаккаунтс | Нет |Нет |
> | Екстерналбиллингаккаунтс и оповещения | Нет |Нет |
> | Екстерналбиллингаккаунтс и измерения | Нет |Нет |
> | Екстерналбиллингаккаунтс/прогноз | Нет |Нет |
> | Екстерналбиллингаккаунтс/запрос | Нет |Нет |
> | екстерналсубскриптионс | Нет |Нет |
> | Екстерналсубскриптионс и оповещения | Нет |Нет |
> | Екстерналсубскриптионс и измерения | Нет |Нет |
> | Екстерналсубскриптионс/прогноз | Нет |Нет |
> | Екстерналсубскриптионс/запрос | Нет |Нет |
> | Прогноз | Нет |Нет |
> | Запрос | Нет |Нет |
> | регистрация | Нет |Нет |
> | Reportconfigs | Нет |Нет |
> | Отчеты | Нет |Нет |
> | Настройки | Нет |Нет |
> | шовбаккрулес | Нет |Нет |
> | Представления | Нет |Нет |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | hubs | Да |Да |
> | hubs/authorizationPolicies | Нет |Нет |
> | hubs/connectors | Нет |Нет |
> | hubs/connectors/mappings | Нет |Нет |
> | hubs/interactions | Нет |Нет |
> | hubs/kpi | Нет |Нет |
> | hubs/links | Нет |Нет |
> | hubs/profiles | Нет |Нет |
> | hubs/roleAssignments | Нет |Нет |
> | hubs/roles | Нет |Нет |
> | hubs/suggestTypeSchema | Нет |Нет |
> | hubs/views | Нет |Нет |
> | hubs/widgetTypes | Нет |Нет |

## <a name="microsoftcustomerlockbox"></a>Microsoft. Кустомерлоккбокс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | запросы | Нет |Нет |

## <a name="microsoftcustomproviders"></a>Microsoft. Кустомпровидерс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | связи | Нет |Нет |
> | ресаурцепровидерс | Да |Да |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | задания | Да |Да |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Да |Да |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | рабочие области | Да |Нет |
> | workspaces/virtualNetworkPeerings | Нет |Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | catalogs | Да |Да |
> | каталоги | Да |Да |
> | каталоги и скантаржетс | Нет |Нет |
> | каталоги данных/скантаржетс/DataSets | Нет |Нет |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | connectionManagers | Да |Да |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | dataFactories | Да |Нет |
> | dataFactories/diagnosticSettings | Нет |Нет |
> | dataFactories/metricDefinitions | Нет |Нет |
> | dataFactorySchema | Нет |Нет |
> | factories | Да |Нет |
> | factories/integrationRuntimes | Нет |Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | учетные записи | Да |Да |
> | accounts/dataLakeStoreAccounts | Нет |Нет |
> | accounts/storageAccounts | Нет |Нет |
> | accounts/storageAccounts/containers | Нет |Нет |
> | Accounts/Трансфераналитиксунитс | Нет |Нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | учетные записи | Да |Да |
> | accounts/eventGridFilters | Нет |Нет |
> | accounts/firewallRules | Нет |Нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | Нет |Нет |
> | services/projects | Нет |Нет |

## <a name="microsoftdatashare"></a>Общая папка Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | учетные записи | Да |Да |
> | учетные записи и общие папки | Нет |Нет |
> | учетные записи, общие ресурсы и наборы данных | Нет |Нет |
> | учетные записи, общие ресурсы и приглашения | Нет |Нет |
> | Accounts/shares/провидершаресубскриптионс | Нет |Нет |
> | Accounts/shares/Синчронизатионсеттингс | Нет |Нет |
> | Accounts/шаресубскриптионс | Нет |Нет |
> | Accounts/шаресубскриптионс/Консумерсаурцедатасетс | Нет |Нет |
> | Accounts/шаресубскриптионс/датасетмаппингс | Нет |Нет |
> | учетные записи/шаресубскриптионс/триггеры | Нет |Нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | серверы | Да |Да |
> | servers/advisors | Нет |Нет |
> | servers/queryTexts | Нет |Нет |
> | servers/recoverableServers | Нет |Нет |
> | servers/topQueryStatistics | Нет |Нет |
> | servers/virtualNetworkRules | Нет |Нет |
> | servers/waitStatistics | Нет |Нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | серверы | Да |Да |
> | servers/advisors | Нет |Нет |
> | servers/queryTexts | Нет |Нет |
> | servers/recoverableServers | Нет |Нет |
> | servers/topQueryStatistics | Нет |Нет |
> | servers/virtualNetworkRules | Нет |Нет |
> | servers/waitStatistics | Нет |Нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | серверграупс | Да |Да |
> | серверы | Да |Да |
> | servers/advisors | Нет |Нет |
> | servers/queryTexts | Нет |Нет |
> | servers/recoverableServers | Нет |Нет |
> | servers/topQueryStatistics | Нет |Нет |
> | servers/virtualNetworkRules | Нет |Нет |
> | servers/waitStatistics | Нет |Нет |
> | serversv2 | Да |Да |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | артифактсаурцес | Да |Да |
> | rollouts | Да |Да |
> | сервицетопологиес | Да |Да |
> | Сервицетопологиес и службы | Да |Да |
> | Сервицетопологиес/Services/Сервицеунитс | Да |Да |
> | steps | Да |Да |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. Десктопвиртуализатион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | аппликатионграупс | Да |Да |
> | аппликатионграупс и приложения | Нет |Нет |
> | аппликатионграупс/ассигнедусерс | Нет |Нет |
> | аппликатионграупс/стартменуитемс | Нет |Нет |
> | хостпулс | Да |Да |
> | хостпулс/сессионхостс | Нет |Нет |
> | хостпулс/сессионхостс/усерсессионс | Нет |Нет |
> | хостпулс/усерсессионс | Нет |Нет |
> | рабочие области | Да |Да |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | еластикпулс | Да |Да |
> | Еластикпулс/Иосубтенантс | Да |Да |
> | IotHubs | Да |Да |
> | IotHubs/eventGridFilters | Нет |Нет |
> | ProvisioningServices | Да |Да |
> | usages | Нет |Нет |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конвейеры | Да |Да |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | controllers | Да |Да |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | labcenters | Да |Да |
> | labs | Да |Да |
> | лаборатории и среды | Да |Да |
> | labs/serviceRunners | Да |Да |
> | labs/virtualMachines | Да |Да |
> | schedules | Да |Да |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Нет |Нет |
> | databaseAccounts | Да |Да |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | domains | Да |Да |
> | domains/domainOwnershipIdentifiers | Нет |Нет |
> | generateSsoRequest | Нет |Нет |
> | topLevelDomains | Нет |Нет |
> | validateDomainRegistrationInformation | Нет |Нет |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Нет |Нет |
> | lcsprojects/clouddeployments | Нет |Нет |
> | lcsprojects/connectors | Нет |Нет |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | Да |Да |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | domains | Да |Да |
> | domains/topics | Нет |Нет |
> | eventSubscriptions | Нет |Нет |
> | extensionTopics | Нет |Нет |
> | topics | Да |Да |
> | topicTypes | Нет |Нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да |Да |
> | пространства имен | Да |Да |
> | namespaces/authorizationrules | Нет |Нет |
> | namespaces/disasterrecoveryconfigs | Нет |Нет |
> | namespaces/eventhubs | Нет |Нет |
> | namespaces/eventhubs/authorizationrules | Нет |Нет |
> | namespaces/eventhubs/consumergroups | Нет |Нет |
> | пространства имен/нетворкрулесетс | Нет |Нет |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | features | Нет |Нет |
> | providers | Нет |Нет |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | зарегистрировать | Нет |Нет |
> | galleryitems | Нет |Нет |
> | generateartifactaccessuri | Нет |Нет |
> | myareas | Нет |Нет |
> | myareas/areas | Нет |Нет |
> | myareas/areas/areas | Нет |Нет |
> | myareas/areas/areas/galleryitems | Нет |Нет |
> | myareas/areas/galleryitems | Нет |Нет |
> | myareas/galleryitems | Нет |Нет |
> | регистрация | Нет |Нет |
> | ресурсы | Нет |Нет |
> | retrieveresourcesbyid | Нет |Нет |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | учетные записи | Да |Да |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | guestConfigurationAssignments | Нет |Нет |
> | software | Нет |Нет |
> | софтвареупдатепрофиле | Нет |Нет |
> | софтвареупдатес | Нет |Нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Да |Да |
> | сапмониторс | Да |Да |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | дедикатедхсмс | Да |Да |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да |Да |
> | clusters/applications | Нет |Нет |

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | Да |Да |

## <a name="microsofthybridcompute"></a>Microsoft. Хибридкомпуте

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Компьютеры | Да |Да |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Диспетчеры | Да |Да |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | components | Да |Да |
> | нетворкскопес | Да |Да |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | задания | Да |Да |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Нет |Нет |
> | diagnosticSettingsCategories | Нет |Нет |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | апптемплатес | Нет |Нет |
> | IoTApps | Да |Да |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Граф | Да |Да |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Нет |Нет |
> | хсмпулс | Да |Да |
> | vaults | Да |Да |
> | vaults/accessPolicies | Нет |Нет |
> | хранилища и Евентгридфилтерс | Нет |Нет |
> | vaults/secrets | Нет |Нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да |Да |
> | кластеры/аттачеддатабасеконфигуратионс | Нет |Нет |
> | clusters/databases | Нет |Нет |
> | clusters/databases/dataconnections | Нет |Нет |
> | clusters/databases/eventhubconnections | Нет |Нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | labaccounts | Да |Да |
> | пользователей | Нет |Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Да |Да |
> | integrationAccounts | Да |Да |
> | интегратионсервицеенвиронментс | Да |Да |
> | исолатеденвиронментс | Да |Да |
> | workflows | Да |Да |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Да |Да |
> | webServices | Да |Да |
> | Рабочие области | Да |Да |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | рабочие области | Да |Да |
> | workspaces/computes | Нет |Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Удостоверения | Нет |Нет |
> | userAssignedIdentities | Да |Да |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | labaccounts | Да |Да |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | маркетплацерегистратиондефинитионс | Нет |Нет |
> | регистратионассигнментс | Нет |Нет |
> | регистратиондефинитионс | Нет |Нет |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | getEntities | Нет |Нет |
> | managementGroups | Нет |Нет |
> | ресурсы | Нет |Нет |
> | startTenantBackfill | Нет |Нет |
> | tenantBackfillStatus | Нет |Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | учетные записи | Да |Да |
> | accounts/eventGridFilters | Нет |Нет |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | offers | Нет |Нет |
> | offerTypes | Нет |Нет |
> | offerTypes/publishers | Нет |Нет |
> | offerTypes/publishers/offers | Нет |Нет |
> | offerTypes/publishers/offers/plans | Нет |Нет |
> | offerTypes/publishers/offers/plans/agreements | Нет |Нет |
> | offerTypes/publishers/offers/plans/configs | Нет |Нет |
> | offerTypes/publishers/offers/plans/configs/importImage | Нет |Нет |
> | privategalleryitems | Нет |Нет |
> | products | Нет |Нет |
> | издатели | Нет |Нет |
> | издатели и предложения | Нет |Нет |
> | издатели, предложения и поправки | Нет |Нет |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Да |Да |
> | updateCommunicationPreference | Нет |Нет |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | agreements | Нет |Нет |
> | offertypes | Нет |Нет |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | mediaservices | Да |Да |
> | mediaservices/accountFilters | Нет |Нет |
> | mediaservices/assets | Нет |Нет |
> | mediaservices/assets/assetFilters | Нет |Нет |
> | mediaservices/contentKeyPolicies | Нет |Нет |
> | mediaservices/eventGridFilters | Нет |Нет |
> | mediaservices/liveEventOperations | Нет |Нет |
> | mediaservices/liveEvents | Да |Да |
> | mediaservices/liveEvents/liveOutputs | Нет |Нет |
> | mediaservices/liveOutputOperations | Нет |Нет |
> | mediaservices/streamingEndpointOperations | Нет |Нет |
> | mediaservices/streamingEndpoints | Да |Да |
> | mediaservices/streamingLocators | Нет |Нет |
> | mediaservices/streamingPolicies | Нет |Нет |
> | mediaservices/transforms | Нет |Нет |
> | mediaservices/transforms/jobs | Нет |Нет |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | аппклустерс | Да |Да |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | ассессментпрожектс | Да |Да |
> | migrateprojects | Да |Да |
> | projects | Да |Да |

## <a name="microsoftmixedreality"></a>Microsoft. Микседреалити

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | ремотерендерингаккаунтс | Да |Да |
> | спатиаланчорсаккаунтс | Да |Да |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | нетаппаккаунтс | Да |Да |
> | Нетаппаккаунтс/КапаЦитипулс | Да |Да |
> | Нетаппаккаунтс/КапаЦитипулс/тома | Да |Да |
> | Нетаппаккаунтс/КапаЦитипулс/Volumes/Маунттаржетс | Да |Да |
> | Нетаппаккаунтс/КапаЦитипулс/тома/моментальные снимки | Да |Да |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Да |Да |
> | аппликатионгатевайвебаппликатионфиреваллполиЦиес | Да |Да |
> | applicationSecurityGroups | Да |Да |
> | azureFirewallFqdnTags | Нет |Нет |
> | azureFirewalls | Да |Да |
> | бастионхостс | Да |Да |
> | bgpServiceCommunities | Нет |Нет |
> | подключения | Да |Да |
> | ddosCustomPolicies | Да |Да |
> | ddosProtectionPlans | Да |Да |
> | dnsOperationStatuses | Нет |Нет |
> | dnszones | Да |Да |
> | dnszones/A | Нет |Нет |
> | dnszones/AAAA | Нет |Нет |
> | dnszones/all | Нет |Нет |
> | dnszones/CAA | Нет |Нет |
> | dnszones/CNAME | Нет |Нет |
> | dnszones/MX | Нет |Нет |
> | dnszones/NS | Нет |Нет |
> | dnszones/PTR | Нет |Нет |
> | dnszones/recordsets | Нет |Нет |
> | dnszones/SOA | Нет |Нет |
> | dnszones/SRV | Нет |Нет |
> | dnszones/TXT | Нет |Нет |
> | expressRouteCircuits | Да |Да |
> | expressRouteCrossConnections | Да |Да |
> | експрессраутегатевайс | Да |Да |
> | експрессраутепортс | Да |Да |
> | expressRouteServiceProviders | Нет |Нет |
> | фиреваллполиЦиес | Да |Да |
> | frontdoor | Да, но ограничено (см. [Примечание ниже](#frontdoor)) |Да |
> | фронтдурвебаппликатионфиреваллманажедрулесетс | Да, но ограничено (см. [Примечание ниже](#frontdoor)) |Нет |
> | frontdoorWebApplicationFirewallPolicies | Да, но ограничено (см. [Примечание ниже](#frontdoor)) |Да |
> | getDnsResourceReference | Нет |Нет |
> | internalNotify | Нет |Нет |
> | loadBalancers | Да |Нет |
> | localNetworkGateways | Да |Да |
> | natGateways | Да |Да |
> | networkIntentPolicies | Да |Да |
> | networkInterfaces | Да |Да |
> | networkProfiles | Да |Да |
> | networkSecurityGroups | Да |Да |
> | networkWatchers | Да |Нет |
> | networkWatchers/connectionMonitors | Да |Нет |
> | networkWatchers/lenses | Да |Нет |
> | networkWatchers/pingMeshes | Да |Нет |
> | p2sVpnGateways | Да |Да |
> | приватеднсоператионстатусес | Нет |Нет |
> | приватеднсзонес | Да |Да |
> | Приватеднсзонес/A | Нет |Нет |
> | Приватеднсзонес/AAAA | Нет |Нет |
> | Приватеднсзонес/все | Нет |Нет |
> | Приватеднсзонес и CNAME | Нет |Нет |
> | Приватеднсзонес/MX | Нет |Нет |
> | Приватеднсзонес/PTR | Нет |Нет |
> | Приватеднсзонес/SOA | Нет |Нет |
> | Приватеднсзонес/SRV | Нет |Нет |
> | Приватеднсзонес/TXT | Нет |Нет |
> | Приватеднсзонес/Виртуалнетворклинкс | Да |Да |
> | приватиндпоинтс | Да |Да |
> | privateLinkServices | Да |Да |
> | publicIPAddresses | Да |Да |
> | publicIPPrefixes | Да |Да |
> | routeFilters | Да |Да |
> | routeTables | Да |Да |
> | секурегатевайс | Да |Да |
> | serviceEndpointPolicies | Да |Да |
> | trafficManagerGeographicHierarchies | Нет |Нет |
> | trafficmanagerprofiles | Да |Да |
> | trafficmanagerprofiles/heatMaps | Нет |Нет |
> | траффикманажерусерметрикскэйс | Нет |Нет |
> | virtualHubs | Да |Да |
> | virtualNetworkGateways | Да |Да |
> | virtualNetworks | Да |Да |
> | virtualNetworkTaps | Да |Да |
> | virtualWans | Да |Да |
> | vpnGateways | Да |Нет |
> | vpnSites | Да |Да |
> | webApplicationFirewallPolicies | Да |Да |

<a id="frontdoor" />

> [!NOTE]
> Для службы "Передняя дверца Azure" можно применять теги при создании ресурса, но обновление или добавление тегов в настоящее время не поддерживается.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да |Нет |
> | namespaces/notificationHubs | Да |Нет |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хипервситес | Да |Да |
> | импортситес | Да |Да |
> | серверситес | Да |Да |
> | вмвареситес | Да |Да |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | устройства | Нет |Нет |
> | linkTargets | Нет |Нет |
> | storageInsightConfigs | Нет |Нет |
> | рабочие области | Да |Да |
> | workspaces/dataSources | Нет |Нет |
> | workspaces/linkedServices | Нет |Нет |
> | workspaces/query | Нет |Нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managementassociations | Нет |Нет |
> | managementconfigurations | Да |Да |
> | решения | Да |Да |
> | просмотры | Да |Да |

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | легаципирингс | Нет |Нет |
> | пираснс | Нет |Нет |
> | пиринги | Да |Да |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | policyEvents | Нет |Нет |
> | policyStates | Нет |Нет |
> | policyTrackedResources | Нет |Нет |
> | remediations | Нет |Нет |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | consoles | Нет |Нет |
> | dashboards | Да |Да |
> | userSettings | Нет |Нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Да |Да |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capacities | Да |Да |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Нет |Нет |
> | vaults | Да |Да |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да |Да |
> | namespaces/authorizationrules | Нет |Нет |
> | namespaces/hybridconnections | Нет |Нет |
> | namespaces/hybridconnections/authorizationrules | Нет |Нет |
> | namespaces/wcfrelays | Нет |Нет |
> | namespaces/wcfrelays/authorizationrules | Нет |Нет |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | учетные записи | Нет |Нет |
> | семейства | Да |Да |
> | коллекции и приложения | Нет |Нет |
> | Collections/секуритипринЦипалс | Нет |Нет |
> | темплатеимажес | Нет |Нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Запросы | Да |Да |
> | ресаурцечанжедетаилс | Нет |Нет |
> | ресаурцечанжес | Нет |Нет |
> | ресурсы | Нет |Нет |
> | ресаурцешистори | Нет |Нет |
> | subscriptionsStatus | Нет |Нет |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Нет |Нет |
> | childAvailabilityStatuses | Нет |Нет |
> | childResources | Нет |Нет |
> | события | Нет |Нет |
> | impactedResources | Нет |Нет |
> | метаданные | Нет |Нет |
> | уведомления | Нет |Нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | развертывания | Нет |Нет |
> | deployments/operations | Нет |Нет |
> | links | Нет |Нет |
> | notifyResourceJobs | Нет |Нет |
> | providers | Нет |Нет |
> | resourceGroups | Нет |Нет |
> | ресурсы | Нет |Нет |
> | — подписки | Нет |Нет |
> | subscriptions/providers | Нет |Нет |
> | subscriptions/resourceGroups | Нет |Нет |
> | subscriptions/resourcegroups/resources | Нет |Нет |
> | subscriptions/resources | Нет |Нет |
> | subscriptions/tagnames | Нет |Нет |
> | subscriptions/tagNames/tagValues | Нет |Нет |
> | теги | Нет |Нет |
> | tenants | Нет |Нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | приложения | Да |Да |
> | saasresources | Нет |Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Потоки | Да |Да |
> | jobcollections | Да |Да |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Нет |Нет |
> | searchServices | Да |Да |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | адаптивенетворкхарденингс | Нет |Нет |
> | advancedThreatProtectionSettings | Нет |Нет |
> | оповещений | Нет |Нет |
> | allowedConnections | Нет |Нет |
> | applicationWhitelistings | Нет |Нет |
> | ассессментметадата | Нет |Нет |
> | оценки | Нет |Нет |
> | AutoProvisioningSettings | Нет |Нет |
> | Compliances | Нет |Нет |
> | dataCollectionAgents | Нет |Нет |
> | девицесекуритиграупс | Нет |Нет |
> | discoveredSecuritySolutions | Нет |Нет |
> | externalSecuritySolutions | Нет |Нет |
> | InformationProtectionPolicies | Нет |Нет |
> | иотсекуритисолутионс | Да |Да |
> | Иотсекуритисолутионс/Аналитиксмоделс | Нет |Нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедалертс | Нет |Нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедрекоммендатионс | Нет |Нет |
> | jitNetworkAccessPolicies | Нет |Нет |
> | плайбукконфигуратионс | Да |Да |
> | политики | Нет |Нет |
> | pricings | Нет |Нет |
> | регулаторикомплианцестандардс | Нет |Нет |
> | Регулаторикомплианцестандардс/Регулаторикомплианцеконтролс | Нет |Нет |
> | Регулаторикомплианцестандардс/Регулаторикомплианцеконтролс/Регулаторикомплианцеассессментс | Нет |Нет |
> | securityContacts | Нет |Нет |
> | securitySolutions | Нет |Нет |
> | securitySolutionsReferenceData | Нет |Нет |
> | securityStatuses | Нет |Нет |
> | securityStatusesSummaries | Нет |Нет |
> | сервервулнерабилитяссессментс | Нет |Нет |
> | параметры | Нет |Нет |
> | задачи; | Нет |Нет |
> | topologies | Нет |Нет |
> | workspaceSettings | Нет |Нет |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Нет |Нет |
> | diagnosticSettingsCategories | Нет |Нет |

## <a name="microsoftsecurityinsights"></a>Microsoft. Секуритинсигхтс

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | агрегатов | Нет |Нет |
> | alertRules | Нет |Нет |
> | закладки | Нет |Нет |
> | cases | Нет |Нет |
> | Подключения к компонентам | Нет |Нет |
> | сущности | Нет |Нет |
> | ентитикуериес | Нет |Нет |
> | оффицеконсентс | Нет |Нет |
> | параметры | Нет |Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да |Нет |
> | namespaces/authorizationrules | Нет |Нет |
> | namespaces/disasterrecoveryconfigs | Нет |Нет |
> | namespaces/eventgridfilters | Нет |Нет |
> | пространства имен/нетворкрулесетс | Нет |Нет |
> | namespaces/queues | Нет |Нет |
> | namespaces/queues/authorizationrules | Нет |Нет |
> | namespaces/topics | Нет |Нет |
> | namespaces/topics/authorizationrules | Нет |Нет |
> | namespaces/topics/subscriptions | Нет |Нет |
> | namespaces/topics/subscriptions/rules | Нет |Нет |
> | premiumMessagingRegions | Нет |Нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | приложения | Да |Да |
> | clusters | Да |Да |
> | clusters/applications | Нет |Нет |
> | containerGroups | Да |Да |
> | контаинерграупсетс | Да |Да |
> | edgeclusters | Да |Да |
> | еджеклустерс и приложения | Нет |Нет |
> | Сети | Да |Да |
> | secretstores | Да |Да |
> | секретсторес и сертификаты | Нет |Нет |
> | секретсторес/секреты | Нет |Нет |
> | тома | Да |Да |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | приложения | Да |Да |
> | containerGroups | Да |Да |
> | gateways | Да |Да |
> | Сети | Да |Да |
> | секреты | Да |Да |
> | тома | Да |Да |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Нет |Нет |
> | Провидеррегистратионс/Ресаурцетиперегистратионс | Нет |Нет |
> | rollouts | Да |Да |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SignalR | Да |Да |
> | SignalR/Евентгридфилтерс | Нет |Нет |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Да |Да |

## <a name="microsoftsoftwareplan"></a>Microsoft. Софтвареплан

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хибридусебенефитс | Нет |Нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Да |Да |
> | приложения | Да |Да |
> | jitRequests | Да |Да |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managedInstances | Да | Да |
> | managedInstances/databases | Да (см. [Примечание ниже](#sqlnote)) | Да |
> | managedInstances/databases/backupShortTermRetentionPolicies | Нет | Нет |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Нет | Нет |
> | managedInstances/databases/vulnerabilityAssessments | Нет | Нет |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Нет | Нет |
> | managedInstances/encryptionProtector | Нет | Нет |
> | managedInstances/keys | Нет | Нет |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Нет | Нет |
> | managedInstances/vulnerabilityAssessments | Нет | Нет |
> | серверы | Да | Да |
> | servers/administrators | Нет | Нет |
> | servers/communicationLinks | Нет | Нет |
> | servers/databases | Да (см. [Примечание ниже](#sqlnote)) | Да |
> | servers/encryptionProtector | Нет | Нет |
> | servers/firewallRules | Нет | Нет |
> | servers/keys | Нет | Нет |
> | servers/restorableDroppedDatabases | Нет | Нет |
> | servers/serviceobjectives | Нет | Нет |
> | servers/tdeCertificates | Нет | Нет |

<a id="sqlnote" />

> [!NOTE]
> База данных Master не поддерживает теги, но другие базы данных, в том числе базы данных хранилища данных Azure SQL, поддерживают их. Базы данных Хранилища данных SQL Azure должны быть в активном состоянии (работа не приостановлена).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Да |Да |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Нет |Нет |
> | SqlVirtualMachines | Да |Да |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Да |Да |
> | storageAccounts/blobServices | Нет |Нет |
> | storageAccounts/fileServices | Нет |Нет |
> | storageAccounts/queueServices | Нет |Нет |
> | storageAccounts/services | Нет |Нет |
> | storageAccounts/services/metricDefinitions | Нет |Нет |
> | storageAccounts/tableServices | Нет |Нет |
> | usages | Нет |Нет |

## <a name="microsoftstoragecache"></a>Microsoft. Сторажекаче

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | кэширует | Да |Да |
> | кэши/Сторажетаржетс | Нет |Нет |
> | усажемоделс | Нет |Нет |

## <a name="microsoftstoragereplication"></a>Microsoft. Сторажерепликатион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | репликатионграупс | Нет |Нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да |Да |
> | storageSyncServices/registeredServers | Нет |Нет |
> | storageSyncServices/syncGroups | Нет |Нет |
> | storageSyncServices/syncGroups/cloudEndpoints | Нет |Нет |
> | storageSyncServices/syncGroups/serverEndpoints | Нет |Нет |
> | storageSyncServices/workflows | Нет |Нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да |Да |
> | storageSyncServices/registeredServers | Нет |Нет |
> | storageSyncServices/syncGroups | Нет |Нет |
> | storageSyncServices/syncGroups/cloudEndpoints | Нет |Нет |
> | storageSyncServices/syncGroups/serverEndpoints | Нет |Нет |
> | storageSyncServices/workflows | Нет |Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да |Да |
> | storageSyncServices/registeredServers | Нет |Нет |
> | storageSyncServices/syncGroups | Нет |Нет |
> | storageSyncServices/syncGroups/cloudEndpoints | Нет |Нет |
> | storageSyncServices/syncGroups/serverEndpoints | Нет |Нет |
> | storageSyncServices/workflows | Нет |Нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managers | Да |Да |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Да (см. примечание ниже) |Да |

> [!NOTE]
> Невозможно добавить тег при выполнении streamingjobs. Остановите ресурс, чтобы добавить тег.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | отмена | Нет |Нет |
> | CreateSubscription | Нет |Нет |
> | переименовать | Нет |Нет |
> | SubscriptionDefinitions | Нет |Нет |
> | SubscriptionOperations | Нет |Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | environments | Да |Нет |
> | environments/accessPolicies | Нет |Нет |
> | environments/eventsources | Да |Нет |
> | environments/referenceDataSets | Да |Нет |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | дедикатедклауднодес | Да |Да |
> | дедикатедклаудсервицес | Да |Да |
> | virtualMachines | Да |Да |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Нет |Нет |
> | apiManagementAccounts/apiAcls | Нет |Нет |
> | apiManagementAccounts/apis | Нет |Нет |
> | apiManagementAccounts/apis/apiAcls | Нет |Нет |
> | apiManagementAccounts/apis/connectionAcls | Нет |Нет |
> | apiManagementAccounts/apis/connections | Нет |Нет |
> | apiManagementAccounts/apis/connections/connectionAcls | Нет |Нет |
> | apiManagementAccounts/apis/localizedDefinitions | Нет |Нет |
> | apiManagementAccounts/connectionAcls | Нет |Нет |
> | apiManagementAccounts/connections | Нет |Нет |
> | billingMeters | Нет |Нет |
> | сертификаты | Да |Да |
> | connectionGateways | Да |Да |
> | подключения | Да |Да |
> | customApis | Да |Да |
> | deletedSites | Нет |Нет |
> | функц. | Нет |Нет |
> | hostingEnvironments | Да |Да |
> | hostingEnvironments/multiRolePools | Нет |Нет |
> | hostingEnvironments/workerPools | Нет |Нет |
> | publishingUsers | Нет |Нет |
> | рекомендации | Нет |Нет |
> | resourceHealthMetadata | Нет |Нет |
> | runtimes | Нет |Нет |
> | serverFarms | Да |Да |
> | serverFarms/Евентгридфилтерс | Нет |Нет |
> | сайты | Да |Да |
> | сайты и конфигурация  | Нет | Нет |
> | Sites/Евентгридфилтерс | Нет |Нет |
> | sites/hostNameBindings | Нет |Нет |
> | Sites/файл networkconfig | Нет |Нет |
> | sites/premieraddons | Да |Да |
> | sites/slots | Да |Да |
> | сайты/слоты/Евентгридфилтерс | Нет |Нет |
> | sites/slots/hostNameBindings | Нет |Нет |
> | сайты/слоты/файл networkconfig | Нет |Нет |
> | sourceControls | Нет |Нет |
> | validate | Нет |Нет |
> | verifyHostingEnvironmentVnet | Нет |Нет |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Нет |Нет |
> | diagnosticSettingsCategories | Нет |Нет |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Да |Да |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | components | Нет |Нет |
> | componentsSummary | Нет |Нет |
> | monitorInstances | Нет |Нет |
> | monitorInstancesSummary | Нет |Нет |
> | monitors | Нет |Нет |
> | notificationSettings | Нет |Нет |

## <a name="next-steps"></a>Следующие шаги

Сведения о том, как применить теги к ресурсам, см. в статье, посвященной [использованию тегов для организации ресурсов Azure](resource-group-using-tags.md).
