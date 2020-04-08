---
title: Удаление ресурсов в полном режиме
description: Здесь показано, как происходит удаление ресурсов в полном режиме в шаблонах Azure Resource Manager по типу ресурса.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e0250b289ce7a228d844023c3e1d1110438b3afc
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802575"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Удаление ресурсов Azure для развертываний в полном режиме

В этой статье описывается, как процесс удаления будет обрабатываться различными типами, когда он не происходит в шаблоне, развернутом в завершенном режиме.

Типы ресурсов, отмеченные **Yes,** удаляются, когда тип не в шаблоне, развернутом с полным режимом.

Типы ресурсов, отмеченные **No,** не удаляются автоматически, когда он не в шаблоне; однако они удаляются, если родительский ресурс удален. Чтобы получить полное описание поведения см. статью [Режимы развертывания в Azure Resource Manager](deployment-modes.md).

При развертывании [в шаблоне более чем одной группе ресурсов](cross-resource-group-deployment.md)ресурсы группы ресурсов, указанные в операции развертывания, могут быть удалены. Ресурсы в группах вторичных ресурсов не удаляются.

Перейти к названию поставщика ресурсов:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Авторизация](#microsoftauthorization)
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
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
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
> - [Share Microsoft.DataShare](#microsoftdatashare)
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
> - [Microsoft.Falcon](#microsoftfalcon)
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
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
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
> - [Microsoft.Notebooks](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.ПолитикаInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.Квантовая](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
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
> - [Microsoft.SpoolService](#microsoftspoolservice)
> - [Microsoft.S'L](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage;](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.ХранениеРепликация](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | DomainServices | Да |
> | DomainServices / oucontainer | нет |

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
> | оповещенияMetaData | нет |
> | alertsSummary | нет |
> | alertsSummaryList | нет |
> | smartDetectorAlertRules | Да |
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

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | configurationStores | Да |
> | configurationStores / eventGridFilters | нет |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

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
> | dataAliases | нет |
> | denyAssignments | нет |
> | elevateAccess | нет |
> | findOrphanRoleНазначения | нет |
> | locks | нет |
> | разрешения | нет |
> | policyAssignments | нет |
> | policyDefinitions | нет |
> | policySetDefinitions | нет |
> | providerOperations | нет |
> | roleAssignments | нет |
> | рольНазначенияИспользованиеМетри | нет |
> | roleDefinitions | нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | automationAccounts | Да |
> | автоматизацияСчета / конфигурации | Да |
> | автоматизацияСчета / рабочие места | нет |
> | автоматизацияСчета / privateEndpointConnectionProxies | нет |
> | автоматизацияСчета / privateEndpointConnections | нет |
> | автоматизацияСчета / privateLinkРесурсы | нет |
> | автоматизацияСчета / runbooks | Да |
> | автоматизацияСчета/ softwareUpdateConfigurations | нет |
> | автоматизацияСчета / веб-крючки | нет |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | configurationStores | Да |
> | configurationStores / eventGridFilters | нет |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | environments | нет |
> | среды / счета | нет |
> | среды / учетные записи / пространства имен | нет |
> | среды / учетные записи / именные пространства / конфигурации | нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | b2cDirectories | Да |
> | b2ctenants | нет |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | hybridDataManagers | Да |
> | postgresInstances | Да |
> | sqlInstances | Да |
> | sqlServerРегистрации | Да |
> | sqlServerРегистрации / sqlServers | нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | cloudManifestFiles | нет |
> | registrations | Да |
> | регистрации / подписка на клиента | нет |
> | регистрации / продукты | нет |

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
> | счетаСчета / соглашения | нет |
> | БиллингAccounts / биллингРазрешения | нет |
> | биллингAccounts / billingProfiles | нет |
> | биллингAccounts / billingProfiles / billingPermissions | нет |
> | биллингAccounts / billingProfiles / billingRoleНазначения | нет |
> | биллингAccounts / billingProfiles / billingRoleDefinitions | нет |
> | БиллингAccounts / billingProfiles / биллингПодписки | нет |
> | биллингAccounts / billingProfiles / createBillingRoleAssignment | нет |
> | биллингAccounts / billingProfiles / клиенты | нет |
> | биллингAccounts / billingProfiles / инструкции | нет |
> | биллингAccounts / billingProfiles / счета-фактуры | нет |
> | биллингAccounts / billingProfiles / счета/ счета / ценовой лист | нет |
> | счетаСчета / биллингПрофили / счета/ транзакции | нет |
> | биллингAccounts / billingProfiles / накладныеразделы | нет |
> | биллингAccounts / billingProfiles / накладныеразделы / биллингРазрешения | нет |
> | биллингAccounts / billingProfiles / накладныеразделы / биллингРоназначение | нет |
> | биллингAccounts / billingProfiles / накладныеразделы / биллингРолины | нет |
> | биллингAccounts / billingProfiles / накладные разделы / биллингПодписки | нет |
> | биллингAccounts / billingProfiles / накладныеразделы / createBillingRoleAssignment | нет |
> | биллингAccounts / billingProfiles / накладныеРазделы / инициироватьПередача | нет |
> | биллингAccounts / billingProfiles / накладныеразделы / продукты | нет |
> | биллингAccounts / billingProfiles / накладные разделы / продукты / передача | нет |
> | биллингAccounts / billingProfiles / накладные разделы / продукты / обновлениеAutoRenew | нет |
> | биллингAccounts / billingProfiles / накладныеразделы / транзакции | нет |
> | биллингAccounts / billingProfiles / накладныеРазделы / переводы | нет |
> | биллинг-счета / БиллингПрофилс / патч-операции | нет |
> | биллингAccounts / billingProfiles / paymentMethods | нет |
> | биллингAccounts / billingProfiles / политики | нет |
> | биллингAccounts / billingProfiles / цена лист | нет |
> | биллингAccounts / billingProfiles / pricesheetDownloadOperations | нет |
> | биллингAccounts / billingProfiles / продукты | нет |
> | счетаСчета / биллингПрофили / транзакции | нет |
> | биллингAccounts / биллингРоназначения | нет |
> | биллингAccounts / биллингРолетопределения | нет |
> | Биллинг-аккаунты / подписка на счета | нет |
> | счета-счета / биллингПодписки / счета | нет |
> | биллинг-счета / созданиеБиллингРолназначения | нет |
> | биллинг-счета / созданиеInvoiceSectionOperations | нет |
> | счетаСчета / клиенты | нет |
> | биллингAccounts / клиентов / биллингРазрешения | нет |
> | биллинг-аккаунты / клиенты / подписка на счета | нет |
> | биллингСчета / клиенты / инициироватьПеревод | нет |
> | счетаСчета / клиенты / политики | нет |
> | биллингСчета / клиенты / продукты | нет |
> | счетаСчета / клиенты / транзакции | нет |
> | биллингСчета / клиенты / переводы | нет |
> | счетаСчета / отделы | нет |
> | счетаСчета / регистрационные счета | нет |
> | счета-фактуры / счета-фактуры | нет |
> | счетаСчета / накладныеразделы | нет |
> | биллингAccounts / накладныеразделы / биллингSubscriptionMoveOperations | нет |
> | биллинг-счета / накладныеразделы / подписка на счета | нет |
> | биллингAccounts / накладныеразделы / биллингПодписки / передача | нет |
> | биллинг-счета / счета-фактурыРазделы / повышение | нет |
> | биллинг-счета / счета-фактурыРазделы / инициироватьПередача | нет |
> | счетаСчета / накладныеРазделы / патч-операции | нет |
> | биллинг-счета / накладныеразделы / productMoveOperations | нет |
> | счетаСчета / накладныеразделы / продукты | нет |
> | биллингСчета / счета-фактурыРазделы / продукты / передача | нет |
> | биллинг-счета / накладныеразделы / продукты / обновлениеAutoRenew | нет |
> | счетаСчета / накладныеразделы / транзакции | нет |
> | счетаСчета / счетаРаздели / переводы | нет |
> | биллинг-счета / lineOfCredit | нет |
> | счетаСчета / патч-операции | нет |
> | счетаСчета / paymentMethods | нет |
> | счетаСчета / продукты | нет |
> | счетаСчета / транзакции | нет |
> | billingPeriods | нет |
> | БиллингРазрешения | нет |
> | billingProperty | нет |
> | БиллингРоназначения | нет |
> | БиллингРоОпределения | нет |
> | создатьБиллингРоназначение | нет |
> | departments | нет |
> | enrollmentAccounts | нет |
> | invoices | нет |
> | transfers | нет |
> | переводы / приемТрансфер | нет |
> | переводы / отклонениеТрансфер | нет |
> | переводы / операцияСтатус | нет |
> | переводы / проверкаПередача | нет |
> | проверитьАдрес | нет |

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
> | блокчейнЧлены | Да |
> | кордаЧлены | Да |
> | Наблюдателей | Да |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | ТокенСервисы | Да |
> | Токенсервисы / BlockchainNetworks | нет |
> | ТокенСервисы / Группы | нет |
> | Токенсервисы / Группы / Счета | нет |
> | TokenServices / ТокенШаблоны | нет |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | blueprintAssignments | нет |
> | чертежНазначения / назначенияОперации | нет |
> | чертежНазначения / операции | нет |
> | blueprints | нет |
> | чертежи / артефакты | нет |
> | чертежи / версии | нет |
> | чертежи / версии / артефакты | нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | botServices | Да |
> | botServices / каналы | нет |
> | botServices / соединения | нет |
> | языки | нет |
> | шаблоны | нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Redis | Да |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | appliedReservations | нет |
> | автоквокуамУвеличение | нет |
> | calculateExchange | нет |
> | calculatePrice | нет |
> | вычислитьPurchasePrice | нет |
> | catalogs | нет |
> | commercialReservationOrders | нет |
> | обмен валюты | нет |
> | placePurchaseOrder | нет |
> | reservationOrders | нет |
> | БронированиеЗаказы / вычислитьВозврат | нет |
> | ReservationOrders / слияние | нет |
> | БронированиеЗаказы / бронирование | нет |
> | БронированиеЗаказы / бронирование / пересмотры | нет |
> | БронированиеЗаказы / возвращение | нет |
> | БронированиеЗаказы / раскол | нет |
> | ReservationOrders / своп | нет |
> | reservations | нет |
> | ресурспровайдеры | нет |
> | ресурсов | нет |
> | validateReservationOrder | нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSetSets | нет |
> | CdnWebApplicationFirewallПолитика | Да |
> | edgenodes | нет |
> | профили | Да |
> | профили / конечные точки | Да |
> | профили / конечные точки / пользовательские домемы | нет |
> | профили / конечные точки / группы происхождения | нет |
> | профили / конечные точки / происхождение | нет |
> | validateProbe | нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | certificateOrders | Да |
> | сертификатЗаказы / сертификаты | нет |
> | validateCertificateRegistrationInformation | нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | capabilities | нет |
> | domainNames | Да |
> | domainNames / возможности | нет |
> | domainNames / internalLoadBalancers | нет |
> | domainNames / serviceCertificates | нет |
> | domainNames / слоты | нет |
> | domainNames / слоты / роли | нет |
> | domainNames / слоты / роли / метрическиеопределения | нет |
> | domainNames / слоты / роли / метрики | нет |
> | moveSubscriptionResources | нет |
> | operatingSystemFamilies | нет |
> | operatingSystems | нет |
> | quotas | нет |
> | resourceTypes | нет |
> | validateSubscriptionMoveAvailability | нет |
> | virtualMachines | Да |
> | virtualMachines / диагностикаНастройки | нет |
> | virtualMachines / метрическиеопределения | нет |
> | virtualMachines / метрики | нет |

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
> | expressRouteCrossConnections / пиринги | нет |
> | gatewaySupportedDevices | нет |
> | networkSecurityGroups | Да |
> | quotas | нет |
> | reservedIps | Да |
> | virtualNetworks | Да |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | нет |
> | virtualNetworks / virtualNetworkPeerings | нет |

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
> | хранениеСчета / blobServices | нет |
> | учетные данные / fileServices | нет |
> | учетные данные / метрическиеопределения | нет |
> | учетные записи / метрики | нет |
> | учетные записи / очередиСервисы | нет |
> | хранениеСчета / услуги | нет |
> | хранениеСчета / услуги / диагностикаНастройки | нет |
> | хранениеСчета / услуги / метрическиеопределения | нет |
> | хранениеСчета / услуги / метрики | нет |
> | хранениеСчета / таблицыСервисы | нет |
> | storageAccounts / vmImages | нет |
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
> | diskEncryptionSets | Да |
> | disks | Да |
> | galleries | Да |
> | галереи / приложения | нет |
> | галереи / приложения / версии | нет |
> | галереи / изображения | нет |
> | галереи / изображения / версии | нет |
> | хостГрупп | Да |
> | hostGroups / хосты | Да |
> | images | Да |
> | proximityPlacementGroups | Да |
> | restorePointCollections | Да |
> | восстановитьПойнтКоллекции / восстановитьPoints | нет |
> | общиеVMExtensions | Да |
> | общие VMExtensions / версии | нет |
> | sharedVMImages | Да |
> | sharedVMImages / версии | нет |
> | snapshots | Да |
> | sshPublicKeys | Да |
> | virtualMachines | Да |
> | virtualMachines / расширения | Да |
> | virtualMachines / метрическиеопределения | нет |
> | virtualMachineScaleSets | Да |
> | virtualMachineScaleSets / расширения | нет |
> | virtualMachineScaleSets / NetworkInterfaces | нет |
> | virtualMachineScaleSets / publicIPAddresses | нет |
> | virtualMachineScaleSets / виртуальныемашины | нет |
> | virtualMachineScaleSets / виртуальныемашины / сетевыеинтерфейсы | нет |

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
> | реестры / agentPools | Да |
> | реестры / сборки | нет |
> | реестры / сборки / отмена | нет |
> | реестры / сборки / getLogLink | нет |
> | реестры / сборкаЗадачи | Да |
> | реестры / сборкаЗадачи / шаги | нет |
> | реестры / eventGridFilters | нет |
> | реестры / generateCredentials | нет |
> | реестры / getBuildSourceUploadUrl | нет |
> | реестры / GetCredentials | нет |
> | реестры / importImage | нет |
> | реестры / privateEndpointConnectionProxies | нет |
> | реестры / privateEndpointConnectionProxies / проверка | нет |
> | реестры / privateEndpointConnections | нет |
> | реестры / privateLinkРесурсы | нет |
> | реестры / очередьСтроительство | нет |
> | реестры / регенерироватьCredential | нет |
> | реестры / регенерироватьCredentials | нет |
> | реестры / репликации | Да |
> | реестры / работает | нет |
> | реестры / работает / отменить | нет |
> | реестры / расписаниеRun | нет |
> | реестры / scopeMaps | нет |
> | реестры / taskRuns | Да |
> | реестры / задачи | Да |
> | реестры / токены | нет |
> | реестры / UpdateПолитика | нет |
> | реестры / веб-крючки | Да |
> | реестры / webhooks / getCallbackConfig | нет |
> | реестры / webhooks / пинг | нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | containerServices | Да |
> | managedClusters | Да |
> | openShiftManagedКластеры | Да |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | видны узлы | нет |
> | BillingAccounts | нет |
> | сведения о бюджете; | нет |
> | Облачные Коннектодоры | нет |
> | Соединители | Да |
> | Departments | нет |
> | Измерения | нет |
> | EnrollmentAccounts | нет |
> | Экспорт | нет |
> | Внешниесчета | нет |
> | Внешниесчета / Оповещения | нет |
> | ВнешниеСчета / Размеры | нет |
> | Внешниесчета / Прогноз | нет |
> | Внешниесчета / Запрос | нет |
> | Внешняя подписка | нет |
> | Внешняя подписка / Оповещения | нет |
> | Внешняя подписка / Размеры | нет |
> | Внешняя подписка / Прогноз | нет |
> | Внешняя подписка / Запрос | нет |
> | Прогноз | нет |
> | Запрос | нет |
> | регистрация | нет |
> | Reportconfigs | нет |
> | Отчеты | нет |
> | Параметры | нет |
> | showbackПравила | нет |
> | Представления | нет |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | requests | нет |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | взаимосвязи | нет |
> | ресурспровайдеры | Да |

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
> | рабочие области / dbWorkspaces | нет |
> | рабочие места / шифрование хранилища | нет |
> | рабочие места / virtualNetworkPeerings | нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | catalogs | Да |
> | каталоги данных | Да |
> | каталоги данных / источники данных | нет |
> | каталоги данных / источники данных / сканирование | нет |
> | каталоги данных / источники данных / сканирование / наборы данных | нет |
> | каталоги данных / источники данных / сканирование / триггеры | нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | dataFactories | Да |
> | dataFactories / diagnosticSettings | нет |
> | dataFactories / метрическиеопределения | нет |
> | dataFactorySchema | нет |
> | factories | Да |
> | фабрики / интеграцияRuntimes | нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |
> | учетные записи / данныеLakeStoreAccounts | нет |
> | счета / учетные записи | нет |
> | счета / хранилищаСчета / контейнеры | нет |
> | счета / transferAnalyticsUnits | нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |
> | счета / eventGridFilters | нет |
> | учетные записи / брандмауэрЫПравила | нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | services; | Да |
> | услуги / проекты | Да |

## <a name="microsoftdatashare"></a>Share Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |
> | счета / акции | нет |
> | счета / акции / наборы данных | нет |
> | счета / акции / приглашения | нет |
> | счета / акции / providersharesubscriptions | нет |
> | счета / акции / синхронизацияНастройки | нет |
> | счета / подписка на акции | нет |
> | учетные записи / подписка на акции / consumerSourceDataSets | нет |
> | учетные записи / подписка на акции / набор данных | нет |
> | счета / подписка на акции / триггеры | нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | servers | Да |
> | серверы / консультанты | нет |
> | серверы / ключи | нет |
> | серверы / privateEndpointConnectionProxies | нет |
> | серверы / privateEndpointConnections | нет |
> | серверы / privateLinkРесурсы | нет |
> | серверы / запросЫТексты | нет |
> | серверы / восстанавливаемыесерверы | нет |
> | серверы / Топ-КуиверСтатистика | нет |
> | серверы / virtualNetworkРулес | нет |
> | серверы / ожиданиеСтатистика | нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | servers | Да |
> | серверы / консультанты | нет |
> | серверы / ключи | нет |
> | серверы / privateEndpointConnectionProxies | нет |
> | серверы / privateEndpointConnections | нет |
> | серверы / privateLinkРесурсы | нет |
> | серверы / запросЫТексты | нет |
> | серверы / восстанавливаемыесерверы | нет |
> | серверы / Топ-КуиверСтатистика | нет |
> | серверы / virtualNetworkРулес | нет |
> | серверы / ожиданиеСтатистика | нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | сервергруппы | Да |
> | servers | Да |
> | серверы / консультанты | нет |
> | серверы / ключи | нет |
> | серверы / privateEndpointConnectionProxies | нет |
> | серверы / privateEndpointConnections | нет |
> | серверы / privateLinkРесурсы | нет |
> | серверы / запросЫТексты | нет |
> | серверы / восстанавливаемыесерверы | нет |
> | серверы / Топ-КуиверСтатистика | нет |
> | серверы / virtualNetworkРулес | нет |
> | серверы / ожиданиеСтатистика | нет |
> | serversv2 | Да |
> | singleServers | Да |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | артефактИсточники | Да |
> | rollouts | Да |
> | serviceTopologies | Да |
> | serviceTopologies / услуги | Да |
> | serviceTopologies / услуги / сервисИкуты | Да |
> | steps | Да |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | группы заявок | Да |
> | группы приложений / приложения | нет |
> | группы приложений / настольные компьютеры | нет |
> | группы приложений / startmenuitems | нет |
> | хостпулы | Да |
> | hostpools / сессионные хозяева | нет |
> | hostpools / сессионные хосты / usersessions | нет |
> | hostpools / usersessions | нет |
> | workspaces | Да |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Эластичные бассейны | Да |
> | ElasticPools / IotHubTenants | Да |
> | ElasticPools / IotHubTenants / securitySettings | нет |
> | IotHubs | Да |
> | IotHubs / eventGridFilters | нет |
> | IotHubs / безопасностьНастройки | нет |
> | ProvisioningServices | Да |
> | usages | нет |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Трубопроводов | Да |

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
> | лаборатории / среды | Да |
> | лаборатории / serviceRunners | Да |
> | лаборатории / виртуальныемашины | Да |
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
> | домены / domainOwnershipИт | нет |
> | generateSsoRequest | нет |
> | topLevelDomains | нет |
> | validateDomainRegistrationInformation | нет |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | lcsprojects | нет |
> | lcsprojects / облачные развертывания | нет |
> | lcsprojects / разъемы | нет |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | services; | Да |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | domains | Да |
> | домены / темы | нет |
> | eventSubscriptions | нет |
> | extensionTopics | нет |
> | partnerNamespaces | Да |
> | partnerNamespaces / eventChannels | нет |
> | партнеррегистрации | Да |
> | partnerTopics | Да |
> | partnerTopics / EventПодписки | нет |
> | systemTopics | Да |
> | systemTopics / EventПодписки | нет |
> | topics | Да |
> | topicTypes | нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | Да |
> | пространства имен | Да |
> | именные пространства / правила авторизации | нет |
> | namespaces / disasterrecoveryconfigs | нет |
> | именные пространства / eventhubs | нет |
> | namespaces / eventhubs / authorizationrules | нет |
> | именные пространства / eventhubs / группы потребителей | нет |
> | именные пространства / сетевые рулеты | нет |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | пространства имен | Да |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | featureProviders | нет |
> | features | нет |
> | providers | нет |
> | subscriptionFeatureРегистрации | нет |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | enroll | нет |
> | galleryitems | нет |
> | generateartifactaccessuri | нет |
> | myareas | нет |
> | myareas / области | нет |
> | myareas / области / области | нет |
> | myareas / области / области / galleryitems | нет |
> | myareas / области / galleryitems | нет |
> | myareas / galleryitems | нет |
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
> | автоуправляемыесчета | Да |
> | autoManagedVmConfigurationProfiles | Да |
> | configurationProfileНазначенияи | нет |
> | guestConfigurationAssignments | нет |
> | software | нет |
> | программное обеспечениеUpdateProfile | нет |
> | softwareUpdates | нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | hanaInstances | Да |
> | sapMonitors | Да |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | выделенные HSMs | Да |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | Да |
> | кластеры / приложения | нет |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | services; | Да |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Машины | Да |
> | машины / расширения | Да |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | dataManagers | Да |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | components | Да |
> | networkScopes | Да |

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
> | appTemplates | нет |
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
> | hsmPools | Да |
> | vaults | Да |
> | хранилища / accessПолитика | нет |
> | хранилища / eventGridFilters | нет |
> | хранилища / секреты | нет |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | подключенныекластеры | Да |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | Да |
> | кластеры / прилагаемыенастройкныенастройки | нет |
> | кластеры / базы данных | нет |
> | кластеры / базы данных / подключения к данным | нет |
> | кластеры / базы данных / eventhubconnections | нет |
> | кластеры / базы данных / основные назначения | нет |
> | кластеры / подключения к данным | нет |
> | кластеры / основные назначения | нет |
> | кластеры / доли | нет |

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
> | интеграцияServiceясреда | Да |
> | integrationServiceEnvironments / managedApis | Да |
> | изолированныеКиэкологи | Да |
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
> | рабочие области / вычисления | нет |
> | рабочие места / eventGridFilters | нет |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | applyUpdates | нет |
> | конфигурацияНазначения | нет |
> | обслуживаниеКонфигурации | Да |
> | Обновления | нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Identities | нет |
> | userAssignedIdentities | Да |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | рынокРегистрацияОпределения | нет |
> | регистрацияНазначения | нет |
> | регистрацияОпределения | нет |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | getEntities | нет |
> | managementGroups | нет |
> | группы управления / настройки | нет |
> | ресурсов | нет |
> | startTenantBackfill | нет |
> | tenantBackfillStatus | нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |
> | счета / eventGridFilters | нет |
> | счета / privateAtlases | Да |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | offers | нет |
> | offerTypes | нет |
> | offerTypes / издатели | нет |
> | offerTypes / издатели / предложения | нет |
> | offerTypes / издатели / предложения / планы | нет |
> | offerTypes / издатели / предложения / планы / соглашения | нет |
> | offerTypes / издатели / предложения / планы / конфигурации | нет |
> | offerTypes / издатели / предложения / планы / конфигурации / importImage | нет |
> | privategalleryitems | нет |
> | privateStoreКлиент | нет |
> | privateStores | нет |
> | privateStores / предложения | нет |
> | products | нет |
> | publishers | нет |
> | издатели / предложения | нет |
> | издатели / предложения / поправки | нет |
> | регистрация | нет |

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
> | медиасервисы / аккаунтфильтры | нет |
> | медиасервисы / активы | нет |
> | медиасервисы / активы / активыФильтры | нет |
> | медиа-услуги / contentKeyPolicies | нет |
> | медиасервисы / eventGridFilters | нет |
> | медиасервисы / liveEventOperations | нет |
> | медиа-услуги / liveEvents | Да |
> | mediaservices / liveEvents / liveOutputs | нет |
> | медиасервисы / liveOutputOperations | нет |
> | медиа-услуги / mediaGraphs | нет |
> | медиасервисы / streamingEndPointOperations | нет |
> | медиасервисы / streamingEndpoints | Да |
> | медиасервисы / стриминговыелокаторы | нет |
> | медиасервисы / streamingПолитика | нет |
> | медиа-услуги / преобразования | нет |
> | медиа-услуги / преобразования / рабочие места | нет |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | appКластеры | Да |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | оценкаПроекты | Да |
> | migrateprojects | Да |
> | moveСборы | Да |
> | projects | Да |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | голографикаВещательныесчета | Да |
> | объектПониманиеСчетов | Да |
> | remoteRenderingAccounts | Да |
> | пространственныеясчетаСчета | Да |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | netAppAccounts | Да |
> | netAppAccounts / Backups | нет |
> | netAppAccounts / емкостьPools | Да |
> | netAppAccounts / емкостьБассейны / объемы | Да |
> | netAppAccounts / емкостьБассейны / тома / снимки | Да |
## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | applicationGateways | Да |
> | приложениеGatewayWebApplicationFirewallПолитики | Да |
> | applicationSecurityGroups | Да |
> | azureFirewallFqdnTags | нет |
> | azureFirewalls | Да |
> | бастионХомсты | Да |
> | bgpServiceCommunities | нет |
> | connections | Да |
> | ddosCustomPolicies | Да |
> | ddosProtectionPlans | Да |
> | dnsOperationStatuses | нет |
> | dnszones | Да |
> | dnszones / A | нет |
> | dnszones / AAAA | нет |
> | dnszones / все | нет |
> | dnszones / CAA | нет |
> | dnszones / CNAME | нет |
> | dnszones / MX | нет |
> | dnszones / NS | нет |
> | dnszones / PTR | нет |
> | dnszones / рекорды | нет |
> | dnszones / SOA | нет |
> | dnszones / SRV | нет |
> | dnszones / TXT | нет |
> | expressRouteCircuits | Да |
> | expressRouteCrossConnections | Да |
> | expressRouteGateways | Да |
> | expressRoutePorts | Да |
> | expressRouteServiceProviders | нет |
> | брандмауэрПолитика | Да |
> | frontdoors | Да |
> | frontdoorWebApplicationFirewallManagedRuleSets | нет |
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
> | networkWatchers / connectionMonitors | Да |
> | networkWatchers / линзы | Да |
> | networkWatchers / pingMeshes | Да |
> | p2sVpnGateways | Да |
> | privateDnsOperationStatuses | нет |
> | privateDnsзоны | Да |
> | privateDnszones / A | нет |
> | privateDnszones / AAAA | нет |
> | privateDnsзоны / все | нет |
> | privateDnsзоны / CNAME | нет |
> | privateDnszones / MX | нет |
> | privateDnszones / PTR | нет |
> | privateDnszones / SOA | нет |
> | privateDnszones / SRV | нет |
> | privateDnszones / TXT | нет |
> | privateDnszones / virtualNetworkLinks | Да |
> | privateEndpoints | Да |
> | privateLinkServices | Да |
> | publicIPAddresses | Да |
> | publicIPPrefixes | Да |
> | routeFilters | Да |
> | routeTables | Да |
> | serviceEndpointPolicies | Да |
> | trafficManagerGeographicHierarchies | нет |
> | trafficmanagerprofiles | Да |
> | trafficmanagerprofiles / heatMaps | нет |
> | trafficManagerUserMetricsKeys | нет |
> | virtualHubs | Да |
> | virtualNetworkGateways | Да |
> | virtualNetworks | Да |
> | virtualNetworkTaps | Да |
> | virtualWans | Да |
> | vpnGateways | Да |
> | vpnSites | Да |
> | webApplicationFirewallPolicies | Да |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | NotebookProxies | нет |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | пространства имен | Да |
> | пространства имен / notificationHubs | Да |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | osNameпространства | Да |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | ГиперВ-сайты | Да |
> | Импортные сайты | Да |
> | Серверсайты | Да |
> | VMwareSites | Да |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | clusters | Да |
> | linkTargets | нет |
> | storageInsightConfigs | нет |
> | workspaces | Да |
> | рабочие области / экспорт данных | нет |
> | рабочие области / данныеИсточники | нет |
> | рабочие места / linkedServices | нет |
> | рабочие области / связанныеучетныеУчет | нет |
> | рабочие области / запрос | нет |
> | рабочие области / scopedPrivateLinkProxies | нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managementassociations | нет |
> | managementconfigurations | Да |
> | solutions | Да |
> | узел "Представления" | Да |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | legacyPeerings | нет |
> | peerAsns | нет |
> | вглядывания | Да |
> | peeringServiceCountries | нет |
> | peeringServiceProviders | нет |
> | peeringServices | Да |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | policyEvents | нет |
> | политикаМетадата | нет |
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

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | Да |

## <a name="microsoftquantum"></a>Microsoft.Квантовая

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Рабочие области | Да |

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
> | именные пространства / правила авторизации | нет |
> | именные пространства / гибридные соединения | нет |
> | namespaces / гибридныесоединения / authorizationrules | нет |
> | именные пространства / wcfrelays | нет |
> | namespaces / wcfrelays / authorizationrules | нет |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | accounts | нет |
> | коллекции | Да |
> | коллекции / приложения | нет |
> | коллекции / безопасностьпринципиальны | нет |
> | templateImages | нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Запросы | Да |
> | ресурсИзменениеПодробнее | нет |
> | ресурсИзменения | нет |
> | ресурсов | нет |
> | РесурсыИстория | нет |
> | subscriptionsStatus | нет |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | availabilityStatuses | нет |
> | childAvailabilityStatuses | нет |
> | childResources | нет |
> | emergingissues | нет |
> | события | нет |
> | impactedResources | нет |
> | метаданные | нет |
> | Уведомления | нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | deployments | нет |
> | развертывание / операции | нет |
> | развертываниеScripts | Да |
> | развертываниеСценарии / журналы | нет |
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
> | адаптивныеОсетные Хардендинги | нет |
> | advancedThreatProtectionSettings | нет |
> | оповещения | нет |
> | allowedConnections | нет |
> | applicationWhitelistings | нет |
> | оценкаМетадата | нет |
> | оценки | нет |
> | autoDismissAlertsПравила | нет |
> | автоматизация | Да |
> | AutoProvisioningSettings | нет |
> | Compliances | нет |
> | dataCollectionAgents | нет |
> | устройствоSecurityGroups | нет |
> | discoveredSecuritySolutions | нет |
> | externalSecuritySolutions | нет |
> | InformationProtectionPolicies | нет |
> | iotSecuritySolutions | Да |
> | iotSecuritySolutions / AnalyticsModels | нет |
> | iotSecuritySolutions / AnalyticsModels / aggregatedAlerts | нет |
> | iotSecuritySolutions / AnalyticsModels / агрегированныеРекомендации | нет |
> | jitNetworkAccessPolicies | нет |
> | networkData | нет |
> | политики | нет |
> | pricings | нет |
> | нормативныестандарты | нет |
> | regulatoryComplianceStandards / regulatoryComplianceControls | нет |
> | нормативное соответствиеСтандартам / нормативному соответствиюКонтролю / Регулятивные Оценки | нет |
> | secureScoreControlОпределения | нет |
> | безопасныйСкорКонтроль | нет |
> | secureScores | нет |
> | secureScores / secureScoreControls | нет |
> | securityContacts | нет |
> | securitySolutions | нет |
> | securitySolutionsReferenceData | нет |
> | securityStatuses | нет |
> | securityStatusesSummaries | нет |
> | серверУязвимостьОценки | нет |
> | Параметры | нет |
> | субоценки | нет |
> | задачи | нет |
> | topologies | нет |
> | workspaceSettings | нет |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | diagnosticSettings | нет |
> | diagnosticSettingsCategories | нет |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | aggregations | нет |
> | alertRules | нет |
> | alertRuleTemplates | нет |
> | закладки | нет |
> | cases | нет |
> | DataConnectors | нет |
> | dataConnectorsCheckRequirements | нет |
> | Сущности | нет |
> | сущностьЗапросы | нет |
> | Инцидентов | нет |
> | офисСогласия | нет |
> | Параметры | нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | пространства имен | Да |
> | именные пространства / правила авторизации | нет |
> | namespaces / disasterrecoveryconfigs | нет |
> | именные пространства / eventgridfilters | нет |
> | именные пространства / сетевые рулеты | нет |
> | именные пространства / очереди | нет |
> | именные пространства / очереди / авторизационные правила | нет |
> | именные пространства / темы | нет |
> | namespaces / темы / authorizationrules | нет |
> | именные пространства / темы / подписки | нет |
> | именные пространства / темы / подписки / правила | нет |
> | premiumMessagingRegions | нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | веб-масштабированием; | Да |
> | clusters | Да |
> | кластеры / приложения | нет |
> | containerGroups | Да |
> | контейнерGroupSets | Да |
> | edgeclusters | Да |
> | краевые кластеры / приложения | нет |
> | managedclusters | Да |
> | управляемыекластеры / узлы | нет |
> | networks | Да |
> | secretstores | Да |
> | тайные магазины / сертификаты | нет |
> | тайные магазины / секреты | нет |
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

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | providerRegistrations | нет |
> | providerRegistrations / resourceTypeRegistrations | нет |
> | rollouts | Да |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SignalR | Да |
> | SignalR / eventGridFilters | нет |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SiteRecoveryVault | Да |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | hybridUseBenefits | нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | applicationDefinitions | Да |
> | веб-масштабированием; | Да |
> | jitRequests | Да |

## <a name="microsoftspoolservice"></a>Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | зарегистрированныеПодписки | нет |
> | Катушки | Да |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | managedInstances | Да |
> | управляемыеInstances / базы данных | Да |
> | managedInstances / базы данных / резервное копированиеShortTermRetentionPolicies | нет |
> | managedInstances / базы данных / схемы / таблицы / столбцы / чувствительностьLabels | нет |
> | managedInstances / базы данных / оценки уязвимости | нет |
> | managedInstances / базы данных / уязвимостьОценки / правила / базовые | нет |
> | управляемыеInstances / шифрованиеПротектор | нет |
> | управляемыеInstances / ключи | нет |
> | управляемыеInstances / restorableDroppedDatabases / резервное копированиеShortTermRetentionПолитика | нет |
> | управляемыеInstances / уязвимостьОценки | нет |
> | servers | Да |
> | серверы / администраторы | нет |
> | серверы / communicationLinks | нет |
> | серверы / базы данных | Да |
> | серверы / шифрованиеПротектор | нет |
> | серверы / брандмауэрЫПравила | нет |
> | серверы / ключи | нет |
> | серверы / восстановительныеБазы данных | нет |
> | серверы / сервис-цели | нет |
> | серверы / tdeCertificates | нет |
> | виртуальныекластеры | нет |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Да |
> | SqlVirtualMachineGroups / ДоступностьGroupСлушатели | нет |
> | SqlVirtualMachines | Да |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageAccounts | Да |
> | хранениеСчета / blobServices | нет |
> | учетные данные / fileServices | нет |
> | учетные записи / очередиСервисы | нет |
> | хранениеСчета / услуги | нет |
> | хранениеСчета / услуги / метрическиеопределения | нет |
> | хранениеСчета / таблицыСервисы | нет |
> | usages | нет |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | Кэшей | Да |
> | кэши / храненияЦели | нет |
> | usageModels | нет |

## <a name="microsoftstoragereplication"></a>Microsoft.ХранениеРепликация

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | репликацииГруппы | нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | Да |
> | storageSyncServices / зарегистрированныеСерверы | нет |
> | storageSyncServices / syncGroups | нет |
> | storageSyncServices / syncGroups / cloudEndpoints | нет |
> | storageSyncServices / syncGroups / serverEndpoints | нет |
> | storageSyncServices / рабочие процессы | нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | Да |
> | storageSyncServices / зарегистрированныеСерверы | нет |
> | storageSyncServices / syncGroups | нет |
> | storageSyncServices / syncGroups / cloudEndpoints | нет |
> | storageSyncServices / syncGroups / serverEndpoints | нет |
> | storageSyncServices / рабочие процессы | нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | storageSyncServices | Да |
> | storageSyncServices / зарегистрированныеСерверы | нет |
> | storageSyncServices / syncGroups | нет |
> | storageSyncServices / syncGroups / cloudEndpoints | нет |
> | storageSyncServices / syncGroups / serverEndpoints | нет |
> | storageSyncServices / рабочие процессы | нет |

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
> | subscriptions | нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | environments | Да |
> | среды / доступПолитика | нет |
> | среды / источники событий | Да |
> | среды / ссылкиDataSets | Да |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | посвященныйCloudNodes | Да |
> | посвященныйCloudServices | Да |
> | virtualMachines | Да |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | устройства | Да |
> | зарегистрированныеПодписки | нет |
> | Поставщиков | нет |
> | продавцы / сукус | нет |
> | продавцы / vnfs | нет |
> | virtualNetworkFunctionSkus | нет |
> | vnfs | Да |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Удаление ресурсов в полном режиме |
> | ------------- | ----------- |
> | apiManagementAccounts | нет |
> | apiManagementAccounts / apiAcls | нет |
> | apiManagementAccounts / apis | нет |
> | apiManagementAccounts / apis / apiAcls | нет |
> | apiManagementAccounts / apis / connectionAcls | нет |
> | apiManagementAccounts / apis / соединения | нет |
> | apiManagementAccounts / apis / соединения / connectionAcls | нет |
> | apiManagementAccounts / apis / локализованныеОпределения | нет |
> | apiManagementAccounts / connectionAcls | нет |
> | apiManagementAccounts / соединения | нет |
> | billingMeters | нет |
> | certificates | Да |
> | connectionGateways | Да |
> | connections | Да |
> | customApis | Да |
> | deletedSites | нет |
> | hostingEnvironments | Да |
> | ХостингЭкологи / eventGridFilters | нет |
> | хостингКи / multiRolePools | нет |
> | hostingEnvironments / workerPools | нет |
> | kubeEnvironments | Да |
> | publishingUsers | нет |
> | к просмотру фильмов | нет |
> | resourceHealthMetadata | нет |
> | runtimes | нет |
> | serverFarms | Да |
> | serverFarms / eventGridFilters | нет |
> | sites | Да |
> | сайты/конфиг  | нет |
> | сайты / eventGridFilters | нет |
> | сайты / hostNameBindings | нет |
> | сайты / networkConfig | нет |
> | сайты / Премьераддоны | Да |
> | сайты / слоты | Да |
> | сайты / слоты / eventGridFilters | нет |
> | сайты / слоты / hostNameBindings | нет |
> | сайты / слоты / networkConfig | нет |
> | sourceControls | нет |
> | статическиеСайти | Да |
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
