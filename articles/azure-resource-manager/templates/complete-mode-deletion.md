---
title: Удаление ресурсов в полном режиме
description: Здесь показано, как происходит удаление ресурсов в полном режиме в шаблонах Azure Resource Manager по типу ресурса.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 37d055503b8c9524392bedaa265025554dc4f8ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479622"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Удаление ресурсов Azure для развертываний в полном режиме

В этой статье описывается, как процесс удаления будет обрабатываться различными типами, когда он не происходит в шаблоне, развернутом в завершенном режиме.

Типы ресурсов, помеченные **Да** , удаляются, если тип не находится в шаблоне, развернутом в полном режиме.

Типы ресурсов, помеченные как " **нет** ", не удаляются автоматически при отсутствии в шаблоне. Однако они удаляются, если родительский ресурс удаляется. Чтобы получить полное описание поведения см. статью [Режимы развертывания в Azure Resource Manager](deployment-modes.md).

Если развернуть в [шаблоне несколько групп ресурсов](cross-resource-group-deployment.md), то ресурсы в группе ресурсов, указанной в операции развертывания, могут быть удалены. Ресурсы во вторичных группах ресурсов не удаляются.

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
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | DomainServices | Да |
> | DomainServices/ауконтаинер | Нет |

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
> | метаданные | Нет |
> | к просмотру фильмов | Нет |
> | suppressions | Нет |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | actionRules | Да |
> | оповещения | Нет |
> | alertsList | Нет |
> | алертсметадата | Нет |
> | alertsSummary | Нет |
> | alertsSummaryList | Нет |
> | обратная связь | Нет |
> | smartDetectorAlertRules | Да |
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

## <a name="microsoftappplatform"></a>Microsoft. Аппплатформ

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Spring | Да |

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
> | automationAccounts и конфигурации | Да |
> | automationAccounts и задания | Нет |
> | automationAccounts и модули Runbook | Да |
> | automationAccounts/Софтвареупдатеконфигуратионс | Нет |
> | automationAccounts/веб-перехватчики | Нет |

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
> | среды и учетные записи | Нет |
> | среды, учетные записи и пространства имен | Нет |
> | среды, учетные записи, пространства имен и конфигурации | Нет |

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
> | хибриддатаманажерс | Да |
> | постгресинстанцес | Да |
> | склбигдатаклустерс | Да |
> | склинстанцес | Да |
> | склсерверрегистратионс | Да |
> | Склсерверрегистратионс и sqlServer | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | registrations | Да |
> | регистрации и Кустомерсубскриптионс | Нет |
> | регистрации и продукты | Нет |
> | верификатионкэйс | Нет |

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
> | Биллингаккаунтс/Биллингпермиссионс | Нет |
> | Биллингаккаунтс/Биллингпрофилес | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингпермиссионс | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингролеассигнментс | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингроледефинитионс | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингсубскриптионс | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Креатебиллингролеассигнмент | Нет |
> | Биллингаккаунтс/Биллингпрофилес/клиенты | Нет |
> | Биллингаккаунтс/Биллингпрофилес/счета | Нет |
> | Биллингаккаунтс/Биллингпрофилес/счета/прайс-лист | Нет |
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
> | Биллингаккаунтс/Биллингпрофилес/Пайментмесодс | Нет |
> | Биллингаккаунтс/Биллингпрофилес/политики | Нет |
> | Биллингаккаунтс/Биллингпрофилес/прайс-лист | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Прицешитдовнлоадоператионс | Нет |
> | Биллингаккаунтс/Биллингпрофилес/Products | Нет |
> | Биллингаккаунтс/Биллингпрофилес/транзакции | Нет |
> | Биллингаккаунтс/Биллингролеассигнментс | Нет |
> | Биллингаккаунтс/Биллингроледефинитионс | Нет |
> | Биллингаккаунтс/Биллингсубскриптионс | Нет |
> | Биллингаккаунтс/Биллингсубскриптионс/счета | Нет |
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
> | Биллингаккаунтс и отделы | Нет |
> | Биллингаккаунтс/Енроллментаккаунтс | Нет |
> | Биллингаккаунтс/счета | Нет |
> | Биллингаккаунтс/Инвоицесектионс | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионмовеоператионс | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионс | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионс/перемещение | Нет |
> | Биллингаккаунтс/Инвоицесектионс/повышение прав | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Инитиатетрансфер | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Патчоператионс | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Продуктмовеоператионс | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Products | Нет |
> | Биллингаккаунтс/Инвоицесектионс/продукты/перемещение | Нет |
> | Биллингаккаунтс/Инвоицесектионс/Products/Упдатеауторенев | Нет |
> | Биллингаккаунтс/Инвоицесектионс/транзакции | Нет |
> | Биллингаккаунтс/Инвоицесектионс/передача | Нет |
> | Биллингаккаунтс/Линеофкредит | Нет |
> | Биллингаккаунтс/Патчоператионс | Нет |
> | Биллингаккаунтс/Пайментмесодс | Нет |
> | Биллингаккаунтс и продукты | Нет |
> | Биллингаккаунтс/транзакции | Нет |
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
> | передачи/Акцепттрансфер | Нет |
> | передачи/Деклинетрансфер | Нет |
> | передачи/значение operationstatus | Нет |
> | передачи/Валидатетрансфер | Нет |
> | валидатеаддресс | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | mapApis | Да |
> | updateCommunicationPreference | Нет |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | блоккчаинмемберс | Да |
> | кордамемберс | Да |
> | наблюдатели | Да |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | blueprintAssignments | Нет |
> | Блуепринтассигнментс/Ассигнментоператионс | Нет |
> | Блуепринтассигнментс/операции | Нет |
> | blueprints | Нет |
> | схемы и артефакты | Нет |
> | проекты и версии | Нет |
> | схемы, версии и артефакты | Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | botServices | Да |
> | Ботсервицес и каналы | Нет |
> | Ботсервицес и подключения | Нет |
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
> | exchange | Нет |
> | плацепурчасеордер | Нет |
> | reservationOrders | Нет |
> | Ресерватионордерс/Калкулатерефунд | Нет |
> | Ресерватионордерс/Merge | Нет |
> | Ресерватионордерс/резервирования | Нет |
> | Ресерватионордерс/резервирования/редакции | Нет |
> | Ресерватионордерс/Return | Нет |
> | Ресерватионордерс/Split | Нет |
> | Ресерватионордерс/swap | Нет |
> | reservations | Нет |
> | ресурсов | Нет |
> | validateReservationOrder | Нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | кднвебаппликатионфиреваллманажедрулесетс | Нет |
> | кднвебаппликатионфиреваллполиЦиес | Да |
> | edgenodes | Нет |
> | профили | Да |
> | профили и конечные точки | Да |
> | профили, конечные точки/кустомдомаинс | Нет |
> | профили, конечные точки и источники | Нет |
> | validateProbe | Нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | certificateOrders | Да |
> | Certificateorder и сертификаты | Нет |
> | validateCertificateRegistrationInformation | Нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capabilities | Нет |
> | domainNames | Да |
> | имя_домена/возможности | Нет |
> | имя_домена/Интерналлоадбаланцерс | Нет |
> | имя_домена/Сервицецертификатес | Нет |
> | имя_домена/слоты | Нет |
> | Доменные имя, слоты и роли | Нет |
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
> | virtualMachines/метрики | Нет |

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
> | Експрессраутекроссконнектионс и пиринг | Нет |
> | gatewaySupportedDevices | Нет |
> | networkSecurityGroups | Да |
> | quotas | Нет |
> | reservedIps | Да |
> | virtualNetworks | Да |
> | virtualNetworks/Ремотевиртуалнетворкпирингпроксиес | Нет |
> | virtualNetworks/Виртуалнетворкпирингс | Нет |

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
> | storageAccounts | Да |
> | storageAccounts/Блобсервицес | Нет |
> | storageAccounts/Филесервицес | Нет |
> | storageAccounts/metricDefinitions | Нет |
> | storageAccounts/метрики | Нет |
> | storageAccounts/Куеуесервицес | Нет |
> | storageAccounts и службы | Нет |
> | storageAccounts/Services/diagnosticSettings | Нет |
> | storageAccounts/Services/metricDefinitions | Нет |
> | storageAccounts/Services/метрики | Нет |
> | storageAccounts/Таблесервицес | Нет |
> | storageAccounts/Вмимажес | Нет |
> | vmImages | Нет |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |

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
> | availabilitySets | Да |
> | дискенкриптионсетс | Да |
> | disks | Да |
> | galleries | Да |
> | коллекции и приложения | Нет |
> | коллекции, приложения и версии | Нет |
> | коллекции и изображения | Нет |
> | коллекции, изображения и версии | Нет |
> | хостграупс | Да |
> | Хостграупс и узлы | Да |
> | images | Да |
> | проксимитиплацементграупс | Да |
> | restorePointCollections | Да |
> | Ресторепоинтколлектионс/Ресторепоинтс | Нет |
> | sharedVMImages | Да |
> | Шаредвмимажес и версии | Нет |
> | snapshots | Да |
> | virtualMachines | Да |
> | virtualMachines и расширения | Да |
> | virtualMachines/metricDefinitions | Нет |
> | virtualMachineScaleSets | Да |
> | virtualMachineScaleSets и расширения | Нет |
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
> | Теги | Нет |
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
> | реестры и сборки | Нет |
> | реестры/сборки/Отмена | Нет |
> | реестры/сборки/Жетлоглинк | Нет |
> | реестры и Буилдтаскс | Да |
> | реестры, Буилдтаскс и шаги | Нет |
> | реестры и Евентгридфилтерс | Нет |
> | реестры и Женератекредентиалс | Нет |
> | реестры и Жетбуилдсаурцеуплоадурл | Нет |
> | реестры и учетные данные | Нет |
> | реестры и Импортимаже | Нет |
> | реестры и Куеуебуилд | Нет |
> | реестры и Реженератекредентиал | Нет |
> | реестры и Реженератекредентиалс | Нет |
> | реестры и репликации | Да |
> | реестры и запуски | Нет |
> | реестры/запуски/Отмена | Нет |
> | реестры и Счедулерун | Нет |
> | реестры и Скопемапс | Нет |
> | реестры и Таскрунс | Да |
> | реестры и задачи | Да |
> | реестры и маркеры | Нет |
> | реестры и УпдатеполиЦиес | Нет |
> | реестры и веб-перехватчики | Да |
> | реестры/веб-перехватчики/Жеткаллбаккконфиг | Нет |
> | реестры, веб-перехватчики и проверка связи | Нет |

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
> | Оповещения | Нет |
> | BillingAccounts | Нет |
> | Расходы | Нет |
> | клаудконнекторс | Нет |
> | Коннекторы | Да |
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
> | Прогноз | Нет |
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
> | взаимосвязи | Нет |
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
> | рабочие области и Виртуалнетворкпирингс | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | catalogs | Да |
> | каталоги | Да |
> | каталоги данных и источники данных | Нет |
> | каталоги данных, источники данных и проверки | Нет |
> | каталоги данных/DataSources/scans/DataSets | Нет |
> | каталоги данных, источники данных, проверки и триггеры | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | dataFactories | Да |
> | Коэффициенты и diagnosticSettings | Нет |
> | Коэффициенты и metricDefinitions | Нет |
> | dataFactorySchema | Нет |
> | factories | Да |
> | фабрики/Интегратионрунтимес | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |
> | Accounts/Даталакестореаккаунтс | Нет |
> | Accounts/storageAccounts | Нет |
> | Accounts/storageAccounts/Containers | Нет |
> | Accounts/Трансфераналитиксунитс | Нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |
> | Accounts/Евентгридфилтерс | Нет |
> | Accounts/Фиреваллрулес | Нет |

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
> | серверы и помощники | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет |
> | серверы и Привателинкресаурцес | Нет |
> | серверы и Куеритекстс | Нет |
> | серверы и Рековераблесерверс | Нет |
> | серверы и Топкуеристатистикс | Нет |
> | серверы и Виртуалнетворкрулес | Нет |
> | серверы и Ваитстатистикс | Нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | серверы | Да |
> | серверы и помощники | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет |
> | серверы и Привателинкресаурцес | Нет |
> | серверы и Куеритекстс | Нет |
> | серверы и Рековераблесерверс | Нет |
> | серверы и Топкуеристатистикс | Нет |
> | серверы и Виртуалнетворкрулес | Нет |
> | серверы и Ваитстатистикс | Нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | серверграупс | Да |
> | серверы | Да |
> | серверы и помощники | Нет |
> | серверы и ключи | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет |
> | серверы и Привателинкресаурцес | Нет |
> | серверы и Куеритекстс | Нет |
> | серверы и Рековераблесерверс | Нет |
> | серверы и Топкуеристатистикс | Нет |
> | серверы и Виртуалнетворкрулес | Нет |
> | серверы и Ваитстатистикс | Нет |
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
> | аппликатионграупс и настольные системы | Нет |
> | аппликатионграупс/стартменуитемс | Нет |
> | хостпулс | Да |
> | хостпулс/сессионхостс | Нет |
> | хостпулс/сессионхостс/усерсессионс | Нет |
> | хостпулс/усерсессионс | Нет |
> | workspaces | Да |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | еластикпулс | Да |
> | Еластикпулс/Иосубтенантс | Да |
> | IotHubs | Да |
> | IotHubs/Евентгридфилтерс | Нет |
> | ProvisioningServices | Да |
> | usages | Нет |

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
> | databaseAccountNames | Нет |
> | databaseAccounts | Да |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | domains | Да |
> | домены/Домаиновнершипидентифиерс | Нет |
> | generateSsoRequest | Нет |
> | topLevelDomains | Нет |
> | validateDomainRegistrationInformation | Нет |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | lcsprojects | Нет |
> | лкспрожектс/клауддеплойментс | Нет |
> | лкспрожектс и соединители | Нет |

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
> | домены и темы | Нет |
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
> | пространства имен/authorizationrules | Нет |
> | пространства имен/дисастеррековериконфигс | Нет |
> | пространства имен/eventhubs | Нет |
> | пространства имен/eventhubs/authorizationrules | Нет |
> | пространства имен/eventhubs/eventhub | Нет |
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
> | мяреас/области | Нет |
> | мяреас/области/области | Нет |
> | мяреас/области/области/галлеритемс | Нет |
> | мяреас/области/галлеритемс | Нет |
> | мяреас/галлеритемс | Нет |
> | регистрация | Нет |
> | ресурсов | Нет |
> | retrieveresourcesbyid | Нет |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | конфигуратионпрофилеассигнментс | Нет |
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
> | кластеры и приложения | Нет |

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
> | хранилища и accessPolicies | Нет |
> | хранилища и Евентгридфилтерс | Нет |
> | хранилища и секреты | Нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | Да |
> | кластеры/аттачеддатабасеконфигуратионс | Нет |
> | кластеры и базы данных | Нет |
> | кластеры/базы данных/подключения | Нет |
> | кластеры/базы данных/евенсубконнектионс | Нет |
> | кластеры/шаредидентитиес | Нет |

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
> | рабочие области и расчеты | Нет |
> | рабочие области и Евентгридфилтерс | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Identities | Нет |
> | userAssignedIdentities | Да |

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
> | accounts | Да |
> | Accounts/Евентгридфилтерс | Нет |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | offers | Нет |
> | offerTypes | Нет |
> | Оффертипес и издатели | Нет |
> | Оффертипес, издатели и предложения | Нет |
> | Оффертипес, издатели, предложения и планы | Нет |
> | Оффертипес/Publishers/Offers/Plans/Agreement | Нет |
> | Оффертипес/Publishers/предложения, планы и конфигурации | Нет |
> | Оффертипес/Publishers/Offers/Plans/Configurations/Импортимаже | Нет |
> | privategalleryitems | Нет |
> | products | Нет |
> | publishers | Нет |
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
> | mediaservices/Аккаунтфилтерс | Нет |
> | mediaservices и активы | Нет |
> | mediaservices/активы/Ассетфилтерс | Нет |
> | mediaservices/КонтенткэйполиЦиес | Нет |
> | mediaservices/Евентгридфилтерс | Нет |
> | mediaservices/Лививентоператионс | Нет |
> | mediaservices/Лививентс | Да |
> | mediaservices/Лививентс/Ливеаутпутс | Нет |
> | mediaservices/Ливеаутпутоператионс | Нет |
> | mediaservices/Медиаграфс | Нет |
> | mediaservices/Стреаминжендпоинтоператионс | Нет |
> | mediaservices/Streamingendpoint | Да |
> | mediaservices/Стреаминглокаторс | Нет |
> | mediaservices/СтреамингполиЦиес | Нет |
> | mediaservices/преобразования | Нет |
> | mediaservices/преобразования/задания | Нет |

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
> | azureFirewallFqdnTags | Нет |
> | azureFirewalls | Да |
> | бастионхостс | Да |
> | bgpServiceCommunities | Нет |
> | connections | Да |
> | ddosCustomPolicies | Да |
> | ddosProtectionPlans | Да |
> | dnsOperationStatuses | Нет |
> | dnszones | Да |
> | днсзонес/A | Нет |
> | днсзонес/AAAA | Нет |
> | днсзонес/все | Нет |
> | днсзонес/CAA | Нет |
> | днсзонес и CNAME | Нет |
> | днсзонес/MX | Нет |
> | днсзонес/NS | Нет |
> | днсзонес/PTR | Нет |
> | днсзонес и наборы записей | Нет |
> | днсзонес/SOA | Нет |
> | днсзонес/SRV | Нет |
> | днсзонес/TXT | Нет |
> | expressRouteCircuits | Да |
> | expressRouteCrossConnections | Да |
> | експрессраутегатевайс | Да |
> | експрессраутепортс | Да |
> | expressRouteServiceProviders | Нет |
> | фиреваллполиЦиес | Да |
> | frontdoors | Да |
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
> | Нетворкватчерс/Коннектионмониторс | Да |
> | Нетворкватчерс/lenses | Да |
> | Нетворкватчерс/Пингмешес | Да |
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
> | serviceEndpointPolicies | Да |
> | trafficManagerGeographicHierarchies | Нет |
> | trafficmanagerprofiles | Да |
> | trafficmanagerprofiles/тепловые карты | Нет |
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
> | устройства | Нет |
> | linkTargets | Нет |
> | storageInsightConfigs | Нет |
> | workspaces | Да |
> | рабочие области и источники данных | Нет |
> | рабочие области и linkedServices | Нет |
> | рабочие области и запросы | Нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managementassociations | Нет |
> | managementconfigurations | Да |
> | solutions | Да |
> | узел "Представления" | Да |

## <a name="microsoftpeering"></a>Microsoft. пиринг

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | легаципирингс | Нет |
> | пираснс | Нет |
> | пиринги | Да |
> | пирингсервицепровидерс | Нет |
> | пирингсервицес | Да |

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
> | пространства имен/authorizationrules | Нет |
> | пространства имен/хибридконнектионс | Нет |
> | пространства имен/хибридконнектионс/authorizationrules | Нет |
> | пространства имен/вкфрелайс | Нет |
> | пространства имен/вкфрелайс/authorizationrules | Нет |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Нет |
> | коллекции | Да |
> | коллекции и приложения | Нет |
> | Collections/секуритипринЦипалс | Нет |
> | темплатеимажес | Нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | запросы | Да |
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
> | события | Нет |
> | impactedResources | Нет |
> | метаданные | Нет |
> | Уведомления | Нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | deployments | Нет |
> | развертывания и операции | Нет |
> | deploymentScripts | Да |
> | deploymentScripts и журналы | Нет |
> | ссылки | Нет |
> | notifyResourceJobs | Нет |
> | providers | Нет |
> | resourceGroups | Нет |
> | ресурсов | Нет |
> | subscriptions | Нет |
> | подписки и поставщики | Нет |
> | подписки и ресурсы | Нет |
> | подписки и имена | Нет |
> | Subscriptions/имена/Тагвалуес | Нет |
> | tenants | Нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | приложений | Да |
> | saasresources | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
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
> | оповещения | Нет |
> | allowedConnections | Нет |
> | applicationWhitelistings | Нет |
> | ассессментметадата | Нет |
> | оценки | Нет |
> | аутодисмиссалертсрулес | Нет |
> | инструментах автоматизации Composer | Да |
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
> | networkData | Нет |
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
> | задачи | Нет |
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
> | aggregations | Нет |
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
> | пространства имен | Да |
> | пространства имен/authorizationrules | Нет |
> | пространства имен/дисастеррековериконфигс | Нет |
> | пространства имен/евентгридфилтерс | Нет |
> | пространства имен/нетворкрулесетс | Нет |
> | пространства имен и очереди | Нет |
> | пространства имен/очереди/authorizationrules | Нет |
> | пространства имен и разделы | Нет |
> | пространства имен/разделы/authorizationrules | Нет |
> | пространства имен, разделы и подписки | Нет |
> | пространства имен, разделы, подписки и правила | Нет |
> | premiumMessagingRegions | Нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | приложений | Да |
> | clusters | Да |
> | кластеры и приложения | Нет |
> | containerGroups | Да |
> | контаинерграупсетс | Да |
> | edgeclusters | Да |
> | еджеклустерс и приложения | Нет |
> | networks | Да |
> | secretstores | Да |
> | секретсторес и сертификаты | Нет |
> | секретсторес/секреты | Нет |
> | volumes. | Да |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | приложений | Да |
> | containerGroups | Да |
> | gateways | Да |
> | networks | Да |
> | секретные коды | Да |
> | volumes. | Да |

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
> | приложений | Да |
> | jitRequests | Да |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managedInstances | Да |
> | Манажединстанцес и базы данных | Да |
> | Манажединстанцес/databases/БаккупшорттермретентионполиЦиес | Нет |
> | Манажединстанцес/базы данных, схемы, таблицы, столбцы и Сенситивитилабелс | Нет |
> | Манажединстанцес/databases/Вулнерабилитяссессментс | Нет |
> | Манажединстанцес/базы данных/Вулнерабилитяссессментс/правила и базовые планы | Нет |
> | Манажединстанцес/Енкриптионпротектор | Нет |
> | Манажединстанцес и ключи | Нет |
> | Манажединстанцес/Ресторабледроппеддатабасес/БаккупшорттермретентионполиЦиес | Нет |
> | Манажединстанцес/Вулнерабилитяссессментс | Нет |
> | серверы | Да |
> | серверы и администраторы | Нет |
> | серверы и Коммуникатионлинкс | Нет |
> | серверы и базы данных | Да |
> | серверы и Енкриптионпротектор | Нет |
> | серверы и Фиреваллрулес | Нет |
> | серверы и ключи | Нет |
> | серверы и Ресторабледроппеддатабасес | Нет |
> | серверы и сервицеобжективес | Нет |
> | серверы и Тдецертификатес | Нет |
> | виртуалклустерс | Нет |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Да |
> | Склвиртуалмачинеграупс/Аваилабилитиграуплистенерс | Нет |
> | SqlVirtualMachines | Да |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageAccounts | Да |
> | storageAccounts/Блобсервицес | Нет |
> | storageAccounts/Филесервицес | Нет |
> | storageAccounts/Куеуесервицес | Нет |
> | storageAccounts и службы | Нет |
> | storageAccounts/Services/metricDefinitions | Нет |
> | storageAccounts/Таблесервицес | Нет |
> | usages | Нет |

## <a name="microsoftstoragecache"></a>Microsoft. Сторажекаче

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Кэширует | Да |
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
> | Сторажесинксервицес/Регистередсерверс | Нет |
> | Сторажесинксервицес/Синкграупс | Нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | Нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | Нет |
> | Сторажесинксервицес и рабочие процессы | Нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | Да |
> | Сторажесинксервицес/Регистередсерверс | Нет |
> | Сторажесинксервицес/Синкграупс | Нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | Нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | Нет |
> | Сторажесинксервицес и рабочие процессы | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | Да |
> | Сторажесинксервицес/Регистередсерверс | Нет |
> | Сторажесинксервицес/Синкграупс | Нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | Нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | Нет |
> | Сторажесинксервицес и рабочие процессы | Нет |

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
> | cancel | Нет |
> | CreateSubscription | Нет |
> | enable | Нет |
> | переименовать | Нет |
> | SubscriptionDefinitions | Нет |
> | SubscriptionOperations | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | environments | Да |
> | среды и accessPolicies | Нет |
> | среды и классов EventSource | Да |
> | среды и Референцедатасетс | Да |

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
> | Апиманажементаккаунтс/списков ACL для | Нет |
> | Апиманажементаккаунтс/API | Нет |
> | Апиманажементаккаунтс/API/списков ACL для | Нет |
> | Апиманажементаккаунтс/API/списков ACL для | Нет |
> | Апиманажементаккаунтс/API/подключения | Нет |
> | Апиманажементаккаунтс/API/Connections/списков ACL для | Нет |
> | Апиманажементаккаунтс/API/Локализеддефинитионс | Нет |
> | Апиманажементаккаунтс/списков ACL для | Нет |
> | Апиманажементаккаунтс и подключения | Нет |
> | billingMeters | Нет |
> | certificates | Да |
> | connectionGateways | Да |
> | connections | Да |
> | customApis | Да |
> | deletedSites | Нет |
> | функции | Нет |
> | hostingEnvironments | Да |
> | hostingEnvironments/Мултиролепулс | Нет |
> | hostingEnvironments/внешнего размещения | Нет |
> | publishingUsers | Нет |
> | к просмотру фильмов | Нет |
> | resourceHealthMetadata | Нет |
> | runtimes | Нет |
> | serverFarms | Да |
> | serverFarms/Евентгридфилтерс | Нет |
> | sites | Да |
> | сайты и конфигурация  | Нет |
> | Sites/Евентгридфилтерс | Нет |
> | Sites/Хостнамебиндингс | Нет |
> | Sites/файл networkconfig | Нет |
> | Sites/премиераддонс | Да |
> | сайты и слоты | Да |
> | сайты/слоты/Евентгридфилтерс | Нет |
> | сайты/слоты/Хостнамебиндингс | Нет |
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

## <a name="next-steps"></a>Дальнейшие действия

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
