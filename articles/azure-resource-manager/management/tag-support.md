---
title: Поддержка тегов для ресурсов
description: В этой статье описываются типы ресурсов Azure, поддерживающие теги, а также приводятся сведения о всех службах Azure.
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 493313cbb0b43b04d3c4dde82b5ca95dd7e9ac4e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479778"
---
# <a name="tag-support-for-azure-resources"></a>Поддержка тегов для ресурсов Azure
В этой статье приводятся сведения о поддержке типом ресурса функции [тегов](tag-resources.md). Столбец с подписью **поддерживает теги** . указывает, имеет ли тип ресурса свойство для тега. Столбец с меткой **тег в отчете о затратах** указывает, передает ли этот тип ресурса тег в отчет о затратах. Вы можете просматривать затраты по тегам в [анализе затрат на управление затратами](../../cost-management/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) , а также в [счетах на счета и данные о ежедневном использовании в Azure](../../billing/billing-download-azure-invoice-daily-usage-date.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Переход к пространству имен поставщика ресурсов:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. Аппплатформ](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. Обжектсторе](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights).
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | DomainServices | Да | Да |
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
> | настройка | Нет | Нет |
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
> | метаданные | Нет | Нет |
> | к просмотру фильмов | Нет | Нет |
> | suppressions | Нет | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | actionRules | Да | Да |
> | оповещения | Нет | Нет |
> | alertsList | Нет | Нет |
> | алертсметадата | Нет | Нет |
> | alertsSummary | Нет | Нет |
> | alertsSummaryList | Нет | Нет |
> | обратная связь | Нет | Нет |
> | smartDetectorAlertRules | Да | Да |
> | smartDetectorRuntimeEnvironments | Нет | Нет |
> | smartGroups | Нет | Нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | серверы | Да | Да |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Нет | Нет |
> | служба | Да | Да |
> | validateServiceName | Нет | Нет |

## <a name="microsoftappconfiguration"></a>Microsoft. Аппконфигуратион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конфигуратионсторес | Да | Да |
> | Конфигуратионсторес/Евентгридфилтерс | Нет | Нет |

## <a name="microsoftappplatform"></a>Microsoft. Аппплатформ

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Spring | Да | Да |

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
> | разрешения | Нет | Нет |
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
> | automationAccounts | Да | Да |
> | automationAccounts и конфигурации | Да | Да |
> | automationAccounts и задания | Нет | Нет |
> | automationAccounts и модули Runbook | Да | Да |
> | automationAccounts/Софтвареупдатеконфигуратионс | Нет | Нет |
> | automationAccounts/веб-перехватчики | Нет | Нет |

## <a name="microsoftazconfig"></a>Microsoft. Азконфиг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | конфигуратионсторес | Да | Да |
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
> | b2cDirectories | Да | Нет |
> | b2ctenants | Нет | Нет |

## <a name="microsoftazuredata"></a>Microsoft. Азуредата

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хибриддатаманажерс | Да | Да |
> | постгресинстанцес | Да | Да |
> | склбигдатаклустерс | Да | Да |
> | склинстанцес | Да | Да |
> | склсерверрегистратионс | Да | Да |
> | Склсерверрегистратионс и sqlServer | Нет | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | registrations | Да | Да |
> | регистрации и Кустомерсубскриптионс | Нет | Нет |
> | регистрации и продукты | Нет | Нет |
> | верификатионкэйс | Нет | Нет |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Да | Да |

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
> | mapApis | Да | Да |
> | updateCommunicationPreference | Нет | Нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | блоккчаинмемберс | Да | Да |
> | кордамемберс | Да | Да |
> | наблюдатели | Да | Да |

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
> | botServices | Да | Да |
> | Ботсервицес и каналы | Нет | Нет |
> | Ботсервицес и подключения | Нет | Нет |
> | языки | Нет | Нет |
> | шаблоны | Нет | Нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Redis | Да | Да |
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
> | кднвебаппликатионфиреваллполиЦиес | Да | Да |
> | edgenodes | Нет | Нет |
> | профили | Да | Да |
> | профили и конечные точки | Да | Да |
> | профили, конечные точки/кустомдомаинс | Нет | Нет |
> | профили, конечные точки и источники | Нет | Нет |
> | validateProbe | Нет | Нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Да | Да |
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
> | accounts | Да | Да |

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
> | availabilitySets | Да | Да |
> | дискенкриптионсетс | Да | Да |
> | disks | Да | Да |
> | galleries | Да | Да |
> | коллекции и приложения | Нет | Нет |
> | коллекции, приложения и версии | Нет | Нет |
> | коллекции и изображения | Нет | Нет |
> | коллекции, изображения и версии | Нет | Нет |
> | хостграупс | Да | Да |
> | Хостграупс и узлы | Да | Да |
> | images | Да | Да |
> | проксимитиплацементграупс | Да | Да |
> | restorePointCollections | Да | Да |
> | Ресторепоинтколлектионс/Ресторепоинтс | Нет | Нет |
> | sharedVMImages | Да | Да |
> | Шаредвмимажес и версии | Нет | Нет |
> | snapshots | Да | Да |
> | virtualMachines | Да | Да |
> | virtualMachines и расширения | Да | Да |
> | virtualMachines/metricDefinitions | Нет | Нет |
> | virtualMachineScaleSets | Да | Да |
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
> | Расходы | Нет | Нет |
> | Расходы | Нет | Нет |
> | CostTags | Нет | Нет |
> | credits | Нет | Нет |
> | события | Нет | Нет |
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
> | containerGroups | Да | Да |
> | serviceAssociationLinks | Нет | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | registries | Да | Да |
> | реестры и сборки | Нет | Нет |
> | реестры/сборки/Отмена | Нет | Нет |
> | реестры/сборки/Жетлоглинк | Нет | Нет |
> | реестры и Буилдтаскс | Да | Да |
> | реестры, Буилдтаскс и шаги | Нет | Нет |
> | реестры и Евентгридфилтерс | Нет | Нет |
> | реестры и Женератекредентиалс | Нет | Нет |
> | реестры и Жетбуилдсаурцеуплоадурл | Нет | Нет |
> | реестры и учетные данные | Нет | Нет |
> | реестры и Импортимаже | Нет | Нет |
> | реестры и Куеуебуилд | Нет | Нет |
> | реестры и Реженератекредентиал | Нет | Нет |
> | реестры и Реженератекредентиалс | Нет | Нет |
> | реестры и репликации | Да | Да |
> | реестры и запуски | Нет | Нет |
> | реестры/запуски/Отмена | Нет | Нет |
> | реестры и Счедулерун | Нет | Нет |
> | реестры и Скопемапс | Нет | Нет |
> | реестры и Таскрунс | Да | Да |
> | реестры и задачи | Да | Да |
> | реестры и маркеры | Нет | Нет |
> | реестры и УпдатеполиЦиес | Нет | Нет |
> | реестры и веб-перехватчики | Да | Да |
> | реестры/веб-перехватчики/Жеткаллбаккконфиг | Нет | Нет |
> | реестры, веб-перехватчики и проверка связи | Нет | Нет |

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
> | Оповещения | Нет | Нет |
> | BillingAccounts | Нет | Нет |
> | Расходы | Нет | Нет |
> | клаудконнекторс | Нет | Нет |
> | Коннекторы | Да | Да |
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
> | Прогноз | Нет | Нет |
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
> | взаимосвязи | Нет | Нет |
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
> | workspaces | Да | Нет |
> | рабочие области и Виртуалнетворкпирингс | Нет | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | catalogs | Да | Да |
> | каталоги | Да | Да |
> | каталоги данных и источники данных | Нет | Нет |
> | каталоги данных, источники данных и проверки | Нет | Нет |
> | каталоги данных/DataSources/scans/DataSets | Нет | Нет |
> | каталоги данных, источники данных, проверки и триггеры | Нет | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | dataFactories | Да | Нет |
> | Коэффициенты и diagnosticSettings | Нет | Нет |
> | Коэффициенты и metricDefinitions | Нет | Нет |
> | dataFactorySchema | Нет | Нет |
> | factories | Да | Нет |
> | фабрики/Интегратионрунтимес | Нет | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | Accounts/Даталакестореаккаунтс | Нет | Нет |
> | Accounts/storageAccounts | Нет | Нет |
> | Accounts/storageAccounts/Containers | Нет | Нет |
> | Accounts/Трансфераналитиксунитс | Нет | Нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
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
> | accounts | Да | Да |
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
> | серверы | Да | Да |
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
> | серверы | Да | Да |
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
> | серверграупс | Да | Да |
> | серверы | Да | Да |
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
> | аппликатионграупс и приложения | Нет | Нет |
> | аппликатионграупс и настольные системы | Нет | Нет |
> | аппликатионграупс/стартменуитемс | Нет | Нет |
> | хостпулс | Да | Да |
> | хостпулс/сессионхостс | Нет | Нет |
> | хостпулс/сессионхостс/усерсессионс | Нет | Нет |
> | хостпулс/усерсессионс | Нет | Нет |
> | workspaces | Да | Да |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | еластикпулс | Да | Да |
> | Еластикпулс/Иосубтенантс | Да | Да |
> | IotHubs | Да | Да |
> | IotHubs/Евентгридфилтерс | Нет | Нет |
> | ProvisioningServices | Да | Да |
> | usages | Нет | Нет |

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
> | databaseAccountNames | Нет | Нет |
> | databaseAccounts | Да | Да |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | domains | Да | Да |
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
> | services; | Да | Да |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | domains | Да | Да |
> | домены и темы | Нет | Нет |
> | eventSubscriptions | Нет | Нет |
> | extensionTopics | Нет | Нет |
> | topics | Да | Да |
> | topicTypes | Нет | Нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | пространства имен | Да | Да |
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
> | accounts | Да | Да |

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
> | кластеры и приложения | Нет | Нет |

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
> | diagnosticSettings | Нет | Нет |
> | diagnosticSettingsCategories | Нет | Нет |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | апптемплатес | Нет | Нет |
> | IoTApps | Да | Да |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Граф | Да | Да |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Нет | Нет |
> | хсмпулс | Да | Да |
> | vaults | Да | Да |
> | хранилища и accessPolicies | Нет | Нет |
> | хранилища и Евентгридфилтерс | Нет | Нет |
> | хранилища и секреты | Нет | Нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | кластеры/аттачеддатабасеконфигуратионс | Нет | Нет |
> | кластеры и базы данных | Нет | Нет |
> | кластеры/базы данных/подключения | Нет | Нет |
> | кластеры/базы данных/евенсубконнектионс | Нет | Нет |
> | кластеры/шаредидентитиес | Нет | Нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | labaccounts | Да | Да |
> | пользователей | Нет | Нет |

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
> | рабочие области и расчеты | Нет | Нет |
> | рабочие области и Евентгридфилтерс | Нет | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Identities | Нет | Нет |
> | userAssignedIdentities | Да | Да |

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
> | accounts | Да | Да |
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
> | publishers | Нет | Нет |
> | издатели и предложения | Нет | Нет |
> | издатели, предложения и поправки | Нет | Нет |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Да | Да |
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
> | mediaservices | Да | Да |
> | mediaservices/Аккаунтфилтерс | Нет | Нет |
> | mediaservices и активы | Нет | Нет |
> | mediaservices/активы/Ассетфилтерс | Нет | Нет |
> | mediaservices/КонтенткэйполиЦиес | Нет | Нет |
> | mediaservices/Евентгридфилтерс | Нет | Нет |
> | mediaservices/Лививентоператионс | Нет | Нет |
> | mediaservices/Лививентс | Да | Да |
> | mediaservices/Лививентс/Ливеаутпутс | Нет | Нет |
> | mediaservices/Ливеаутпутоператионс | Нет | Нет |
> | mediaservices/Медиаграфс | Нет | Нет |
> | mediaservices/Стреаминжендпоинтоператионс | Нет | Нет |
> | mediaservices/Streamingendpoint | Да | Да |
> | mediaservices/Стреаминглокаторс | Нет | Нет |
> | mediaservices/СтреамингполиЦиес | Нет | Нет |
> | mediaservices/преобразования | Нет | Нет |
> | mediaservices/преобразования/задания | Нет | Нет |

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
> | нетаппаккаунтс | Да | Нет |
> | Нетаппаккаунтс/КапаЦитипулс | Да | Нет |
> | Нетаппаккаунтс/КапаЦитипулс/тома | Да | Нет |
> | Нетаппаккаунтс/КапаЦитипулс/Volumes/Маунттаржетс | Да | Нет |
> | Нетаппаккаунтс/КапаЦитипулс/тома/моментальные снимки | Да | Нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Да | Да |
> | аппликатионгатевайвебаппликатионфиреваллполиЦиес | Да | Да |
> | applicationSecurityGroups | Да | Да |
> | azureFirewallFqdnTags | Нет | Нет |
> | azureFirewalls | Да | Нет |
> | бастионхостс | Да | Да |
> | bgpServiceCommunities | Нет | Нет |
> | connections | Да | Да |
> | ddosCustomPolicies | Да | Да |
> | ddosProtectionPlans | Да | Да |
> | dnsOperationStatuses | Нет | Нет |
> | dnszones | Да | Да |
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
> | expressRouteCircuits | Да | Да |
> | expressRouteCrossConnections | Да | Да |
> | експрессраутегатевайс | Да | Да |
> | експрессраутепортс | Да | Да |
> | expressRouteServiceProviders | Нет | Нет |
> | фиреваллполиЦиес | Да | Да |
> | frontdoors | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | Да |
> | фронтдурвебаппликатионфиреваллманажедрулесетс | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | Нет |
> | frontdoorWebApplicationFirewallPolicies | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | Да |
> | getDnsResourceReference | Нет | Нет |
> | internalNotify | Нет | Нет |
> | loadBalancers | Да | Нет |
> | localNetworkGateways | Да | Да |
> | natGateways | Да | Да |
> | networkIntentPolicies | Да | Да |
> | networkInterfaces | Да | Да |
> | networkProfiles | Да | Да |
> | networkSecurityGroups | Да | Да |
> | networkWatchers | Да | Нет |
> | Нетворкватчерс/Коннектионмониторс | Да | Нет |
> | Нетворкватчерс/lenses | Да | Нет |
> | Нетворкватчерс/Пингмешес | Да | Нет |
> | p2sVpnGateways | Да | Да |
> | приватеднсоператионстатусес | Нет | Нет |
> | приватеднсзонес | Да | Да |
> | Приватеднсзонес/A | Нет | Нет |
> | Приватеднсзонес/AAAA | Нет | Нет |
> | Приватеднсзонес/все | Нет | Нет |
> | Приватеднсзонес и CNAME | Нет | Нет |
> | Приватеднсзонес/MX | Нет | Нет |
> | Приватеднсзонес/PTR | Нет | Нет |
> | Приватеднсзонес/SOA | Нет | Нет |
> | Приватеднсзонес/SRV | Нет | Нет |
> | Приватеднсзонес/TXT | Нет | Нет |
> | Приватеднсзонес/Виртуалнетворклинкс | Да | Да |
> | приватиндпоинтс | Да | Да |
> | privateLinkServices | Да | Да |
> | publicIPAddresses | Да | Да |
> | publicIPPrefixes | Да | Да |
> | routeFilters | Да | Да |
> | routeTables | Да | Да |
> | serviceEndpointPolicies | Да | Да |
> | trafficManagerGeographicHierarchies | Нет | Нет |
> | trafficmanagerprofiles | Да | Да |
> | trafficmanagerprofiles/heatMaps | Нет | Нет |
> | траффикманажерусерметрикскэйс | Нет | Нет |
> | virtualHubs | Да | Да |
> | virtualNetworkGateways | Да | Да |
> | virtualNetworks | Да | Да |
> | virtualNetworkTaps | Да | Да |
> | virtualWans | Да | Да |
> | vpnGateways | Да | Нет |
> | vpnSites | Да | Да |
> | webApplicationFirewallPolicies | Да | Да |

<a id="frontdoor" />

> [!NOTE]
> Для службы "Передняя дверца Azure" можно применять теги при создании ресурса, но обновление или добавление тегов в настоящее время не поддерживается.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да | Нет |
> | пространства имен/notificationHubs | Да | Нет |

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
> | устройства | Нет | Нет |
> | linkTargets | Нет | Нет |
> | storageInsightConfigs | Нет | Нет |
> | workspaces | Да | Да |
> | рабочие области и источники данных | Нет | Нет |
> | рабочие области и linkedServices | Нет | Нет |
> | рабочие области и запросы | Нет | Нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managementassociations | Нет | Нет |
> | managementconfigurations | Да | Да |
> | solutions | Да | Да |
> | узел "Представления" | Да | Да |

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | легаципирингс | Нет | Нет |
> | пираснс | Нет | Нет |
> | пиринги | Да | Да |
> | пирингсервицепровидерс | Нет | Нет |
> | пирингсервицес | Да | Да |

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
> | dashboards | Да | Да |
> | userSettings | Нет | Нет |

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

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Нет | Нет |
> | vaults | Да | Да |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да | Да |
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
> | коллекции | Да | Да |
> | коллекции и приложения | Нет | Нет |
> | Collections/секуритипринЦипалс | Нет | Нет |
> | темплатеимажес | Нет | Нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | запросы | Да | Да |
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
> | события | Нет | Нет |
> | impactedResources | Нет | Нет |
> | метаданные | Нет | Нет |
> | Уведомления | Нет | Нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | deployments | Да | Нет |
> | развертывания и операции | Нет | Нет |
> | deploymentScripts | Да | Да |
> | deploymentScripts и журналы | Нет | Нет |
> | ссылки | Нет | Нет |
> | notifyResourceJobs | Нет | Нет |
> | providers | Нет | Нет |
> | resourceGroups | Да | Нет |
> | subscriptions | Нет | Нет |
> | tenants | Нет | Нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | приложений | Да | Да |
> | saasresources | Нет | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | jobcollections | Да | Да |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Нет | Нет |
> | searchServices | Да | Да |

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
> | инструментах автоматизации Composer | Да | Да |
> | AutoProvisioningSettings | Нет | Нет |
> | Compliances | Нет | Нет |
> | dataCollectionAgents | Нет | Нет |
> | девицесекуритиграупс | Нет | Нет |
> | discoveredSecuritySolutions | Нет | Нет |
> | externalSecuritySolutions | Нет | Нет |
> | InformationProtectionPolicies | Нет | Нет |
> | иотсекуритисолутионс | Да | Да |
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
> | задачи | Нет | Нет |
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
> | aggregations | Нет | Нет |
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
> | пространства имен | Да | Нет |
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
> | приложений | Да | Да |
> | clusters | Да | Да |
> | кластеры и приложения | Нет | Нет |
> | containerGroups | Да | Да |
> | контаинерграупсетс | Да | Да |
> | edgeclusters | Да | Да |
> | еджеклустерс и приложения | Нет | Нет |
> | networks | Да | Да |
> | secretstores | Да | Да |
> | секретсторес и сертификаты | Нет | Нет |
> | секретсторес/секреты | Нет | Нет |
> | volumes. | Да | Да |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | приложений | Да | Да |
> | containerGroups | Да | Да |
> | gateways | Да | Да |
> | networks | Да | Да |
> | секретные коды | Да | Да |
> | volumes. | Да | Да |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Нет | Нет |
> | Провидеррегистратионс/Ресаурцетиперегистратионс | Нет | Нет |
> | rollouts | Да | Да |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SignalR | Да | Да |
> | SignalR/Евентгридфилтерс | Нет | Нет |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Да | Да |

## <a name="microsoftsoftwareplan"></a>Microsoft. Софтвареплан

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хибридусебенефитс | Нет | Нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Да | Да |
> | приложений | Да | Да |
> | jitRequests | Да | Да |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managedInstances | Да | Да |
> | Манажединстанцес и базы данных | Нет | Нет |
> | Манажединстанцес/databases/БаккупшорттермретентионполиЦиес | Нет | Нет |
> | Манажединстанцес/базы данных, схемы, таблицы, столбцы и Сенситивитилабелс | Нет | Нет |
> | Манажединстанцес/databases/Вулнерабилитяссессментс | Нет | Нет |
> | Манажединстанцес/базы данных/Вулнерабилитяссессментс/правила и базовые планы | Нет | Нет |
> | Манажединстанцес/Енкриптионпротектор | Нет | Нет |
> | Манажединстанцес и ключи | Нет | Нет |
> | Манажединстанцес/Ресторабледроппеддатабасес/БаккупшорттермретентионполиЦиес | Нет | Нет |
> | Манажединстанцес/Вулнерабилитяссессментс | Нет | Нет |
> | серверы | Да | Да |
> | серверы и администраторы | Нет | Нет |
> | серверы и Коммуникатионлинкс | Нет | Нет |
> | серверы и базы данных | Да (см. [Примечание ниже](#sqlnote)) | Да |
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
> | SqlVirtualMachineGroups | Да | Да |
> | Склвиртуалмачинеграупс/Аваилабилитиграуплистенерс | Нет | Нет |
> | SqlVirtualMachines | Да | Да |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Да | Да |
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
> | Кэширует | Да | Да |
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
> | storageSyncServices | Да | Да |
> | Сторажесинксервицес/Регистередсерверс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | Нет | Нет |
> | Сторажесинксервицес и рабочие процессы | Нет | Нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да | Да |
> | Сторажесинксервицес/Регистередсерверс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | Нет | Нет |
> | Сторажесинксервицес и рабочие процессы | Нет | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да | Да |
> | Сторажесинксервицес/Регистередсерверс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | Нет | Нет |
> | Сторажесинксервицес и рабочие процессы | Нет | Нет |

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
> | cancel | Нет | Нет |
> | CreateSubscription | Нет | Нет |
> | enable | Нет | Нет |
> | переименовать | Нет | Нет |
> | SubscriptionDefinitions | Нет | Нет |
> | SubscriptionOperations | Нет | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | environments | Да | Нет |
> | среды и accessPolicies | Нет | Нет |
> | среды и классов EventSource | Да | Нет |
> | среды и Референцедатасетс | Да | Нет |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Вмвареклаудсимпле

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | дедикатедклауднодес | Да | Да |
> | дедикатедклаудсервицес | Да | Да |
> | virtualMachines | Да | Да |

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
> | certificates | Да | Да |
> | connectionGateways | Да | Да |
> | connections | Да | Да |
> | customApis | Да | Да |
> | deletedSites | Нет | Нет |
> | функции | Нет | Нет |
> | hostingEnvironments | Да | Да |
> | hostingEnvironments/Мултиролепулс | Нет | Нет |
> | hostingEnvironments/внешнего размещения | Нет | Нет |
> | publishingUsers | Нет | Нет |
> | к просмотру фильмов | Нет | Нет |
> | resourceHealthMetadata | Нет | Нет |
> | runtimes | Нет | Нет |
> | serverFarms | Да | Да |
> | serverFarms/Евентгридфилтерс | Нет | Нет |
> | sites | Да | Да |
> | сайты и конфигурация  | Нет | Нет |
> | Sites/Евентгридфилтерс | Нет | Нет |
> | Sites/Хостнамебиндингс | Нет | Нет |
> | Sites/файл networkconfig | Нет | Нет |
> | Sites/премиераддонс | Да | Да |
> | сайты и слоты | Да | Да |
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
> | DeviceServices | Да | Да |

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

Сведения о том, как применить теги к ресурсам, см. в статье, посвященной [использованию тегов для организации ресурсов Azure](tag-resources.md).
