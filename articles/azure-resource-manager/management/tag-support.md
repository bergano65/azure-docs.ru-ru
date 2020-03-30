---
title: Поддержка тегов для ресурсов
description: В этой статье описываются типы ресурсов Azure, поддерживающие теги, а также приводятся сведения о всех службах Azure.
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 6100c667c7df0b3e1740777565d260af9fa818a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657579"
---
# <a name="tag-support-for-azure-resources"></a>Поддержка тегов для ресурсов Azure
В этой статье приводятся сведения о поддержке типом ресурса функции [тегов](tag-resources.md). Столбец с **метками поддержки** указывает, есть ли у типа ресурса свойство для тега. Столбец с меткой **Тег в отчете о затратах** указывает, передает ли этот тип ресурса тег отчету о затратах. Вы можете просматривать затраты по тегам в [анализе затрат управления затратами](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) и [счет-фактурой Azure и данными о ежедневном использовании.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

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
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | DomainServices | Да | Да |
> | DomainServices / oucontainer | нет | нет |

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
> | оповещенияMetaData | нет | нет |
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

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | configurationStores | Да | Да |
> | configurationStores / eventGridFilters | нет | нет |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

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
> | dataAliases | нет | нет |
> | denyAssignments | нет | нет |
> | elevateAccess | нет | нет |
> | findOrphanRoleНазначения | нет | нет |
> | locks | нет | нет |
> | разрешения | нет | нет |
> | policyAssignments | нет | нет |
> | policyDefinitions | нет | нет |
> | policySetDefinitions | нет | нет |
> | providerOperations | нет | нет |
> | roleAssignments | нет | нет |
> | рольНазначенияИспользованиеМетри | нет | нет |
> | roleDefinitions | нет | нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Да | Да |
> | автоматизацияСчета / конфигурации | Да | Да |
> | автоматизацияСчета / рабочие места | нет | нет |
> | автоматизацияСчета / privateEndpointConnectionProxies | нет | нет |
> | автоматизацияСчета / privateEndpointConnections | нет | нет |
> | автоматизацияСчета / privateLinkРесурсы | нет | нет |
> | автоматизацияСчета / runbooks | Да | Да |
> | автоматизацияСчета/ softwareUpdateConfigurations | нет | нет |
> | автоматизацияСчета / веб-крючки | нет | нет |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | configurationStores | Да | Да |
> | configurationStores / eventGridFilters | нет | нет |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | environments | нет | нет |
> | среды / счета | нет | нет |
> | среды / учетные записи / пространства имен | нет | нет |
> | среды / учетные записи / именные пространства / конфигурации | нет | нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Да | нет |
> | b2ctenants | нет | нет |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Да | Да |
> | postgresInstances | Да | Да |
> | sqlBigDataКластеры | Да | Да |
> | sqlInstances | Да | Да |
> | sqlServerРегистрации | Да | Да |
> | sqlServerРегистрации / sqlServers | нет | нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | registrations | Да | Да |
> | регистрации / подписка на клиента | нет | нет |
> | регистрации / продукты | нет | нет |
> | verificationKeys | нет | нет |

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
> | счетаСчета / соглашения | нет | нет |
> | БиллингAccounts / биллингРазрешения | нет | нет |
> | биллингAccounts / billingProfiles | нет | нет |
> | биллингAccounts / billingProfiles / billingPermissions | нет | нет |
> | биллингAccounts / billingProfiles / billingRoleНазначения | нет | нет |
> | биллингAccounts / billingProfiles / billingRoleDefinitions | нет | нет |
> | БиллингAccounts / billingProfiles / биллингПодписки | нет | нет |
> | биллингAccounts / billingProfiles / createBillingRoleAssignment | нет | нет |
> | биллингAccounts / billingProfiles / клиенты | нет | нет |
> | биллингAccounts / billingProfiles / инструкции | нет | нет |
> | биллингAccounts / billingProfiles / счета-фактуры | нет | нет |
> | биллингAccounts / billingProfiles / счета/ счета / ценовой лист | нет | нет |
> | счетаСчета / биллингПрофили / счета/ транзакции | нет | нет |
> | биллингAccounts / billingProfiles / накладныеразделы | нет | нет |
> | биллингAccounts / billingProfiles / накладныеразделы / биллингРазрешения | нет | нет |
> | биллингAccounts / billingProfiles / накладныеразделы / биллингРоназначение | нет | нет |
> | биллингAccounts / billingProfiles / накладныеразделы / биллингРолины | нет | нет |
> | биллингAccounts / billingProfiles / накладные разделы / биллингПодписки | нет | нет |
> | биллингAccounts / billingProfiles / накладныеразделы / createBillingRoleAssignment | нет | нет |
> | биллингAccounts / billingProfiles / накладныеРазделы / инициироватьПередача | нет | нет |
> | биллингAccounts / billingProfiles / накладныеразделы / продукты | нет | нет |
> | биллингAccounts / billingProfiles / накладные разделы / продукты / передача | нет | нет |
> | биллингAccounts / billingProfiles / накладные разделы / продукты / обновлениеAutoRenew | нет | нет |
> | биллингAccounts / billingProfiles / накладныеразделы / транзакции | нет | нет |
> | биллингAccounts / billingProfiles / накладныеРазделы / переводы | нет | нет |
> | биллинг-счета / БиллингПрофилс / патч-операции | нет | нет |
> | биллингAccounts / billingProfiles / paymentMethods | нет | нет |
> | биллингAccounts / billingProfiles / политики | нет | нет |
> | биллингAccounts / billingProfiles / цена лист | нет | нет |
> | биллингAccounts / billingProfiles / pricesheetDownloadOperations | нет | нет |
> | биллингAccounts / billingProfiles / продукты | нет | нет |
> | счетаСчета / биллингПрофили / транзакции | нет | нет |
> | биллингAccounts / биллингРоназначения | нет | нет |
> | биллингAccounts / биллингРолетопределения | нет | нет |
> | Биллинг-аккаунты / подписка на счета | нет | нет |
> | счета-счета / биллингПодписки / счета | нет | нет |
> | биллинг-счета / созданиеБиллингРолназначения | нет | нет |
> | биллинг-счета / созданиеInvoiceSectionOperations | нет | нет |
> | счетаСчета / клиенты | нет | нет |
> | биллингAccounts / клиентов / биллингРазрешения | нет | нет |
> | биллинг-аккаунты / клиенты / подписка на счета | нет | нет |
> | биллингСчета / клиенты / инициироватьПеревод | нет | нет |
> | счетаСчета / клиенты / политики | нет | нет |
> | биллингСчета / клиенты / продукты | нет | нет |
> | счетаСчета / клиенты / транзакции | нет | нет |
> | биллингСчета / клиенты / переводы | нет | нет |
> | счетаСчета / отделы | нет | нет |
> | счетаСчета / регистрационные счета | нет | нет |
> | счета-фактуры / счета-фактуры | нет | нет |
> | счетаСчета / накладныеразделы | нет | нет |
> | биллингAccounts / накладныеразделы / биллингSubscriptionMoveOperations | нет | нет |
> | биллинг-счета / накладныеразделы / подписка на счета | нет | нет |
> | биллингAccounts / накладныеразделы / биллингПодписки / передача | нет | нет |
> | биллинг-счета / счета-фактурыРазделы / повышение | нет | нет |
> | биллинг-счета / счета-фактурыРазделы / инициироватьПередача | нет | нет |
> | счетаСчета / накладныеРазделы / патч-операции | нет | нет |
> | биллинг-счета / накладныеразделы / productMoveOperations | нет | нет |
> | счетаСчета / накладныеразделы / продукты | нет | нет |
> | биллингСчета / счета-фактурыРазделы / продукты / передача | нет | нет |
> | биллинг-счета / накладныеразделы / продукты / обновлениеAutoRenew | нет | нет |
> | счетаСчета / накладныеразделы / транзакции | нет | нет |
> | счетаСчета / счетаРаздели / переводы | нет | нет |
> | биллинг-счета / lineOfCredit | нет | нет |
> | счетаСчета / патч-операции | нет | нет |
> | счетаСчета / paymentMethods | нет | нет |
> | счетаСчета / продукты | нет | нет |
> | счетаСчета / транзакции | нет | нет |
> | billingPeriods | нет | нет |
> | БиллингРазрешения | нет | нет |
> | billingProperty | нет | нет |
> | БиллингРоназначения | нет | нет |
> | БиллингРоОпределения | нет | нет |
> | создатьБиллингРоназначение | нет | нет |
> | departments | нет | нет |
> | enrollmentAccounts | нет | нет |
> | invoices | нет | нет |
> | transfers | нет | нет |
> | переводы / приемТрансфер | нет | нет |
> | переводы / отклонениеТрансфер | нет | нет |
> | переводы / операцияСтатус | нет | нет |
> | переводы / проверкаПередача | нет | нет |
> | проверитьАдрес | нет | нет |

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
> | блокчейнЧлены | Да | Да |
> | кордаЧлены | Да | Да |
> | Наблюдателей | Да | Да |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | нет | нет |
> | чертежНазначения / назначенияОперации | нет | нет |
> | чертежНазначения / операции | нет | нет |
> | blueprints | нет | нет |
> | чертежи / артефакты | нет | нет |
> | чертежи / версии | нет | нет |
> | чертежи / версии / артефакты | нет | нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | botServices | Да | Да |
> | botServices / каналы | нет | нет |
> | botServices / соединения | нет | нет |
> | языки | нет | нет |
> | шаблоны | нет | нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Redis | Да | Да |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | appliedReservations | нет | нет |
> | автоквокуамУвеличение | нет | нет |
> | calculateExchange | нет | нет |
> | calculatePrice | нет | нет |
> | вычислитьPurchasePrice | нет | нет |
> | catalogs | нет | нет |
> | commercialReservationOrders | нет | нет |
> | обмен валюты | нет | нет |
> | placePurchaseOrder | нет | нет |
> | reservationOrders | нет | нет |
> | БронированиеЗаказы / вычислитьВозврат | нет | нет |
> | ReservationOrders / слияние | нет | нет |
> | БронированиеЗаказы / бронирование | нет | нет |
> | БронированиеЗаказы / бронирование / пересмотры | нет | нет |
> | БронированиеЗаказы / возвращение | нет | нет |
> | БронированиеЗаказы / раскол | нет | нет |
> | ReservationOrders / своп | нет | нет |
> | reservations | нет | нет |
> | ресурспровайдеры | нет | нет |
> | ресурсов | нет | нет |
> | validateReservationOrder | нет | нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSetSets | нет | нет |
> | CdnWebApplicationFirewallПолитика | Да | Да |
> | edgenodes | нет | нет |
> | профили | Да | Да |
> | профили / конечные точки | Да | Да |
> | профили / конечные точки / пользовательские домемы | нет | нет |
> | профили / конечные точки / происхождение | нет | нет |
> | validateProbe | нет | нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Да | Да |
> | сертификатЗаказы / сертификаты | нет | нет |
> | validateCertificateRegistrationInformation | нет | нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capabilities | нет | нет |
> | domainNames | нет | нет |
> | domainNames / возможности | нет | нет |
> | domainNames / internalLoadBalancers | нет | нет |
> | domainNames / serviceCertificates | нет | нет |
> | domainNames / слоты | нет | нет |
> | domainNames / слоты / роли | нет | нет |
> | domainNames / слоты / роли / метрическиеопределения | нет | нет |
> | domainNames / слоты / роли / метрики | нет | нет |
> | moveSubscriptionResources | нет | нет |
> | operatingSystemFamilies | нет | нет |
> | operatingSystems | нет | нет |
> | quotas | нет | нет |
> | resourceTypes | нет | нет |
> | validateSubscriptionMoveAvailability | нет | нет |
> | virtualMachines | нет | нет |
> | virtualMachines / диагностикаНастройки | нет | нет |
> | virtualMachines / метрическиеопределения | нет | нет |
> | virtualMachines / метрики | нет | нет |

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
> | expressRouteCrossConnections / пиринги | нет | нет |
> | gatewaySupportedDevices | нет | нет |
> | networkSecurityGroups | нет | нет |
> | quotas | нет | нет |
> | reservedIps | нет | нет |
> | virtualNetworks | нет | нет |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | нет | нет |
> | virtualNetworks / virtualNetworkPeerings | нет | нет |

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
> | хранениеСчета / blobServices | нет | нет |
> | учетные данные / fileServices | нет | нет |
> | учетные данные / метрическиеопределения | нет | нет |
> | учетные записи / метрики | нет | нет |
> | учетные записи / очередиСервисы | нет | нет |
> | хранениеСчета / услуги | нет | нет |
> | хранениеСчета / услуги / диагностикаНастройки | нет | нет |
> | хранениеСчета / услуги / метрическиеопределения | нет | нет |
> | хранениеСчета / услуги / метрики | нет | нет |
> | хранениеСчета / таблицыСервисы | нет | нет |
> | storageAccounts / vmImages | нет | нет |
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
> | diskEncryptionSets | Да | Да |
> | disks | Да | Да |
> | galleries | Да | Да |
> | галереи / приложения | нет | нет |
> | галереи / приложения / версии | нет | нет |
> | галереи / изображения | нет | нет |
> | галереи / изображения / версии | нет | нет |
> | хостГрупп | Да | Да |
> | hostGroups / хосты | Да | Да |
> | images | Да | Да |
> | proximityPlacementGroups | Да | Да |
> | restorePointCollections | Да | Да |
> | восстановитьПойнтКоллекции / восстановитьPoints | нет | нет |
> | sharedVMImages | Да | Да |
> | sharedVMImages / версии | нет | нет |
> | snapshots | Да | Да |
> | virtualMachines | Да | Да |
> | virtualMachines / расширения | Да | Да |
> | virtualMachines / метрическиеопределения | нет | нет |
> | virtualMachineScaleSets | Да | Да |
> | virtualMachineScaleSets / расширения | нет | нет |
> | virtualMachineScaleSets / NetworkInterfaces | нет | нет |
> | virtualMachineScaleSets / publicIPAddresses | нет | нет |
> | virtualMachineScaleSets / виртуальныемашины | нет | нет |
> | virtualMachineScaleSets / виртуальныемашины / сетевыеинтерфейсы | нет | нет |

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
> | реестры / сборки | нет | нет |
> | реестры / сборки / отмена | нет | нет |
> | реестры / сборки / getLogLink | нет | нет |
> | реестры / сборкаЗадачи | Да | Да |
> | реестры / сборкаЗадачи / шаги | нет | нет |
> | реестры / eventGridFilters | нет | нет |
> | реестры / generateCredentials | нет | нет |
> | реестры / getBuildSourceUploadUrl | нет | нет |
> | реестры / GetCredentials | нет | нет |
> | реестры / importImage | нет | нет |
> | реестры / privateEndpointConnectionProxies | нет | нет |
> | реестры / privateEndpointConnectionProxies / проверка | нет | нет |
> | реестры / privateEndpointConnections | нет | нет |
> | реестры / privateLinkРесурсы | нет | нет |
> | реестры / очередьСтроительство | нет | нет |
> | реестры / регенерироватьCredential | нет | нет |
> | реестры / регенерироватьCredentials | нет | нет |
> | реестры / репликации | Да | Да |
> | реестры / работает | нет | нет |
> | реестры / работает / отменить | нет | нет |
> | реестры / расписаниеRun | нет | нет |
> | реестры / scopeMaps | нет | нет |
> | реестры / taskRuns | Да | Да |
> | реестры / задачи | Да | Да |
> | реестры / токены | нет | нет |
> | реестры / UpdateПолитика | нет | нет |
> | реестры / веб-крючки | Да | Да |
> | реестры / webhooks / getCallbackConfig | нет | нет |
> | реестры / webhooks / пинг | нет | нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | containerServices | Да | Да |
> | managedClusters | Да | Да |
> | openShiftManagedКластеры | Да | Да |

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
> | Облачные Коннектодоры | нет | нет |
> | Соединители | Да | Да |
> | Departments | нет | нет |
> | Измерения | нет | нет |
> | EnrollmentAccounts | нет | нет |
> | Экспорт | нет | нет |
> | Внешниесчета | нет | нет |
> | Внешниесчета / Оповещения | нет | нет |
> | ВнешниеСчета / Размеры | нет | нет |
> | Внешниесчета / Прогноз | нет | нет |
> | Внешниесчета / Запрос | нет | нет |
> | Внешняя подписка | нет | нет |
> | Внешняя подписка / Оповещения | нет | нет |
> | Внешняя подписка / Размеры | нет | нет |
> | Внешняя подписка / Прогноз | нет | нет |
> | Внешняя подписка / Запрос | нет | нет |
> | Прогноз | нет | нет |
> | Запрос | нет | нет |
> | регистрация | нет | нет |
> | Reportconfigs | нет | нет |
> | Отчеты | нет | нет |
> | Параметры | нет | нет |
> | showbackПравила | нет | нет |
> | Представления | нет | нет |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | requests | нет | нет |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | взаимосвязи | нет | нет |
> | ресурспровайдеры | Да | Да |

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
> | рабочие области / dbWorkspaces | нет | нет |
> | рабочие места / шифрование хранилища | нет | нет |
> | рабочие места / virtualNetworkPeerings | нет | нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | catalogs | Да | Да |
> | каталоги данных | Да | Да |
> | каталоги данных / источники данных | нет | нет |
> | каталоги данных / источники данных / сканирование | нет | нет |
> | каталоги данных / источники данных / сканирование / наборы данных | нет | нет |
> | каталоги данных / источники данных / сканирование / триггеры | нет | нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | dataFactories | Да | нет |
> | dataFactories / diagnosticSettings | нет | нет |
> | dataFactories / метрическиеопределения | нет | нет |
> | dataFactorySchema | нет | нет |
> | factories | Да | нет |
> | фабрики / интеграцияRuntimes | нет | нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | учетные записи / данныеLakeStoreAccounts | нет | нет |
> | счета / учетные записи | нет | нет |
> | счета / хранилищаСчета / контейнеры | нет | нет |
> | счета / transferAnalyticsUnits | нет | нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | счета / eventGridFilters | нет | нет |
> | учетные записи / брандмауэрЫПравила | нет | нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | нет | нет |
> | услуги / проекты | нет | нет |

## <a name="microsoftdatashare"></a>Share Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | счета / акции | нет | нет |
> | счета / акции / наборы данных | нет | нет |
> | счета / акции / приглашения | нет | нет |
> | счета / акции / providersharesubscriptions | нет | нет |
> | счета / акции / синхронизацияНастройки | нет | нет |
> | счета / подписка на акции | нет | нет |
> | учетные записи / подписка на акции / consumerSourceDataSets | нет | нет |
> | учетные записи / подписка на акции / набор данных | нет | нет |
> | счета / подписка на акции / триггеры | нет | нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | servers | Да | Да |
> | серверы / консультанты | нет | нет |
> | серверы / ключи | нет | нет |
> | серверы / privateEndpointConnectionProxies | нет | нет |
> | серверы / privateEndpointConnections | нет | нет |
> | серверы / privateLinkРесурсы | нет | нет |
> | серверы / запросЫТексты | нет | нет |
> | серверы / восстанавливаемыесерверы | нет | нет |
> | серверы / Топ-КуиверСтатистика | нет | нет |
> | серверы / virtualNetworkРулес | нет | нет |
> | серверы / ожиданиеСтатистика | нет | нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | servers | Да | Да |
> | серверы / консультанты | нет | нет |
> | серверы / ключи | нет | нет |
> | серверы / privateEndpointConnectionProxies | нет | нет |
> | серверы / privateEndpointConnections | нет | нет |
> | серверы / privateLinkРесурсы | нет | нет |
> | серверы / запросЫТексты | нет | нет |
> | серверы / восстанавливаемыесерверы | нет | нет |
> | серверы / Топ-КуиверСтатистика | нет | нет |
> | серверы / virtualNetworkРулес | нет | нет |
> | серверы / ожиданиеСтатистика | нет | нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | сервергруппы | Да | Да |
> | servers | Да | Да |
> | серверы / консультанты | нет | нет |
> | серверы / ключи | нет | нет |
> | серверы / privateEndpointConnectionProxies | нет | нет |
> | серверы / privateEndpointConnections | нет | нет |
> | серверы / privateLinkРесурсы | нет | нет |
> | серверы / запросЫТексты | нет | нет |
> | серверы / восстанавливаемыесерверы | нет | нет |
> | серверы / Топ-КуиверСтатистика | нет | нет |
> | серверы / virtualNetworkРулес | нет | нет |
> | серверы / ожиданиеСтатистика | нет | нет |
> | serversv2 | Да | Да |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | артефактИсточники | Да | Да |
> | rollouts | Да | Да |
> | serviceTopologies | Да | Да |
> | serviceTopologies / услуги | Да | Да |
> | serviceTopologies / услуги / сервисИкуты | Да | Да |
> | steps | Да | Да |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | группы заявок | Да | Да |
> | группы приложений / приложения | нет | нет |
> | группы приложений / настольные компьютеры | нет | нет |
> | группы приложений / startmenuitems | нет | нет |
> | хостпулы | Да | Да |
> | hostpools / сессионные хозяева | нет | нет |
> | hostpools / сессионные хосты / usersessions | нет | нет |
> | hostpools / usersessions | нет | нет |
> | workspaces | Да | Да |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Эластичные бассейны | Да | Да |
> | ElasticPools / IotHubTenants | Да | Да |
> | ElasticPools / IotHubTenants / securitySettings | нет | нет |
> | IotHubs | Да | Да |
> | IotHubs / eventGridFilters | нет | нет |
> | IotHubs / безопасностьНастройки | нет | нет |
> | ProvisioningServices | Да | Да |
> | usages | нет | нет |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Трубопроводов | Да | Да |

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
> | лаборатории / среды | Да | Да |
> | лаборатории / serviceRunners | Да | Да |
> | лаборатории / виртуальныемашины | Да | Да |
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
> | домены / domainOwnershipИт | нет | нет |
> | generateSsoRequest | нет | нет |
> | topLevelDomains | нет | нет |
> | validateDomainRegistrationInformation | нет | нет |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | lcsprojects | нет | нет |
> | lcsprojects / облачные развертывания | нет | нет |
> | lcsprojects / разъемы | нет | нет |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | Да | Да |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | domains | Да | Да |
> | домены / темы | нет | нет |
> | eventSubscriptions | нет | нет |
> | extensionTopics | нет | нет |
> | partnerNamespaces | Да | Да |
> | partnerNamespaces / eventChannels | нет | нет |
> | партнеррегистрации | Да | Да |
> | partnerTopics | Да | Да |
> | partnerTopics / EventПодписки | нет | нет |
> | systemTopics | Да | Да |
> | systemTopics / EventПодписки | нет | нет |
> | topics | Да | Да |
> | topicTypes | нет | нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | пространства имен | Да | Да |
> | именные пространства / правила авторизации | нет | нет |
> | namespaces / disasterrecoveryconfigs | нет | нет |
> | именные пространства / eventhubs | нет | нет |
> | namespaces / eventhubs / authorizationrules | нет | нет |
> | именные пространства / eventhubs / группы потребителей | нет | нет |
> | именные пространства / сетевые рулеты | нет | нет |

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
> | myareas / области | нет | нет |
> | myareas / области / области | нет | нет |
> | myareas / области / области / galleryitems | нет | нет |
> | myareas / области / galleryitems | нет | нет |
> | myareas / galleryitems | нет | нет |
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
> | автоуправляемыесчета | Да | Да |
> | autoManagedVmConfigurationProfiles | Да | Да |
> | configurationProfileНазначенияи | нет | нет |
> | guestConfigurationAssignments | нет | нет |
> | software | нет | нет |
> | программное обеспечениеUpdateProfile | нет | нет |
> | softwareUpdates | нет | нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Да | Да |
> | sapMonitors | Да | Да |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | выделенные HSMs | Да | Да |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | кластеры / приложения | нет | нет |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | Да | Да |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Машины | Да | Да |
> | машины / расширения | Да | Да |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | dataManagers | Да | Да |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | components | Да | Да |
> | networkScopes | Да | Да |

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
> | appTemplates | нет | нет |
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
> | hsmPools | Да | Да |
> | vaults | Да | Да |
> | хранилища / accessПолитика | нет | нет |
> | хранилища / eventGridFilters | нет | нет |
> | хранилища / секреты | нет | нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | кластеры / прилагаемыенастройкныенастройки | нет | нет |
> | кластеры / базы данных | нет | нет |
> | кластеры / базы данных / подключения к данным | нет | нет |
> | кластеры / базы данных / eventhubconnections | нет | нет |
> | кластеры / базы данных / основные назначения | нет | нет |
> | кластеры / основные назначения | нет | нет |
> | кластеры / доли | нет | нет |

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
> | интеграцияServiceясреда | Да | Да |
> | integrationServiceEnvironments / managedApis | Да | Да |
> | изолированныеКиэкологи | Да | Да |
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
> | рабочие области / вычисления | нет | нет |
> | рабочие места / eventGridFilters | нет | нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Identities | нет | нет |
> | userAssignedIdentities | Да | Да |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | рынокРегистрацияОпределения | нет | нет |
> | регистрацияНазначения | нет | нет |
> | регистрацияОпределения | нет | нет |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | getEntities | нет | нет |
> | managementGroups | нет | нет |
> | группы управления / настройки | нет | нет |
> | ресурсов | нет | нет |
> | startTenantBackfill | нет | нет |
> | tenantBackfillStatus | нет | нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | счета / eventGridFilters | нет | нет |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | offers | нет | нет |
> | offerTypes | нет | нет |
> | offerTypes / издатели | нет | нет |
> | offerTypes / издатели / предложения | нет | нет |
> | offerTypes / издатели / предложения / планы | нет | нет |
> | offerTypes / издатели / предложения / планы / соглашения | нет | нет |
> | offerTypes / издатели / предложения / планы / конфигурации | нет | нет |
> | offerTypes / издатели / предложения / планы / конфигурации / importImage | нет | нет |
> | privategalleryitems | нет | нет |
> | privateStoreКлиент | нет | нет |
> | products | нет | нет |
> | publishers | нет | нет |
> | издатели / предложения | нет | нет |
> | издатели / предложения / поправки | нет | нет |

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
> | медиасервисы / аккаунтфильтры | нет | нет |
> | медиасервисы / активы | нет | нет |
> | медиасервисы / активы / активыФильтры | нет | нет |
> | медиа-услуги / contentKeyPolicies | нет | нет |
> | медиасервисы / eventGridFilters | нет | нет |
> | медиасервисы / liveEventOperations | нет | нет |
> | медиа-услуги / liveEvents | Да | Да |
> | mediaservices / liveEvents / liveOutputs | нет | нет |
> | медиасервисы / liveOutputOperations | нет | нет |
> | медиа-услуги / mediaGraphs | нет | нет |
> | медиасервисы / streamingEndPointOperations | нет | нет |
> | медиасервисы / streamingEndpoints | Да | Да |
> | медиасервисы / стриминговыелокаторы | нет | нет |
> | медиасервисы / streamingПолитика | нет | нет |
> | медиа-услуги / преобразования | нет | нет |
> | медиа-услуги / преобразования / рабочие места | нет | нет |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | appКластеры | Да | Да |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | оценкаПроекты | Да | Да |
> | migrateprojects | Да | Да |
> | moveСборы | Да | Да |
> | projects | Да | Да |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | голографикаВещательныесчета | Да | Да |
> | объектПониманиеСчетов | Да | Да |
> | remoteRenderingAccounts | Да | Да |
> | пространственныеясчетаСчета | Да | Да |
> | поверхностныеОтчеты | Да | Да |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Да | нет |
> | netAppAccounts / емкостьPools | Да | нет |
> | netAppAccounts / емкостьБассейны / объемы | Да | нет |
> | netAppAccounts / емкостьБассейны / тома / снимки | нет | нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Да | Да |
> | приложениеGatewayWebApplicationFirewallПолитики | Да | Да |
> | applicationSecurityGroups | Да | Да |
> | azureFirewallFqdnTags | нет | нет |
> | azureFirewalls | Да | нет |
> | бастионХомсты | Да | Да |
> | bgpServiceCommunities | нет | нет |
> | connections | Да | Да |
> | ddosCustomPolicies | Да | Да |
> | ddosProtectionPlans | Да | Да |
> | dnsOperationStatuses | нет | нет |
> | dnszones | Да | Да |
> | dnszones / A | нет | нет |
> | dnszones / AAAA | нет | нет |
> | dnszones / все | нет | нет |
> | dnszones / CAA | нет | нет |
> | dnszones / CNAME | нет | нет |
> | dnszones / MX | нет | нет |
> | dnszones / NS | нет | нет |
> | dnszones / PTR | нет | нет |
> | dnszones / рекорды | нет | нет |
> | dnszones / SOA | нет | нет |
> | dnszones / SRV | нет | нет |
> | dnszones / TXT | нет | нет |
> | expressRouteCircuits | Да | Да |
> | expressRouteCrossConnections | Да | Да |
> | expressRouteGateways | Да | Да |
> | expressRoutePorts | Да | Да |
> | expressRouteServiceProviders | нет | нет |
> | брандмауэрПолитика | Да | Да |
> | frontdoors | Да, но ограничено [(см. примечание ниже)](#frontdoor) | Да |
> | frontdoorWebApplicationFirewallManagedRuleSets | Да, но ограничено [(см. примечание ниже)](#frontdoor) | нет |
> | frontdoorWebApplicationFirewallPolicies | Да, но ограничено [(см. примечание ниже)](#frontdoor) | Да |
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
> | networkWatchers / connectionMonitors | Да | нет |
> | networkWatchers / линзы | Да | нет |
> | networkWatchers / pingMeshes | Да | нет |
> | p2sVpnGateways | Да | Да |
> | privateDnsOperationStatuses | нет | нет |
> | privateDnsзоны | Да | Да |
> | privateDnszones / A | нет | нет |
> | privateDnszones / AAAA | нет | нет |
> | privateDnsзоны / все | нет | нет |
> | privateDnsзоны / CNAME | нет | нет |
> | privateDnszones / MX | нет | нет |
> | privateDnszones / PTR | нет | нет |
> | privateDnszones / SOA | нет | нет |
> | privateDnszones / SRV | нет | нет |
> | privateDnszones / TXT | нет | нет |
> | privateDnszones / virtualNetworkLinks | Да | Да |
> | privateEndpoints | Да | Да |
> | privateLinkServices | Да | Да |
> | publicIPAddresses | Да | Да |
> | publicIPPrefixes | Да | Да |
> | routeFilters | Да | Да |
> | routeTables | Да | Да |
> | serviceEndpointPolicies | Да | Да |
> | trafficManagerGeographicHierarchies | нет | нет |
> | trafficmanagerprofiles | Да | Да |
> | trafficmanagerprofiles/heatMaps | нет | нет |
> | trafficManagerUserMetricsKeys | нет | нет |
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
> Для службы Front Door Azure можно применять теги при создании ресурса, но обновление или добавление тегов в настоящее время не поддерживается.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | нет | нет |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да | нет |
> | пространства имен / notificationHubs | Да | нет |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | osNameпространства | Да | Да |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | ГиперВ-сайты | Да | Да |
> | Импортные сайты | Да | Да |
> | Серверсайты | Да | Да |
> | VMwareSites | Да | Да |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | linkTargets | нет | нет |
> | storageInsightConfigs | нет | нет |
> | workspaces | Да | Да |
> | рабочие области / экспорт данных | нет | нет |
> | рабочие области / данныеИсточники | нет | нет |
> | рабочие места / linkedServices | нет | нет |
> | рабочие места / privateEndpointConnectionProxies | нет | нет |
> | рабочие области / privateEndpointConnections | нет | нет |
> | рабочие места / privateLinkРесурсы | нет | нет |
> | рабочие области / запрос | нет | нет |
> | рабочие области / scopedPrivateLinkProxies | нет | нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managementassociations | нет | нет |
> | managementconfigurations | Да | Да |
> | solutions | Да | Да |
> | узел "Представления" | Да | Да |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | нет | нет |
> | peerAsns | нет | нет |
> | вглядывания | Да | Да |
> | peeringServiceCountries | нет | нет |
> | peeringServiceProviders | нет | нет |
> | peeringServices | Да | Да |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | policyEvents | нет | нет |
> | политикаМетадата | нет | нет |
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

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

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
> | именные пространства / правила авторизации | нет | нет |
> | именные пространства / гибридные соединения | нет | нет |
> | namespaces / гибридныесоединения / authorizationrules | нет | нет |
> | именные пространства / wcfrelays | нет | нет |
> | namespaces / wcfrelays / authorizationrules | нет | нет |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | нет | нет |
> | коллекции | Да | Да |
> | коллекции / приложения | нет | нет |
> | коллекции / безопасностьпринципиальны | нет | нет |
> | templateImages | нет | нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Запросы | Да | Да |
> | ресурсИзменениеПодробнее | нет | нет |
> | ресурсИзменения | нет | нет |
> | ресурсов | нет | нет |
> | РесурсыИстория | нет | нет |
> | subscriptionsStatus | нет | нет |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | нет | нет |
> | childAvailabilityStatuses | нет | нет |
> | childResources | нет | нет |
> | emergingissues | нет | нет |
> | события | нет | нет |
> | impactedResources | нет | нет |
> | метаданные | нет | нет |
> | Уведомления | нет | нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | deployments | Да | нет |
> | развертывание / операции | нет | нет |
> | развертываниеScripts | Да | Да |
> | развертываниеСценарии / журналы | нет | нет |
> | ссылки | нет | нет |
> | notifyResourceJobs | нет | нет |
> | providers | нет | нет |
> | resourceGroups | Да | нет |
> | subscriptions | Да | нет |
> | tenants | нет | нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | веб-масштабированием; | Да | Да |
> | saasresources | нет | нет |

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
> | адаптивныеОсетные Хардендинги | нет | нет |
> | advancedThreatProtectionSettings | нет | нет |
> | оповещения | нет | нет |
> | allowedConnections | нет | нет |
> | applicationWhitelistings | нет | нет |
> | оценкаМетадата | нет | нет |
> | оценки | нет | нет |
> | autoDismissAlertsПравила | нет | нет |
> | автоматизация | Да | Да |
> | AutoProvisioningSettings | нет | нет |
> | Compliances | нет | нет |
> | dataCollectionAgents | нет | нет |
> | устройствоSecurityGroups | нет | нет |
> | discoveredSecuritySolutions | нет | нет |
> | externalSecuritySolutions | нет | нет |
> | InformationProtectionPolicies | нет | нет |
> | iotSecuritySolutions | Да | Да |
> | iotSecuritySolutions / AnalyticsModels | нет | нет |
> | iotSecuritySolutions / AnalyticsModels / aggregatedAlerts | нет | нет |
> | iotSecuritySolutions / AnalyticsModels / агрегированныеРекомендации | нет | нет |
> | jitNetworkAccessPolicies | нет | нет |
> | networkData | нет | нет |
> | политики | нет | нет |
> | pricings | нет | нет |
> | нормативныестандарты | нет | нет |
> | regulatoryComplianceStandards / regulatoryComplianceControls | нет | нет |
> | нормативное соответствиеСтандартам / нормативному соответствиюКонтролю / Регулятивные Оценки | нет | нет |
> | securityContacts | нет | нет |
> | securitySolutions | нет | нет |
> | securitySolutionsReferenceData | нет | нет |
> | securityStatuses | нет | нет |
> | securityStatusesSummaries | нет | нет |
> | серверУязвимостьОценки | нет | нет |
> | Параметры | нет | нет |
> | субоценки | нет | нет |
> | задачи | нет | нет |
> | topologies | нет | нет |
> | workspaceSettings | нет | нет |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | нет | нет |
> | diagnosticSettingsCategories | нет | нет |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | aggregations | нет | нет |
> | alertRules | нет | нет |
> | alertRuleTemplates | нет | нет |
> | закладки | нет | нет |
> | cases | нет | нет |
> | DataConnectors | нет | нет |
> | dataConnectorsCheckRequirements | нет | нет |
> | Сущности | нет | нет |
> | сущностьЗапросы | нет | нет |
> | Инцидентов | нет | нет |
> | офисСогласия | нет | нет |
> | Параметры | нет | нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да | нет |
> | именные пространства / правила авторизации | нет | нет |
> | namespaces / disasterrecoveryconfigs | нет | нет |
> | именные пространства / eventgridfilters | нет | нет |
> | именные пространства / сетевые рулеты | нет | нет |
> | именные пространства / очереди | нет | нет |
> | именные пространства / очереди / авторизационные правила | нет | нет |
> | именные пространства / темы | нет | нет |
> | namespaces / темы / authorizationrules | нет | нет |
> | именные пространства / темы / подписки | нет | нет |
> | именные пространства / темы / подписки / правила | нет | нет |
> | premiumMessagingRegions | нет | нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | веб-масштабированием; | Да | Да |
> | clusters | Да | Да |
> | кластеры / приложения | нет | нет |
> | containerGroups | Да | Да |
> | контейнерGroupSets | Да | Да |
> | edgeclusters | Да | Да |
> | краевые кластеры / приложения | нет | нет |
> | managedclusters | Да | Да |
> | управляемыекластеры / узлы | нет | нет |
> | networks | Да | Да |
> | secretstores | Да | Да |
> | тайные магазины / сертификаты | нет | нет |
> | тайные магазины / секреты | нет | нет |
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

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | нет | нет |
> | providerRegistrations / resourceTypeRegistrations | нет | нет |
> | rollouts | Да | Да |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SignalR | Да | Да |
> | SignalR / eventGridFilters | нет | нет |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Да | Да |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | нет | нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Да | Да |
> | веб-масштабированием; | Да | Да |
> | jitRequests | Да | Да |

## <a name="microsoftspoolservice"></a>Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | зарегистрированныеПодписки | нет | нет |
> | Катушки | Да | Да |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managedInstances | Да | Да |
> | управляемыеInstances / базы данных | Да [(см. примечание ниже)](#sqlnote) | Да |
> | managedInstances / базы данных / резервное копированиеShortTermRetentionPolicies | нет | нет |
> | managedInstances / базы данных / схемы / таблицы / столбцы / чувствительностьLabels | нет | нет |
> | managedInstances / базы данных / оценки уязвимости | нет | нет |
> | managedInstances / базы данных / уязвимостьОценки / правила / базовые | нет | нет |
> | управляемыеInstances / шифрованиеПротектор | нет | нет |
> | управляемыеInstances / ключи | нет | нет |
> | управляемыеInstances / restorableDroppedDatabases / резервное копированиеShortTermRetentionПолитика | нет | нет |
> | управляемыеInstances / уязвимостьОценки | нет | нет |
> | servers | Да | Да |
> | серверы / администраторы | нет | нет |
> | серверы / communicationLinks | нет | нет |
> | серверы / базы данных | Да [(см. примечание ниже)](#sqlnote) | Да |
> | серверы / шифрованиеПротектор | нет | нет |
> | серверы / брандмауэрЫПравила | нет | нет |
> | серверы / ключи | нет | нет |
> | серверы / восстановительныеБазы данных | нет | нет |
> | серверы / сервис-цели | нет | нет |
> | серверы / tdeCertificates | нет | нет |
> | виртуальныекластеры | нет | нет |

<a id="sqlnote" />

> [!NOTE]
> База данных Master не поддерживает теги, но другие базы данных, в том числе базы данных хранилища данных Azure SQL, поддерживают их. Базы данных Хранилища данных SQL Azure должны быть в активном состоянии (работа не приостановлена).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Да | Да |
> | SqlVirtualMachineGroups / ДоступностьGroupСлушатели | нет | нет |
> | SqlVirtualMachines | Да | Да |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Да | Да |
> | хранениеСчета / blobServices | нет | нет |
> | учетные данные / fileServices | нет | нет |
> | учетные записи / очередиСервисы | нет | нет |
> | хранениеСчета / услуги | нет | нет |
> | хранениеСчета / услуги / метрическиеопределения | нет | нет |
> | хранениеСчета / таблицыСервисы | нет | нет |
> | usages | нет | нет |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Кэшей | Да | Да |
> | кэши / храненияЦели | нет | нет |
> | usageModels | нет | нет |

## <a name="microsoftstoragereplication"></a>Microsoft.ХранениеРепликация

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | репликацииГруппы | нет | нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да | Да |
> | storageSyncServices / зарегистрированныеСерверы | нет | нет |
> | storageSyncServices / syncGroups | нет | нет |
> | storageSyncServices / syncGroups / cloudEndpoints | нет | нет |
> | storageSyncServices / syncGroups / serverEndpoints | нет | нет |
> | storageSyncServices / рабочие процессы | нет | нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да | Да |
> | storageSyncServices / зарегистрированныеСерверы | нет | нет |
> | storageSyncServices / syncGroups | нет | нет |
> | storageSyncServices / syncGroups / cloudEndpoints | нет | нет |
> | storageSyncServices / syncGroups / serverEndpoints | нет | нет |
> | storageSyncServices / рабочие процессы | нет | нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да | Да |
> | storageSyncServices / зарегистрированныеСерверы | нет | нет |
> | storageSyncServices / syncGroups | нет | нет |
> | storageSyncServices / syncGroups / cloudEndpoints | нет | нет |
> | storageSyncServices / syncGroups / serverEndpoints | нет | нет |
> | storageSyncServices / рабочие процессы | нет | нет |

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
> | среды / доступПолитика | нет | нет |
> | среды / источники событий | Да | нет |
> | среды / ссылкиDataSets | Да | нет |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | посвященныйCloudNodes | Да | Да |
> | посвященныйCloudServices | Да | Да |
> | virtualMachines | Да | Да |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | устройства | Да | Да |
> | зарегистрированныеПодписки | нет | нет |
> | Поставщиков | нет | нет |
> | продавцы / сукус | нет | нет |
> | продавцы / vnfs | нет | нет |
> | vnfs | Да | Да |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | нет | нет |
> | apiManagementAccounts / apiAcls | нет | нет |
> | apiManagementAccounts / apis | нет | нет |
> | apiManagementAccounts / apis / apiAcls | нет | нет |
> | apiManagementAccounts / apis / connectionAcls | нет | нет |
> | apiManagementAccounts / apis / соединения | нет | нет |
> | apiManagementAccounts / apis / соединения / connectionAcls | нет | нет |
> | apiManagementAccounts / apis / локализованныеОпределения | нет | нет |
> | apiManagementAccounts / connectionAcls | нет | нет |
> | apiManagementAccounts / соединения | нет | нет |
> | billingMeters | нет | нет |
> | certificates | Да | Да |
> | connectionGateways | Да | Да |
> | connections | Да | Да |
> | customApis | Да | Да |
> | deletedSites | нет | нет |
> | hostingEnvironments | Да | Да |
> | ХостингЭкологи / eventGridFilters | нет | нет |
> | хостингКи / multiRolePools | нет | нет |
> | hostingEnvironments / workerPools | нет | нет |
> | publishingUsers | нет | нет |
> | к просмотру фильмов | нет | нет |
> | resourceHealthMetadata | нет | нет |
> | runtimes | нет | нет |
> | serverFarms | Да | Да |
> | serverFarms / eventGridFilters | нет | нет |
> | sites | Да | Да |
> | сайты / конфиг  | нет | нет |
> | сайты / eventGridFilters | нет | нет |
> | сайты / hostNameBindings | нет | нет |
> | сайты / networkConfig | нет | нет |
> | сайты / Премьераддоны | Да | Да |
> | сайты / слоты | Да | Да |
> | сайты / слоты / eventGridFilters | нет | нет |
> | сайты / слоты / hostNameBindings | нет | нет |
> | сайты / слоты / networkConfig | нет | нет |
> | sourceControls | нет | нет |
> | статическиеСайти | Да | Да |
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
