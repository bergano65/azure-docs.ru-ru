---
title: Поддержка тегов Azure Resource Manager для ресурсов
description: В этой статье описываются типы ресурсов Azure, поддерживающие теги, а также приводятся сведения о всех службах Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/27/2019
ms.author: tomfitz
ms.openlocfilehash: 6b64eb955476a8ab5307b7d508484c290fd7660a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162173"
---
# <a name="tag-support-for-azure-resources"></a>Поддержка тегов для ресурсов Azure
В этой статье приводятся сведения о поддержке типом ресурса функции [тегов](resource-group-using-tags.md). Столбец с подписью **поддерживает теги** . указывает, имеет ли тип ресурса свойство для тега. Столбец с меткой **тег в отчете о затратах** указывает, передает ли этот тип ресурса тег в отчет о затратах.

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
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | DomainServices | ДА | ДА |
> | DomainServices/oucontainer | Нет | Нет |
> | DomainServices/Репликасетс | ДА | ДА |

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
> | actionRules | ДА | ДА |
> | оповещения | Нет | Нет |
> | alertsList | Нет | Нет |
> | алертсметадата | Нет | Нет |
> | alertsSummary | Нет | Нет |
> | alertsSummaryList | Нет | Нет |
> | Оставлять | Нет | Нет |
> | smartDetectorAlertRules | ДА | ДА |
> | smartDetectorRuntimeEnvironments | Нет | Нет |
> | smartGroups | Нет | Нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | серверы | ДА | ДА |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Нет | Нет |
> | Служба | ДА | ДА |
> | validateServiceName | Нет | Нет |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конфигуратионсторес | ДА | ДА |
> | Конфигуратионсторес/Евентгридфилтерс | Нет | Нет |

## <a name="microsoftappplatform"></a>Microsoft. Аппплатформ

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Spring | ДА | ДА |

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
> | automationAccounts | ДА | ДА |
> | automationAccounts/configurations | ДА | ДА |
> | automationAccounts/jobs | Нет | Нет |
> | automationAccounts/runbooks | ДА | ДА |
> | automationAccounts/softwareUpdateConfigurations | Нет | Нет |
> | automationAccounts/webhooks | Нет | Нет |

## <a name="microsoftazconfig"></a>Microsoft. Азконфиг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конфигуратионсторес | ДА | ДА |
> | Конфигуратионсторес/Евентгридфилтерс | Нет | Нет |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | environments | Нет | Нет |
> | environments/accounts | Нет | Нет |
> | environments/accounts/namespaces | Нет | Нет |
> | environments/accounts/namespaces/configurations | Нет | Нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | ДА | Нет |
> | b2ctenants | Нет | Нет |

## <a name="microsoftazuredata"></a>Microsoft. Азуредата

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хибриддатаманажерс | ДА | ДА |
> | постгресинстанцес | ДА | ДА |
> | склбигдатаклустерс | ДА | ДА |
> | склинстанцес | ДА | ДА |
> | склсерверрегистратионс | ДА | ДА |
> | Склсерверрегистратионс и sqlServer | Нет | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | registrations | ДА | ДА |
> | registrations/customerSubscriptions | Нет | Нет |
> | registrations/products | Нет | Нет |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | batchAccounts | ДА | ДА |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Нет | Нет |
> | Биллингаккаунтс и соглашения | Нет | Нет |
> | Биллингаккаунтс/Биллингпермиссионс | Нет | Нет |
> | billingAccounts/billingProfiles | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингпермиссионс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингролеассигнментс | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингроледефинитионс | Нет | Нет |
> | billingAccounts/billingProfiles/billingSubscriptions | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Креатебиллингролеассигнмент | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/клиенты | Нет | Нет |
> | billingAccounts/billingProfiles/invoices | Нет | Нет |
> | billingAccounts/billingProfiles/invoices/pricesheet | Нет | Нет |
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
> | billingAccounts/billingProfiles/paymentMethods | Нет | Нет |
> | billingAccounts/billingProfiles/policies | Нет | Нет |
> | billingAccounts/billingProfiles/pricesheet | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Прицешитдовнлоадоператионс | Нет | Нет |
> | billingAccounts/billingProfiles/products | Нет | Нет |
> | billingAccounts/billingProfiles/transactions | Нет | Нет |
> | Биллингаккаунтс/Биллингролеассигнментс | Нет | Нет |
> | Биллингаккаунтс/Биллингроледефинитионс | Нет | Нет |
> | billingAccounts/billingSubscriptions | Нет | Нет |
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
> | billingAccounts/departments | Нет | Нет |
> | billingAccounts/enrollmentAccounts | Нет | Нет |
> | billingAccounts/invoices | Нет | Нет |
> | billingAccounts/invoiceSections | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионмовеоператионс | Нет | Нет |
> | billingAccounts/invoiceSections/billingSubscriptions | Нет | Нет |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/повышение прав | Нет | Нет |
> | billingAccounts/invoiceSections/initiateTransfer | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Патчоператионс | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Продуктмовеоператионс | Нет | Нет |
> | billingAccounts/invoiceSections/products | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/продукты/перемещение | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Products/Упдатеауторенев | Нет | Нет |
> | Биллингаккаунтс/Инвоицесектионс/транзакции | Нет | Нет |
> | billingAccounts/invoiceSections/transfers | Нет | Нет |
> | Биллингаккаунтс/Линеофкредит | Нет | Нет |
> | Биллингаккаунтс/Патчоператионс | Нет | Нет |
> | Биллингаккаунтс/Пайментмесодс | Нет | Нет |
> | billingAccounts/products | Нет | Нет |
> | billingAccounts/transactions | Нет | Нет |
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
> | transfers/acceptTransfer | Нет | Нет |
> | transfers/declineTransfer | Нет | Нет |
> | transfers/operationStatus | Нет | Нет |
> | передачи/Валидатетрансфер | Нет | Нет |
> | валидатеаддресс | Нет | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | mapApis | ДА | ДА |
> | updateCommunicationPreference | Нет | Нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | блоккчаинмемберс | ДА | ДА |
> | кордамемберс | ДА | ДА |
> | наблюдатели | ДА | ДА |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Нет | Нет |
> | blueprintAssignments/assignmentOperations | Нет | Нет |
> | blueprintAssignments/operations | Нет | Нет |
> | blueprints | Нет | Нет |
> | blueprints/artifacts | Нет | Нет |
> | blueprints/versions | Нет | Нет |
> | blueprints/versions/artifacts | Нет | Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | botServices | ДА | ДА |
> | botServices/channels | Нет | Нет |
> | botServices/connections | Нет | Нет |
> | языки | Нет | Нет |
> | шаблоны | Нет | Нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Redis | ДА | ДА |
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
> | exchange | Нет | Нет |
> | плацепурчасеордер | Нет | Нет |
> | reservationOrders | Нет | Нет |
> | reservationOrders/calculateRefund | Нет | Нет |
> | reservationOrders/merge | Нет | Нет |
> | reservationOrders/reservations | Нет | Нет |
> | reservationOrders/reservations/revisions | Нет | Нет |
> | reservationOrders/return | Нет | Нет |
> | reservationOrders/split | Нет | Нет |
> | reservationOrders/swap | Нет | Нет |
> | reservations | Нет | Нет |
> | ресурсов | Нет | Нет |
> | validateReservationOrder | Нет | Нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | кднвебаппликатионфиреваллманажедрулесетс | Нет | Нет |
> | кднвебаппликатионфиреваллполиЦиес | ДА | ДА |
> | edgenodes | Нет | Нет |
> | профили | ДА | ДА |
> | profiles/endpoints | ДА | ДА |
> | profiles/endpoints/customdomains | Нет | Нет |
> | profiles/endpoints/origins | Нет | Нет |
> | validateProbe | Нет | Нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | certificateOrders | ДА | ДА |
> | certificateOrders/certificates | Нет | Нет |
> | validateCertificateRegistrationInformation | Нет | Нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capabilities | Нет | Нет |
> | domainNames | Нет | Нет |
> | domainNames/capabilities | Нет | Нет |
> | domainNames/internalLoadBalancers | Нет | Нет |
> | domainNames/serviceCertificates | Нет | Нет |
> | domainNames/slots | Нет | Нет |
> | domainNames/slots/roles | Нет | Нет |
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
> | virtualMachines/metrics | Нет | Нет |

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
> | expressRouteCrossConnections/peerings | Нет | Нет |
> | gatewaySupportedDevices | Нет | Нет |
> | networkSecurityGroups | Нет | Нет |
> | quotas | Нет | Нет |
> | reservedIps | Нет | Нет |
> | virtualNetworks | Нет | Нет |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Нет | Нет |
> | virtualNetworks/virtualNetworkPeerings | Нет | Нет |

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
> | storageAccounts/blobServices | Нет | Нет |
> | storageAccounts/fileServices | Нет | Нет |
> | storageAccounts/metricDefinitions | Нет | Нет |
> | storageAccounts/метрики | Нет | Нет |
> | storageAccounts/queueServices | Нет | Нет |
> | storageAccounts/services | Нет | Нет |
> | storageAccounts/services/diagnosticSettings | Нет | Нет |
> | storageAccounts/services/metricDefinitions | Нет | Нет |
> | storageAccounts/Services/метрики | Нет | Нет |
> | storageAccounts/tableServices | Нет | Нет |
> | storageAccounts/vmImages | Нет | Нет |
> | vmImages | Нет | Нет |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | ДА | ДА |

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
> | availabilitySets | ДА | ДА |
> | дискенкриптионсетс | ДА | ДА |
> | disks | ДА | ДА |
> | galleries | ДА | ДА |
> | коллекции и приложения | Нет | Нет |
> | коллекции, приложения и версии | Нет | Нет |
> | galleries/images | Нет | Нет |
> | galleries/images/versions | Нет | Нет |
> | хостграупс | ДА | ДА |
> | Хостграупс и узлы | ДА | ДА |
> | images | ДА | ДА |
> | проксимитиплацементграупс | ДА | ДА |
> | restorePointCollections | ДА | ДА |
> | restorePointCollections/restorePoints | Нет | Нет |
> | шаредвмекстенсионс | ДА | ДА |
> | Шаредвмекстенсионс и версии | Нет | Нет |
> | sharedVMImages | ДА | ДА |
> | sharedVMImages/versions | Нет | Нет |
> | snapshots | ДА | ДА |
> | virtualMachines | ДА | ДА |
> | virtualMachines/extensions | ДА | ДА |
> | virtualMachines/metricDefinitions | Нет | Нет |
> | virtualMachineScaleSets | ДА | ДА |
> | virtualMachineScaleSets/extensions | Нет | Нет |
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
> | Расходы | Нет | Нет |
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
> | containerGroups | ДА | ДА |
> | serviceAssociationLinks | Нет | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | registries | ДА | ДА |
> | registries/builds | Нет | Нет |
> | registries/builds/cancel | Нет | Нет |
> | registries/builds/getLogLink | Нет | Нет |
> | registries/buildTasks | ДА | ДА |
> | registries/buildTasks/steps | Нет | Нет |
> | registries/eventGridFilters | Нет | Нет |
> | реестры и Женератекредентиалс | Нет | Нет |
> | registries/getBuildSourceUploadUrl | Нет | Нет |
> | registries/GetCredentials | Нет | Нет |
> | registries/importImage | Нет | Нет |
> | registries/queueBuild | Нет | Нет |
> | registries/regenerateCredential | Нет | Нет |
> | registries/regenerateCredentials | Нет | Нет |
> | registries/replications | ДА | ДА |
> | registries/runs | Нет | Нет |
> | registries/runs/cancel | Нет | Нет |
> | registries/scheduleRun | Нет | Нет |
> | реестры и Скопемапс | Нет | Нет |
> | registries/tasks | ДА | ДА |
> | реестры и маркеры | Нет | Нет |
> | registries/updatePolicies | Нет | Нет |
> | registries/webhooks | ДА | ДА |
> | registries/webhooks/getCallbackConfig | Нет | Нет |
> | registries/webhooks/ping | Нет | Нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | containerServices | ДА | ДА |
> | managedClusters | ДА | ДА |
> | опеншифтманажедклустерс | ДА | ДА |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | ДА | ДА |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Оповещения | Нет | Нет |
> | BillingAccounts | Нет | Нет |
> | Расходы | Нет | Нет |
> | клаудконнекторс | Нет | Нет |
> | Коннекторы | ДА | ДА |
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
> | Настройки | Нет | Нет |
> | шовбаккрулес | Нет | Нет |
> | Представления | Нет | Нет |

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
> | ресаурцепровидерс | ДА | ДА |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | jobs | ДА | ДА |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | ДА | ДА |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | workspaces | ДА | Нет |
> | workspaces/virtualNetworkPeerings | Нет | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | catalogs | ДА | ДА |
> | каталоги | ДА | ДА |
> | каталоги данных и источники данных | Нет | Нет |
> | каталоги данных, источники данных и проверки | Нет | Нет |
> | каталоги данных/DataSources/scans/DataSets | Нет | Нет |
> | каталоги данных, источники данных, проверки и триггеры | Нет | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | dataFactories | ДА | Нет |
> | dataFactories/diagnosticSettings | Нет | Нет |
> | dataFactories/metricDefinitions | Нет | Нет |
> | dataFactorySchema | Нет | Нет |
> | factories | ДА | Нет |
> | factories/integrationRuntimes | Нет | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | ДА | ДА |
> | accounts/dataLakeStoreAccounts | Нет | Нет |
> | accounts/storageAccounts | Нет | Нет |
> | accounts/storageAccounts/containers | Нет | Нет |
> | Accounts/Трансфераналитиксунитс | Нет | Нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | ДА | ДА |
> | accounts/eventGridFilters | Нет | Нет |
> | accounts/firewallRules | Нет | Нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | Нет | Нет |
> | services/projects | Нет | Нет |

## <a name="microsoftdatashare"></a>Общая папка Microsoft.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | ДА | ДА |
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
> | серверы | ДА | ДА |
> | servers/advisors | Нет | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет | Нет |
> | серверы и Привателинкресаурцес | Нет | Нет |
> | servers/queryTexts | Нет | Нет |
> | servers/recoverableServers | Нет | Нет |
> | servers/topQueryStatistics | Нет | Нет |
> | servers/virtualNetworkRules | Нет | Нет |
> | servers/waitStatistics | Нет | Нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | серверы | ДА | ДА |
> | servers/advisors | Нет | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет | Нет |
> | серверы и Привателинкресаурцес | Нет | Нет |
> | servers/queryTexts | Нет | Нет |
> | servers/recoverableServers | Нет | Нет |
> | servers/topQueryStatistics | Нет | Нет |
> | servers/virtualNetworkRules | Нет | Нет |
> | servers/waitStatistics | Нет | Нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | серверграупс | ДА | ДА |
> | серверы | ДА | ДА |
> | servers/advisors | Нет | Нет |
> | servers/keys | Нет | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет | Нет |
> | серверы и Привателинкресаурцес | Нет | Нет |
> | servers/queryTexts | Нет | Нет |
> | servers/recoverableServers | Нет | Нет |
> | servers/topQueryStatistics | Нет | Нет |
> | servers/virtualNetworkRules | Нет | Нет |
> | servers/waitStatistics | Нет | Нет |
> | serversv2 | ДА | ДА |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | артифактсаурцес | ДА | ДА |
> | rollouts | ДА | ДА |
> | сервицетопологиес | ДА | ДА |
> | Сервицетопологиес и службы | ДА | ДА |
> | Сервицетопологиес/Services/Сервицеунитс | ДА | ДА |
> | steps | ДА | ДА |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. Десктопвиртуализатион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | аппликатионграупс | ДА | ДА |
> | аппликатионграупс и приложения | Нет | Нет |
> | аппликатионграупс и настольные системы | Нет | Нет |
> | аппликатионграупс/стартменуитемс | Нет | Нет |
> | хостпулс | ДА | ДА |
> | хостпулс/сессионхостс | Нет | Нет |
> | хостпулс/сессионхостс/усерсессионс | Нет | Нет |
> | хостпулс/усерсессионс | Нет | Нет |
> | workspaces | ДА | ДА |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | еластикпулс | ДА | ДА |
> | Еластикпулс/Иосубтенантс | ДА | ДА |
> | IotHubs | ДА | ДА |
> | IotHubs/eventGridFilters | Нет | Нет |
> | ProvisioningServices | ДА | ДА |
> | usages | Нет | Нет |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Конвейеров | ДА | ДА |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | controllers | ДА | ДА |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | labcenters | ДА | ДА |
> | labs | ДА | ДА |
> | лаборатории и среды | ДА | ДА |
> | labs/serviceRunners | ДА | ДА |
> | labs/virtualMachines | ДА | ДА |
> | schedules | ДА | ДА |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Нет | Нет |
> | databaseAccounts | ДА | ДА |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | domains | ДА | ДА |
> | domains/domainOwnershipIdentifiers | Нет | Нет |
> | generateSsoRequest | Нет | Нет |
> | topLevelDomains | Нет | Нет |
> | validateDomainRegistrationInformation | Нет | Нет |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Нет | Нет |
> | lcsprojects/clouddeployments | Нет | Нет |
> | lcsprojects/connectors | Нет | Нет |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | ДА | ДА |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | domains | ДА | ДА |
> | domains/topics | Нет | Нет |
> | eventSubscriptions | Нет | Нет |
> | extensionTopics | Нет | Нет |
> | topics | ДА | ДА |
> | topicTypes | Нет | Нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | ДА | ДА |
> | namespaces | ДА | ДА |
> | namespaces/authorizationrules | Нет | Нет |
> | namespaces/disasterrecoveryconfigs | Нет | Нет |
> | namespaces/eventhubs | Нет | Нет |
> | namespaces/eventhubs/authorizationrules | Нет | Нет |
> | namespaces/eventhubs/consumergroups | Нет | Нет |
> | пространства имен/нетворкрулесетс | Нет | Нет |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | эластичной базы данных | Нет | Нет |
> | providers | Нет | Нет |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | enroll | Нет | Нет |
> | galleryitems | Нет | Нет |
> | generateartifactaccessuri | Нет | Нет |
> | myareas | Нет | Нет |
> | myareas/areas | Нет | Нет |
> | myareas/areas/areas | Нет | Нет |
> | myareas/areas/areas/galleryitems | Нет | Нет |
> | myareas/areas/galleryitems | Нет | Нет |
> | myareas/galleryitems | Нет | Нет |
> | регистрация | Нет | Нет |
> | ресурсов | Нет | Нет |
> | retrieveresourcesbyid | Нет | Нет |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | ДА | ДА |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | аутоманажедвмконфигуратионпрофилес | ДА | ДА |
> | конфигуратионпрофилеассигнментс | Нет | Нет |
> | guestConfigurationAssignments | Нет | Нет |
> | software | Нет | Нет |
> | софтвареупдатепрофиле | Нет | Нет |
> | софтвареупдатес | Нет | Нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | hanaInstances | ДА | ДА |
> | сапмониторс | ДА | ДА |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | дедикатедхсмс | ДА | ДА |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | ДА | ДА |
> | clusters/applications | Нет | Нет |

## <a name="microsofthealthcareapis"></a>Microsoft. Хеалскареапис

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | ДА | ДА |

## <a name="microsofthybridcompute"></a>Microsoft. Хибридкомпуте

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | виртуальных | ДА | ДА |
> | Компьютеры и расширения | ДА | ДА |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Диспетчеры | ДА | ДА |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | components | ДА | ДА |
> | нетворкскопес | ДА | ДА |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | jobs | ДА | ДА |

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
> | IoTApps | ДА | ДА |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Граф | ДА | ДА |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Нет | Нет |
> | хсмпулс | ДА | ДА |
> | vaults | ДА | ДА |
> | vaults/accessPolicies | Нет | Нет |
> | хранилища и Евентгридфилтерс | Нет | Нет |
> | vaults/secrets | Нет | Нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | ДА | ДА |
> | кластеры/аттачеддатабасеконфигуратионс | Нет | Нет |
> | clusters/databases | Нет | Нет |
> | clusters/databases/dataconnections | Нет | Нет |
> | clusters/databases/eventhubconnections | Нет | Нет |
> | кластеры/шаредидентитиес | Нет | Нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | labaccounts | ДА | ДА |
> | пользователей | Нет | Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | ДА | ДА |
> | integrationAccounts | ДА | ДА |
> | интегратионсервицеенвиронментс | ДА | ДА |
> | Интегратионсервицеенвиронментс/Манажедапис | ДА | ДА |
> | исолатеденвиронментс | ДА | ДА |
> | workflows | ДА | ДА |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | ДА | ДА |
> | webServices | ДА | ДА |
> | Рабочие области | ДА | ДА |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | workspaces | ДА | ДА |
> | workspaces/computes | Нет | Нет |
> | рабочие области и Евентгридфилтерс | Нет | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Identities | Нет | Нет |
> | userAssignedIdentities | ДА | ДА |

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
> | accounts | ДА | ДА |
> | accounts/eventGridFilters | Нет | Нет |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | offers | Нет | Нет |
> | offerTypes | Нет | Нет |
> | offerTypes/publishers | Нет | Нет |
> | offerTypes/publishers/offers | Нет | Нет |
> | offerTypes/publishers/offers/plans | Нет | Нет |
> | offerTypes/publishers/offers/plans/agreements | Нет | Нет |
> | offerTypes/publishers/offers/plans/configs | Нет | Нет |
> | offerTypes/publishers/offers/plans/configs/importImage | Нет | Нет |
> | privategalleryitems | Нет | Нет |
> | products | Нет | Нет |
> | издателя | Нет | Нет |
> | издатели и предложения | Нет | Нет |
> | издатели, предложения и поправки | Нет | Нет |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | classicDevServices | ДА | ДА |
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
> | mediaservices | ДА | ДА |
> | mediaservices/accountFilters | Нет | Нет |
> | mediaservices/assets | Нет | Нет |
> | mediaservices/assets/assetFilters | Нет | Нет |
> | mediaservices/contentKeyPolicies | Нет | Нет |
> | mediaservices/eventGridFilters | Нет | Нет |
> | mediaservices/liveEventOperations | Нет | Нет |
> | mediaservices/liveEvents | ДА | ДА |
> | mediaservices/liveEvents/liveOutputs | Нет | Нет |
> | mediaservices/liveOutputOperations | Нет | Нет |
> | mediaservices/Медиаграфс | Нет | Нет |
> | mediaservices/streamingEndpointOperations | Нет | Нет |
> | mediaservices/streamingEndpoints | ДА | ДА |
> | mediaservices/streamingLocators | Нет | Нет |
> | mediaservices/streamingPolicies | Нет | Нет |
> | mediaservices/transforms | Нет | Нет |
> | mediaservices/transforms/jobs | Нет | Нет |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | аппклустерс | ДА | ДА |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | ассессментпрожектс | ДА | ДА |
> | migrateprojects | ДА | ДА |
> | projects | ДА | ДА |

## <a name="microsoftmixedreality"></a>Microsoft. Микседреалити

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | холографиксброадкастаккаунтс | ДА | ДА |
> | обжектундерстандингаккаунтс | ДА | ДА |
> | ремотерендерингаккаунтс | ДА | ДА |
> | спатиаланчорсаккаунтс | ДА | ДА |
> | сурфацереконструктионаккаунтс | ДА | ДА |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | нетаппаккаунтс | ДА | ДА |
> | Нетаппаккаунтс/БаккупполиЦиес | ДА | ДА |
> | Нетаппаккаунтс/КапаЦитипулс | ДА | ДА |
> | Нетаппаккаунтс/КапаЦитипулс/тома | ДА | ДА |
> | Нетаппаккаунтс/КапаЦитипулс/тома/резервные копии | Нет | Нет |
> | Нетаппаккаунтс/КапаЦитипулс/Volumes/Маунттаржетс | ДА | ДА |
> | Нетаппаккаунтс/КапаЦитипулс/тома/моментальные снимки | ДА | ДА |
> | Нетаппаккаунтс и хранилища | Нет | Нет |
## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationGateways | ДА | ДА |
> | аппликатионгатевайвебаппликатионфиреваллполиЦиес | ДА | ДА |
> | applicationSecurityGroups | ДА | ДА |
> | azureFirewallFqdnTags | Нет | Нет |
> | azureFirewalls | ДА | Нет |
> | бастионхостс | ДА | ДА |
> | bgpServiceCommunities | Нет | Нет |
> | connections | ДА | ДА |
> | ddosCustomPolicies | ДА | ДА |
> | ddosProtectionPlans | ДА | ДА |
> | dnsOperationStatuses | Нет | Нет |
> | dnszones | ДА | ДА |
> | dnszones/A | Нет | Нет |
> | dnszones/AAAA | Нет | Нет |
> | dnszones/all | Нет | Нет |
> | dnszones/CAA | Нет | Нет |
> | dnszones/CNAME | Нет | Нет |
> | dnszones/MX | Нет | Нет |
> | dnszones/NS | Нет | Нет |
> | dnszones/PTR | Нет | Нет |
> | dnszones/recordsets | Нет | Нет |
> | dnszones/SOA | Нет | Нет |
> | dnszones/SRV | Нет | Нет |
> | dnszones/TXT | Нет | Нет |
> | expressRouteCircuits | ДА | ДА |
> | expressRouteCrossConnections | ДА | ДА |
> | експрессраутегатевайс | ДА | ДА |
> | експрессраутепортс | ДА | ДА |
> | expressRouteServiceProviders | Нет | Нет |
> | фиреваллполиЦиес | ДА | ДА |
> | frontdoors | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | ДА |
> | фронтдурвебаппликатионфиреваллманажедрулесетс | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | Нет |
> | frontdoorWebApplicationFirewallPolicies | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | ДА |
> | getDnsResourceReference | Нет | Нет |
> | internalNotify | Нет | Нет |
> | loadBalancers | ДА | Нет |
> | localNetworkGateways | ДА | ДА |
> | natGateways | ДА | ДА |
> | networkIntentPolicies | ДА | ДА |
> | networkInterfaces | ДА | ДА |
> | networkProfiles | ДА | ДА |
> | networkSecurityGroups | ДА | ДА |
> | networkWatchers | ДА | Нет |
> | networkWatchers/connectionMonitors | ДА | Нет |
> | networkWatchers/lenses | ДА | Нет |
> | networkWatchers/pingMeshes | ДА | Нет |
> | p2sVpnGateways | ДА | ДА |
> | приватеднсоператионстатусес | Нет | Нет |
> | приватеднсзонес | ДА | ДА |
> | Приватеднсзонес/A | Нет | Нет |
> | Приватеднсзонес/AAAA | Нет | Нет |
> | Приватеднсзонес/все | Нет | Нет |
> | Приватеднсзонес и CNAME | Нет | Нет |
> | Приватеднсзонес/MX | Нет | Нет |
> | Приватеднсзонес/PTR | Нет | Нет |
> | Приватеднсзонес/SOA | Нет | Нет |
> | Приватеднсзонес/SRV | Нет | Нет |
> | Приватеднсзонес/TXT | Нет | Нет |
> | Приватеднсзонес/Виртуалнетворклинкс | ДА | ДА |
> | приватиндпоинтс | ДА | ДА |
> | privateLinkServices | ДА | ДА |
> | publicIPAddresses | ДА | ДА |
> | publicIPPrefixes | ДА | ДА |
> | routeFilters | ДА | ДА |
> | routeTables | ДА | ДА |
> | serviceEndpointPolicies | ДА | ДА |
> | trafficManagerGeographicHierarchies | Нет | Нет |
> | trafficmanagerprofiles | ДА | ДА |
> | trafficmanagerprofiles/heatMaps | Нет | Нет |
> | траффикманажерусерметрикскэйс | Нет | Нет |
> | virtualHubs | ДА | ДА |
> | virtualNetworkGateways | ДА | ДА |
> | virtualNetworks | ДА | ДА |
> | virtualNetworkTaps | ДА | ДА |
> | virtualWans | ДА | ДА |
> | vpnGateways | ДА | Нет |
> | vpnSites | ДА | ДА |
> | webApplicationFirewallPolicies | ДА | ДА |

<a id="frontdoor" />

> [!NOTE]
> Для службы "Передняя дверца Azure" можно применять теги при создании ресурса, но обновление или добавление тегов в настоящее время не поддерживается.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | namespaces | ДА | Нет |
> | namespaces/notificationHubs | ДА | Нет |

## <a name="microsoftobjectstore"></a>Microsoft. Обжектсторе

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | оснамеспацес | ДА | ДА |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хипервситес | ДА | ДА |
> | импортситес | ДА | ДА |
> | серверситес | ДА | ДА |
> | вмвареситес | ДА | ДА |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | ДА | ДА |
> | устройства | Нет | Нет |
> | linkTargets | Нет | Нет |
> | storageInsightConfigs | Нет | Нет |
> | workspaces | ДА | ДА |
> | workspaces/dataSources | Нет | Нет |
> | workspaces/linkedServices | Нет | Нет |
> | workspaces/query | Нет | Нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managementassociations | Нет | Нет |
> | managementconfigurations | ДА | ДА |
> | solutions | ДА | ДА |
> | узел "Представления" | ДА | ДА |

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | легаципирингс | Нет | Нет |
> | пираснс | Нет | Нет |
> | пиринги | ДА | ДА |
> | пирингсервицепровидерс | Нет | Нет |
> | пирингсервицес | ДА | ДА |

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
> | dashboards | ДА | ДА |
> | userSettings | Нет | Нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | ДА | ДА |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capacities | ДА | ДА |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Нет | Нет |
> | vaults | ДА | ДА |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | namespaces | ДА | ДА |
> | namespaces/authorizationrules | Нет | Нет |
> | namespaces/hybridconnections | Нет | Нет |
> | namespaces/hybridconnections/authorizationrules | Нет | Нет |
> | namespaces/wcfrelays | Нет | Нет |
> | namespaces/wcfrelays/authorizationrules | Нет | Нет |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Нет | Нет |
> | Семейства | ДА | ДА |
> | коллекции и приложения | Нет | Нет |
> | Collections/секуритипринЦипалс | Нет | Нет |
> | темплатеимажес | Нет | Нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | запросы | ДА | ДА |
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
> | deployments | ДА | Нет |
> | deployments/operations | Нет | Нет |
> | deploymentScripts | ДА | ДА |
> | deploymentScripts и журналы | Нет | Нет |
> | links | Нет | Нет |
> | notifyResourceJobs | Нет | Нет |
> | providers | Нет | Нет |
> | resourceGroups | ДА | Нет |
> | ресурсов | Нет | Нет |
> | subscriptions | Нет | Нет |
> | subscriptions/providers | Нет | Нет |
> | subscriptions/resourceGroups | Нет | Нет |
> | subscriptions/resourcegroups/resources | Нет | Нет |
> | subscriptions/resources | Нет | Нет |
> | subscriptions/tagnames | Нет | Нет |
> | subscriptions/tagNames/tagValues | Нет | Нет |
> | tenants | Нет | Нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | приложений | ДА | ДА |
> | saasresources | Нет | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | jobcollections | ДА | ДА |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Нет | Нет |
> | searchServices | ДА | ДА |

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
> | инструментах автоматизации Composer | ДА | ДА |
> | AutoProvisioningSettings | Нет | Нет |
> | Compliances | Нет | Нет |
> | dataCollectionAgents | Нет | Нет |
> | девицесекуритиграупс | Нет | Нет |
> | discoveredSecuritySolutions | Нет | Нет |
> | externalSecuritySolutions | Нет | Нет |
> | InformationProtectionPolicies | Нет | Нет |
> | иотсекуритисолутионс | ДА | ДА |
> | Иотсекуритисолутионс/Аналитиксмоделс | Нет | Нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедалертс | Нет | Нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедрекоммендатионс | Нет | Нет |
> | jitNetworkAccessPolicies | Нет | Нет |
> | networkData | Нет | Нет |
> | плайбукконфигуратионс | ДА | ДА |
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
> | namespaces | ДА | Нет |
> | namespaces/authorizationrules | Нет | Нет |
> | namespaces/disasterrecoveryconfigs | Нет | Нет |
> | namespaces/eventgridfilters | Нет | Нет |
> | пространства имен/нетворкрулесетс | Нет | Нет |
> | namespaces/queues | Нет | Нет |
> | namespaces/queues/authorizationrules | Нет | Нет |
> | namespaces/topics | Нет | Нет |
> | namespaces/topics/authorizationrules | Нет | Нет |
> | namespaces/topics/subscriptions | Нет | Нет |
> | namespaces/topics/subscriptions/rules | Нет | Нет |
> | premiumMessagingRegions | Нет | Нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | приложений | ДА | ДА |
> | clusters | ДА | ДА |
> | clusters/applications | Нет | Нет |
> | containerGroups | ДА | ДА |
> | контаинерграупсетс | ДА | ДА |
> | edgeclusters | ДА | ДА |
> | еджеклустерс и приложения | Нет | Нет |
> | networks | ДА | ДА |
> | secretstores | ДА | ДА |
> | секретсторес и сертификаты | Нет | Нет |
> | секретсторес/секреты | Нет | Нет |
> | volumes. | ДА | ДА |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | приложений | ДА | ДА |
> | containerGroups | ДА | ДА |
> | gateways | ДА | ДА |
> | networks | ДА | ДА |
> | секретные коды | ДА | ДА |
> | volumes. | ДА | ДА |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Нет | Нет |
> | Провидеррегистратионс/Ресаурцетиперегистратионс | Нет | Нет |
> | rollouts | ДА | ДА |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SignalR | ДА | ДА |
> | SignalR/Евентгридфилтерс | Нет | Нет |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | ДА | ДА |

## <a name="microsoftsoftwareplan"></a>Microsoft. Софтвареплан

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хибридусебенефитс | Нет | Нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | ДА | ДА |
> | приложений | ДА | ДА |
> | jitRequests | ДА | ДА |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managedInstances | ДА | ДА |
> | managedInstances/databases | Да (см. [Примечание ниже](#sqlnote)) | ДА |
> | managedInstances/databases/backupShortTermRetentionPolicies | Нет | Нет |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Нет | Нет |
> | managedInstances/databases/vulnerabilityAssessments | Нет | Нет |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Нет | Нет |
> | managedInstances/encryptionProtector | Нет | Нет |
> | managedInstances/keys | Нет | Нет |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Нет | Нет |
> | managedInstances/vulnerabilityAssessments | Нет | Нет |
> | серверы | ДА | ДА |
> | servers/administrators | Нет | Нет |
> | servers/communicationLinks | Нет | Нет |
> | servers/databases | Да (см. [Примечание ниже](#sqlnote)) | ДА |
> | servers/encryptionProtector | Нет | Нет |
> | servers/firewallRules | Нет | Нет |
> | servers/keys | Нет | Нет |
> | servers/restorableDroppedDatabases | Нет | Нет |
> | servers/serviceobjectives | Нет | Нет |
> | servers/tdeCertificates | Нет | Нет |
> | виртуалклустерс | Нет | Нет |

<a id="sqlnote" />

> [!NOTE]
> База данных Master не поддерживает теги, но другие базы данных, в том числе базы данных хранилища данных Azure SQL, поддерживают их. Базы данных Хранилища данных SQL Azure должны быть в активном состоянии (работа не приостановлена).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | ДА | ДА |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Нет | Нет |
> | SqlVirtualMachines | ДА | ДА |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageAccounts | ДА | ДА |
> | storageAccounts/blobServices | Нет | Нет |
> | storageAccounts/fileServices | Нет | Нет |
> | storageAccounts/queueServices | Нет | Нет |
> | storageAccounts/services | Нет | Нет |
> | storageAccounts/services/metricDefinitions | Нет | Нет |
> | storageAccounts/tableServices | Нет | Нет |
> | usages | Нет | Нет |

## <a name="microsoftstoragecache"></a>Microsoft. Сторажекаче

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | кэширует | ДА | ДА |
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
> | storageSyncServices | ДА | ДА |
> | storageSyncServices/registeredServers | Нет | Нет |
> | storageSyncServices/syncGroups | Нет | Нет |
> | storageSyncServices/syncGroups/cloudEndpoints | Нет | Нет |
> | storageSyncServices/syncGroups/serverEndpoints | Нет | Нет |
> | storageSyncServices/workflows | Нет | Нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | ДА | ДА |
> | storageSyncServices/registeredServers | Нет | Нет |
> | storageSyncServices/syncGroups | Нет | Нет |
> | storageSyncServices/syncGroups/cloudEndpoints | Нет | Нет |
> | storageSyncServices/syncGroups/serverEndpoints | Нет | Нет |
> | storageSyncServices/workflows | Нет | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | ДА | ДА |
> | storageSyncServices/registeredServers | Нет | Нет |
> | storageSyncServices/syncGroups | Нет | Нет |
> | storageSyncServices/syncGroups/cloudEndpoints | Нет | Нет |
> | storageSyncServices/syncGroups/serverEndpoints | Нет | Нет |
> | storageSyncServices/workflows | Нет | Нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managers | ДА | ДА |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Да (см. примечание ниже) | ДА |

> [!NOTE]
> Невозможно добавить тег при выполнении streamingjobs. Остановите ресурс, чтобы добавить тег.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Отмена | Нет | Нет |
> | CreateSubscription | Нет | Нет |
> | enable | Нет | Нет |
> | Имени | Нет | Нет |
> | SubscriptionDefinitions | Нет | Нет |
> | SubscriptionOperations | Нет | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | environments | ДА | Нет |
> | environments/accessPolicies | Нет | Нет |
> | environments/eventsources | ДА | Нет |
> | environments/referenceDataSets | ДА | Нет |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | дедикатедклауднодес | ДА | ДА |
> | дедикатедклаудсервицес | ДА | ДА |
> | virtualMachines | ДА | ДА |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Нет | Нет |
> | apiManagementAccounts/apiAcls | Нет | Нет |
> | apiManagementAccounts/apis | Нет | Нет |
> | apiManagementAccounts/apis/apiAcls | Нет | Нет |
> | apiManagementAccounts/apis/connectionAcls | Нет | Нет |
> | apiManagementAccounts/apis/connections | Нет | Нет |
> | apiManagementAccounts/apis/connections/connectionAcls | Нет | Нет |
> | apiManagementAccounts/apis/localizedDefinitions | Нет | Нет |
> | apiManagementAccounts/connectionAcls | Нет | Нет |
> | apiManagementAccounts/connections | Нет | Нет |
> | billingMeters | Нет | Нет |
> | certificates | ДА | ДА |
> | connectionGateways | ДА | ДА |
> | connections | ДА | ДА |
> | customApis | ДА | ДА |
> | deletedSites | Нет | Нет |
> | функции | Нет | Нет |
> | hostingEnvironments | ДА | ДА |
> | hostingEnvironments/multiRolePools | Нет | Нет |
> | hostingEnvironments/workerPools | Нет | Нет |
> | publishingUsers | Нет | Нет |
> | к просмотру фильмов | Нет | Нет |
> | resourceHealthMetadata | Нет | Нет |
> | runtimes | Нет | Нет |
> | serverFarms | ДА | ДА |
> | serverFarms/Евентгридфилтерс | Нет | Нет |
> | sites | ДА | ДА |
> | сайты и конфигурация  | Нет | Нет |
> | Sites/Евентгридфилтерс | Нет | Нет |
> | sites/hostNameBindings | Нет | Нет |
> | Sites/файл networkconfig | Нет | Нет |
> | sites/premieraddons | ДА | ДА |
> | sites/slots | ДА | ДА |
> | сайты/слоты/Евентгридфилтерс | Нет | Нет |
> | sites/slots/hostNameBindings | Нет | Нет |
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
> | DeviceServices | ДА | ДА |

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

## <a name="next-steps"></a>Дальнейшие действия

Сведения о том, как применить теги к ресурсам, см. в статье, посвященной [использованию тегов для организации ресурсов Azure](resource-group-using-tags.md).
