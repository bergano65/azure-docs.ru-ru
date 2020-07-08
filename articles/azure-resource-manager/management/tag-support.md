---
title: Поддержка тегов для ресурсов
description: В этой статье описываются типы ресурсов Azure, поддерживающие теги, а также приводятся сведения о всех службах Azure.
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: f59e50e3eadb50db97756aa990f4de822a8ba089
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86026378"
---
# <a name="tag-support-for-azure-resources"></a>Поддержка тегов для ресурсов Azure
В этой статье приводятся сведения о поддержке типом ресурса функции [тегов](tag-resources.md). Столбец с подписью **поддерживает теги** . указывает, имеет ли тип ресурса свойство для тега. Столбец с меткой **тег в отчете о затратах** указывает, передает ли этот тип ресурса тег в отчет о затратах. Вы можете просматривать затраты по тегам в [анализе затрат на управление затратами](../../cost-management-billing/costs/group-filter.md) , а также в [счетах на счета и данные о ежедневном использовании в Azure](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

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
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
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
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. Коннектедкаче](#microsoftconnectedcache)
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
> - [Microsoft.DataProtection](#microsoftdataprotection)
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
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
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
> - [Microsoft. Хибриднетворк](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
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
> - [Microsoft. Notebooks](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights).
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft. Сторажерепликатион](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. Вмвареоназуре](#microsoftvmwareonazure)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. Ворклоадбуилдер](#microsoftworkloadbuilder)
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
> | supportProviders | Нет | нет |

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
> | smartDetectorAlertRules | Да | Да |
> | smartGroups | Нет | Нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | servers | Да | Да |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Нет | Нет |
> | служба | Да | Да |
> | validateServiceName | Нет | Нет |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | configurationStores | Да | Да |
> | Конфигуратионсторес/Евентгридфилтерс | Нет | Нет |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Spring | Да | Да |
> | Пружина/приложения | Нет | Нет |
> | Пружина/приложения/развертывания | Нет | Нет |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Да | Да |
> | дефаултпровидерс | Нет | Нет |

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
> | привателинкассоЦиатионс | Нет | Нет |
> | providerOperations | Нет | Нет |
> | ресаурцеманажементпривателинкс | Нет | Нет |
> | roleAssignments | Нет | Нет |
> | ролеассигнментсусажеметрикс | Нет | Нет |
> | roleDefinitions | Нет | Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Да | Да |
> | automationAccounts и конфигурации | Да | Да |
> | automationAccounts и задания | Нет | Нет |
> | automationAccounts/Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | automationAccounts/Приватиндпоинтконнектионс | Нет | Нет |
> | automationAccounts/Привателинкресаурцес | Нет | Нет |
> | automationAccounts / runbooks | Да | Да |
> | automationAccounts/Софтвареупдатеконфигуратионс | Нет | Нет |
> | automationAccounts / webhooks | Нет | Нет |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | приватеклаудс | Да | Да |
> | Приватеклаудс и авторизация | Нет | Нет |
> | Приватеклаудс и кластеры | Нет | Нет |
> | Приватеклаудс/Хкксентерприсеситес | Нет | Нет |

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

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Контроллеры | Да | Да |
> | хибриддатаманажерс | Да | Да |
> | постгресинстанцес | Да | Да |
> | склинстанцес | Да | Да |
> | склманажединстанцес | Да | Да |
> | склсерверинстанцес | Да | Да |
> | склсерверрегистратионс | Да | Да |
> | Склсерверрегистратионс и sqlServer | Нет | Нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | клаудманифестфилес | Нет | Нет |
> | еджесубскриптионс | Да | Да |
> | registrations | Да | Да |
> | регистрации и Кустомерсубскриптионс | Нет | Нет |
> | registrations / products | Нет | Нет |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |

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
> | Биллингаккаунтс/Биллингпрофилес/инструкции | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/счета | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/счета/прайс-лист | Нет | Нет |
> | Биллингаккаунтс/Биллингпрофилес/счета/транзакции | Нет | Нет |
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
> | Биллингаккаунтс/Departments/Биллингпермиссионс | Нет | Нет |
> | Биллингаккаунтс/Departments/Биллингролеассигнментс | Нет | Нет |
> | Биллингаккаунтс/Departments/Биллингроледефинитионс | Нет | Нет |
> | Биллингаккаунтс/Енроллментаккаунтс | Нет | Нет |
> | Биллингаккаунтс/Енроллментаккаунтс/Биллингпермиссионс | Нет | Нет |
> | Биллингаккаунтс/Енроллментаккаунтс/Биллингролеассигнментс | Нет | Нет |
> | Биллингаккаунтс/Енроллментаккаунтс/Биллингроледефинитионс | Нет | Нет |
> | Биллингаккаунтс/счета | Нет | Нет |
> | Биллингаккаунтс/счета/транзакции | Нет | Нет |
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
> | blockchainMembers | Да | Да |
> | кордамемберс | Да | Да |
> | watchers | Да | Да |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | токенсервицес | Да | Да |
> | Токенсервицес/Блоккчаиннетворкс | Нет | Нет |
> | Токенсервицес и группы | Нет | Нет |
> | Токенсервицес/группы/учетные записи | Нет | Нет |
> | Токенсервицес/Токентемплатес | Нет | Нет |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Нет | Нет |
> | Блуепринтассигнментс/Ассигнментоператионс | Нет | Нет |
> | Блуепринтассигнментс/операции | Нет | Нет |
> | blueprints | Нет | Нет |
> | blueprints / artifacts | Нет | Нет |
> | blueprints / versions | Нет | Нет |
> | blueprints / versions / artifacts | Нет | Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | botServices | Да | Да |
> | botServices / channels | Нет | Нет |
> | Ботсервицес и подключения | Нет | Нет |
> | языки | Нет | Нет |
> | шаблоны | Нет | Нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Redis | Да | Да |
> | Redis/Евентгридфилтерс | Нет | Нет |
> | Redis/Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | Redis/Приватиндпоинтконнектионпроксиес/проверка | Нет | Нет |
> | Redis/Приватиндпоинтконнектионс | Нет | Нет |
> | Redis/Привателинкресаурцес | Нет | Нет |
> | редисентерприсе | Да | Да |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Нет | Нет |
> | аутокуотаинкреасе | Нет | Нет |
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
> | resourceProviders | Нет | Нет |
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
> | profiles / endpoints | Да | Да |
> | profiles / endpoints / customdomains | Нет | Нет |
> | profiles / endpoints / origingroups | Нет | Нет |
> | profiles / endpoints / origins | Нет | Нет |
> | validateProbe | Нет | Нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Да | Да |
> | Certificateorder и сертификаты | Нет | Нет |
> | validateCertificateRegistrationInformation | Нет | Нет |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | профиль | Нет | Нет |
> | ресаурцечанжес | Нет | Нет |

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
> | virtualNetworks / virtualNetworkPeerings | Нет | Нет |

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
> | storageAccounts / blobServices | Нет | Нет |
> | storageAccounts / fileServices | Нет | Нет |
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
> | дискакцессес | Да | Да |
> | diskEncryptionSets | Да | Да |
> | disks | Да | Да |
> | galleries | Да | Да |
> | galleries / applications | Нет | Нет |
> | коллекции, приложения и версии | Нет | Нет |
> | galleries / images | Нет | Нет |
> | galleries / images / versions | Нет | Нет |
> | хостграупс | Да | Да |
> | Хостграупс и узлы | Да | Да |
> | images | Да | Да |
> | проксимитиплацементграупс | Да | Да |
> | restorePointCollections | Да | Да |
> | Ресторепоинтколлектионс/Ресторепоинтс | Нет | Нет |
> | шаредвмекстенсионс | Да | Да |
> | Шаредвмекстенсионс и версии | Нет | Нет |
> | sharedVMImages | Да | Да |
> | Шаредвмимажес и версии | Нет | Нет |
> | snapshots | Да | Да |
> | сшпубликкэйс | Да | Да |
> | virtualMachines | Да | Да |
> | virtualMachines и расширения | Да | Да |
> | virtualMachines/metricDefinitions | Нет | Нет |
> | virtualMachines/Рункоммандс | Да | Да |
> | virtualMachineScaleSets | Да | Да |
> | virtualMachineScaleSets и расширения | Нет | Нет |
> | virtualMachineScaleSets/networkInterfaces | Нет | Нет |
> | virtualMachineScaleSets/publicIPAddresses | Нет | Нет |
> | virtualMachineScaleSets/virtualMachines | Нет | Нет |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Нет | Нет |

> [!NOTE]
> Вы не можете добавить тег к виртуальной машине, которая была помечена как обобщенная. Вы помечаете виртуальную машину как обобщенную с помощью командлета [Set-AzVm-обобщенного](/powershell/module/Az.Compute/Set-AzVM) или [AZ VM generalize](/cli/azure/vm#az-vm-generalize).

## <a name="microsoftconnectedcache"></a>Microsoft. Коннектедкаче

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | каченодес | Да | Да |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Нет | Нет |
> | сведения о балансе. | Нет | Нет |
> | сведения о бюджете; | Нет | Нет |
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
> | ресерватионрекоммендатиондетаилс | Нет | Нет |
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
> | serviceAssociationLinks | Нет | нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | registries | Да | Да |
> | реестры и Ажентпулс | Да | Да |
> | registries / builds | нет | Нет |
> | registries / builds / cancel | Нет | Нет |
> | реестры/сборки/Жетлоглинк | Нет | Нет |
> | registries / buildTasks | Да | Да |
> | реестры, Буилдтаскс и шаги | Нет | Нет |
> | реестры и Евентгридфилтерс | Нет | Нет |
> | реестры и Експортпипелинес | Нет | Нет |
> | реестры и Женератекредентиалс | Нет | Нет |
> | реестры и Жетбуилдсаурцеуплоадурл | Нет | Нет |
> | реестры и учетные данные | Нет | Нет |
> | реестры и Импортимаже | Нет | Нет |
> | реестры и Импортпипелинес | Нет | Нет |
> | реестры и Пипелинерунс | Нет | Нет |
> | реестры и Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | реестры/Приватиндпоинтконнектионпроксиес/проверка | Нет | Нет |
> | реестры и Приватиндпоинтконнектионс | Нет | Нет |
> | реестры и Привателинкресаурцес | Нет | Нет |
> | реестры и Куеуебуилд | Нет | Нет |
> | реестры и Реженератекредентиал | Нет | Нет |
> | реестры и Реженератекредентиалс | Нет | Нет |
> | registries / replications | Да | Да |
> | registries / runs | Нет | Нет |
> | registries / runs / cancel | Нет | Нет |
> | реестры и Счедулерун | Нет | Нет |
> | registries / scopeMaps | Нет | Нет |
> | реестры и Таскрунс | Нет | Нет |
> | registries / tasks | Да | Да |
> | registries / tokens | Нет | Нет |
> | реестры и УпдатеполиЦиес | Нет | Нет |
> | registries / webhooks | Да | Да |
> | реестры/веб-перехватчики/Жеткаллбаккконфиг | Нет | Нет |
> | registries / webhooks / ping | Нет | Нет |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | containerServices | Да | Да |
> | managedClusters | Да | Да |
> | openShiftManagedClusters | Да | Да |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Предупреждения | Нет | Нет |
> | BillingAccounts | Нет | Нет |
> | сведения о бюджете; | Нет | Нет |
> | клаудконнекторс | Нет | Нет |
> | Соединители | Да | Да |
> | косталлокатионрулес | Нет | Нет |
> | Departments | Нет | Нет |
> | Измерения | Нет | Нет |
> | EnrollmentAccounts | Нет | Нет |
> | Экспортируемые элементы | Нет | Нет |
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
> | Параметры | Нет | Нет |
> | шовбаккрулес | Нет | Нет |
> | Представления | Нет | Нет |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | requests | Нет | Нет |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | associations | Нет | Нет |
> | resourceProviders | Да | Да |

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
> | рабочие области и Дбворкспацес | Нет | Нет |
> | рабочие области и storageEncryption | Нет | Нет |
> | рабочие области и Виртуалнетворкпирингс | Нет | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | catalogs | Да | Да |
> | datacatalogs | Да | Да |
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
> | factories / integrationRuntimes | Нет | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | accounts / dataLakeStoreAccounts | Нет | Нет |
> | accounts / storageAccounts | Нет | Нет |
> | Accounts/storageAccounts/Containers | Нет | Нет |
> | Accounts/Трансфераналитиксунитс | Нет | Нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | Accounts/Евентгридфилтерс | Нет | Нет |
> | accounts / firewallRules | Нет | Нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | Нет | Нет |
> | services / projects | Нет | Нет |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | баккупваултс | Да | Да |
> | ресаурцеоператионгатекиперс | Да | Да |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | accounts / shares | Нет | Нет |
> | accounts / shares / datasets | Нет | Нет |
> | accounts / shares / invitations | Нет | Нет |
> | accounts / shares / providersharesubscriptions | Нет | Нет |
> | Accounts/shares/Синчронизатионсеттингс | Нет | Нет |
> | accounts / sharesubscriptions | Нет | Нет |
> | Accounts/шаресубскриптионс/Консумерсаурцедатасетс | Нет | Нет |
> | accounts / sharesubscriptions / datasetmappings | Нет | Нет |
> | accounts / sharesubscriptions / triggers | Нет | Нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | servers | Да | Да |
> | servers / advisors | Нет | Нет |
> | servers / keys | Нет | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет | Нет |
> | серверы и Привателинкресаурцес | Нет | Нет |
> | серверы и Куеритекстс | Нет | Нет |
> | серверы и Рековераблесерверс | Нет | Нет |
> | серверы и Топкуеристатистикс | Нет | Нет |
> | servers / virtualNetworkRules | Нет | Нет |
> | серверы и Ваитстатистикс | Нет | Нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | servers | Да | Да |
> | servers / advisors | Нет | Нет |
> | servers / keys | Нет | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет | Нет |
> | серверы и Привателинкресаурцес | Нет | Нет |
> | серверы и Куеритекстс | Нет | Нет |
> | серверы и Рековераблесерверс | Нет | Нет |
> | серверы и Топкуеристатистикс | Нет | Нет |
> | servers / virtualNetworkRules | Нет | Нет |
> | серверы и Ваитстатистикс | Нет | Нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | серверграупс | Да | Да |
> | servers | Да | Да |
> | servers / advisors | Нет | Нет |
> | servers / keys | Нет | Нет |
> | серверы и Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | серверы и Приватиндпоинтконнектионс | Нет | Нет |
> | серверы и Привателинкресаурцес | Нет | Нет |
> | серверы и Куеритекстс | Нет | Нет |
> | серверы и Рековераблесерверс | Нет | Нет |
> | серверы и Топкуеристатистикс | Нет | Нет |
> | servers / virtualNetworkRules | Нет | Нет |
> | серверы и Ваитстатистикс | Нет | Нет |
> | serversv2 | Да | Да |
> | синглесерверс | Да | Да |

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

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Да | Да |
> | applicationgroups / applications | Нет | Нет |
> | applicationgroups / desktops | Нет | Нет |
> | applicationgroups / startmenuitems | Нет | Нет |
> | hostpools | Да | Да |
> | hostpools / sessionhosts | Нет | Нет |
> | hostpools / sessionhosts / usersessions | Нет | Нет |
> | hostpools / usersessions | Нет | Нет |
> | workspaces | Да | Да |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | еластикпулс | Да | Да |
> | Еластикпулс/Иосубтенантс | Да | Да |
> | Еластикпулс/Иосубтенантс/securitySettings | Нет | Нет |
> | IotHubs | Да | Да |
> | IotHubs/Евентгридфилтерс | Нет | Нет |
> | IotHubs/securitySettings | Нет | Нет |
> | ProvisioningServices | Да | Да |
> | usages | Нет | Нет |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | pipelines | Да | Да |

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
> | labs / environments | Да | Да |
> | Labs и Сервицеруннерс | Да | Да |
> | Labs и virtualMachines | Да | Да |
> | schedules | Да | Да |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | дигиталтвинсинстанцес | Да | Да |
> | Дигиталтвинсинстанцес/конечные точки | Нет | Нет |

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
> | domains / topics | Нет | Нет |
> | eventSubscriptions | Нет | Нет |
> | extensionTopics | Нет | Нет |
> | партнернамеспацес | Да | Да |
> | Партнернамеспацес/eventChannels | Нет | Нет |
> | партнеррегистратионс | Да | Да |
> | партнертопикс | Да | Да |
> | Партнертопикс/eventSubscriptions | Нет | Нет |
> | системтопикс | Да | Да |
> | Системтопикс/eventSubscriptions | Нет | Нет |
> | topics | Да | Да |
> | topicTypes | Нет | Нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | пространства имен | Да | Да |
> | namespaces / authorizationrules | Нет | Нет |
> | namespaces / disasterrecoveryconfigs | Нет | Нет |
> | namespaces / eventhubs | Нет | Нет |
> | namespaces / eventhubs / authorizationrules | Нет | Нет |
> | namespaces / eventhubs / consumergroups | Нет | Нет |
> | namespaces / networkrulesets | Нет | Нет |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | експериментворкспацес | Да | Да |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да | Да |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | феатурепровидерс | Нет | Нет |
> | features | Нет | Нет |
> | providers | Нет | Нет |
> | субскриптионфеатуререгистратионс | Нет | Нет |

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
> | аутоманажедаккаунтс | Да | Да |
> | аутоманажедвмконфигуратионпрофилес | Да | Да |
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
> | clusters / applications | Нет | Нет |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | Да | Да |
> | службы/иомтконнекторс | Нет | Нет |
> | службы/иомтконнекторс/подключения | Нет | Нет |
> | службы/иомтконнекторс/сопоставления | Нет | Нет |
> | службы/Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | службы/Приватиндпоинтконнектионс | Да | Да |
> | службы/Привателинкресаурцес | Да | Да |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | machines | Да | Да |
> | machines / extensions | Да | Да |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Диспетчеры | Да | Да |

## <a name="microsofthybridnetwork"></a>Microsoft. Хибриднетворк

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | устройства | Да | Да |
> | регистередсубскриптионс | Нет | Нет |
> | разработчиков | Нет | Нет |
> | поставщики и номера SKU | Нет | Нет |
> | поставщики и внфс | Нет | Нет |
> | виртуалнетворкфунктионскус | Нет | нет |
> | vnfs | Да | Да |

## <a name="microsofthydra"></a>Microsoft.Hydra

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

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | actionGroups | Да | Да |
> | activityLogAlerts | Да | Да |
> | alertrules | Да | Да |
> | autoscalesettings | Да | Да |
> | components | Да | Да |
> | Components/Линкедсторажеаккаунтс | Нет | Нет |
> | Components/Проактиведетектионконфигс | Нет | Нет |
> | diagnosticSettings | Нет | Нет |
> | гуестдиагностиксеттингс | Да | Да |
> | гуестдиагностиксеттингсассоЦиатион | Да | Да |
> | logprofiles | Да | Да |
> | metricAlerts | Да | Да |
> | привателинкскопес | Да | Да |
> | Привателинкскопес/Приватиндпоинтконнектионс | Нет | Нет |
> | Привателинкскопес/Скопедресаурцес | Нет | Нет |
> | куерипаккс | Да | Да |
> | Куерипаккс/запросы | Нет | Нет |
> | счедуледкуерирулес | Да | Да |
> | webtests | Да | Да |
> | workbooks | Да | Да |
> | workbooktemplates | Да | Да |

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
> | График | Да | Да |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Нет | Нет |
> | хсмпулс | Да | Да |
> | манажедхсмс | Да | Да |
> | vaults | Да | Да |
> | хранилища и accessPolicies | Нет | Нет |
> | хранилища и Евентгридфилтерс | Нет | Нет |
> | vaults / secrets | Нет | Нет |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | коннектедклустерс | Да | Да |
> | регистередсубскриптионс | Нет | Нет |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | саурцеконтролконфигуратионс | Нет | Нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | clusters / attacheddatabaseconfigurations | Нет | Нет |
> | clusters / databases | Нет | Нет |
> | clusters / databases / dataconnections | Нет | Нет |
> | clusters / databases / eventhubconnections | Нет | Нет |
> | clusters / databases / principalassignments | Нет | Нет |
> | кластеры и подключения к соединениям | Нет | Нет |
> | clusters / principalassignments | Нет | Нет |
> | кластеры/шаредидентитиес | Нет | Нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | labaccounts | Да | Да |
> | users | Нет | Нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Да | Да |
> | integrationAccounts | Да | Да |
> | integrationServiceEnvironments | Да | Да |
> | integrationServiceEnvironments / managedApis | Нет | Нет |
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
> | workspaces / computes | Нет | Нет |
> | рабочие области и Евентгридфилтерс | Нет | Нет |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | апплюпдатес | Нет | Нет |
> | конфигуратионассигнментс | Нет | Нет |
> | маинтенанцеконфигуратионс | Да | Да |
> | updates | Нет | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Identities | Нет | Нет |
> | userAssignedIdentities | Да | Да |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | манажеднетворкс | Да | Да |
> | Манажеднетворкс/Манажеднетворкграупс | Да | Да |
> | Манажеднетворкс/МанажеднетворкпирингполиЦиес | Да | Да |
> | уведомление | Да | Да |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

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
> | Манажементграупс/параметры | Нет | Нет |
> | ресурсов | Нет | Нет |
> | startTenantBackfill | Нет | Нет |
> | tenantBackfillStatus | Нет | Нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | Accounts/Евентгридфилтерс | Нет | Нет |
> | Accounts/Приватеатласес | Да | Да |

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
> | приватестореклиент | Нет | Нет |
> | приватесторес | Нет | Нет |
> | Приватесторес/предложения | Нет | Нет |
> | products | Нет | Нет |
> | publishers | Нет | Нет |
> | publishers / offers | Нет | Нет |
> | publishers / offers / amendments | Нет | Нет |
> | регистрация | Нет | Нет |

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
> | mediaservices / assets | Нет | Нет |
> | mediaservices/активы/Ассетфилтерс | Нет | Нет |
> | mediaservices/КонтенткэйполиЦиес | Нет | Нет |
> | mediaservices/Евентгридфилтерс | Нет | Нет |
> | mediaservices/Лививентоператионс | Нет | Нет |
> | mediaservices / liveEvents | Да | Да |
> | mediaservices / liveEvents / liveOutputs | Нет | Нет |
> | mediaservices/Лививентс/Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | mediaservices/Ливеаутпутоператионс | Нет | Нет |
> | mediaservices/Медиаграфс | Нет | Нет |
> | mediaservices/Стреаминжендпоинтоператионс | Нет | Нет |
> | mediaservices / streamingEndpoints | Да | Да |
> | mediaservices/Streamingendpoint/Приватиндпоинтконнектионпроксиес | Нет | Нет |
> | mediaservices/Стреаминглокаторс | Нет | Нет |
> | mediaservices/СтреамингполиЦиес | Нет | Нет |
> | mediaservices/Стреамингприватиндпоинтконнектионпроксйоператионс | Нет | Нет |
> | mediaservices / transforms | Нет | Нет |
> | mediaservices / transforms / jobs | Нет | Нет |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

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

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | холографиксброадкастаккаунтс | Да | Да |
> | обжектундерстандингаккаунтс | Да | Да |
> | ремотерендерингаккаунтс | Да | Да |
> | спатиаланчорсаккаунтс | Да | Да |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | нетаппаккаунтс | Да | Нет |
> | Нетаппаккаунтс/Аккаунтбаккупс | Нет | Нет |
> | Нетаппаккаунтс/КапаЦитипулс | Да | Нет |
> | Нетаппаккаунтс/КапаЦитипулс/тома | Да | Нет |
> | Нетаппаккаунтс/КапаЦитипулс/тома/моментальные снимки | Нет | Нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Да | Да |
> | аппликатионгатевайвебаппликатионфиреваллполиЦиес | Да | Да |
> | applicationSecurityGroups | Да | Да |
> | azureFirewallFqdnTags | Нет | Нет |
> | azureFirewalls | Да | Нет |
> | bastionHosts | Да | Нет |
> | bgpServiceCommunities | Нет | Нет |
> | connections | Да | Да |
> | ddosCustomPolicies | Да | Да |
> | ddosProtectionPlans | Да | Да |
> | dnsOperationStatuses | Нет | Нет |
> | dnszones | Да | Да |
> | днсзонес/A | Нет | Нет |
> | днсзонес/AAAA | Нет | Нет |
> | dnszones / all | Нет | Нет |
> | днсзонес/CAA | Нет | Нет |
> | днсзонес и CNAME | Нет | Нет |
> | днсзонес/MX | Нет | Нет |
> | днсзонес/NS | Нет | Нет |
> | днсзонес/PTR | Нет | Нет |
> | dnszones / recordsets | Нет | Нет |
> | днсзонес/SOA | Нет | Нет |
> | днсзонес/SRV | Нет | Нет |
> | днсзонес/TXT | Нет | Нет |
> | expressRouteCircuits | Да | Да |
> | expressRouteCrossConnections | Да | Да |
> | експрессраутегатевайс | Да | Да |
> | експрессраутепортс | Да | Да |
> | expressRouteServiceProviders | Нет | Нет |
> | firewallPolicies | Да | Да |
> | frontdoors | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | Да |
> | фронтдурвебаппликатионфиреваллманажедрулесетс | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | Нет |
> | frontdoorWebApplicationFirewallPolicies | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | Да |
> | getDnsResourceReference | Нет | Нет |
> | internalNotify | Нет | Нет |
> | loadBalancers | Да | Да |
> | localNetworkGateways | Да | Да |
> | natGateways | Да | Да |
> | networkIntentPolicies | Да | Да |
> | networkInterfaces | Да | Да |
> | networkProfiles | Да | Да |
> | networkSecurityGroups | Да | Да |
> | networkWatchers | Да | Да |
> | Нетворкватчерс/Коннектионмониторс | Да | Нет |
> | Нетворкватчерс/панель | Нет | Нет |
> | Нетворкватчерс/lenses | Да | Нет |
> | Нетворкватчерс/Пингмешес | Да | Нет |
> | p2sVpnGateways | Да | Да |
> | приватеднсоператионстатусес | Нет | Нет |
> | privateDnsZones | Да | Да |
> | Приватеднсзонес/A | Нет | Нет |
> | Приватеднсзонес/AAAA | Нет | Нет |
> | Приватеднсзонес/все | Нет | Нет |
> | Приватеднсзонес и CNAME | Нет | Нет |
> | Приватеднсзонес/MX | Нет | Нет |
> | Приватеднсзонес/PTR | Нет | Нет |
> | Приватеднсзонес/SOA | Нет | Нет |
> | Приватеднсзонес/SRV | Нет | Нет |
> | Приватеднсзонес/TXT | Нет | Нет |
> | privateDnsZones / virtualNetworkLinks | Да | Да |
> | приватиндпоинтс | Да | Да |
> | privateLinkServices | Да | Да |
> | publicIPAddresses | Да | Да |
> | publicIPPrefixes | Да | Да |
> | routeFilters | Да | Да |
> | routeTables | Да | Да |
> | serviceEndpointPolicies | Да | Да |
> | trafficManagerGeographicHierarchies | Нет | нет |
> | trafficmanagerprofiles | Да | Да |
> | trafficmanagerprofiles/heatMaps | Нет | Нет |
> | траффикманажерусерметрикскэйс | Нет | Нет |
> | virtualHubs | Да | Да |
> | virtualNetworkGateways | Да | Да |
> | virtualNetworks | Да | Да |
> | virtualNetworks и подсети | Нет | Нет |
> | virtualNetworkTaps | Да | Да |
> | virtualWans | Да | Нет |
> | vpnGateways | Да | Да |
> | vpnSites | Да | Да |
> | webApplicationFirewallPolicies | Да | Да |

<a id="frontdoor"></a>

> [!NOTE]
> Для службы "Передняя дверца Azure" можно применять теги при создании ресурса, но обновление или добавление тегов в настоящее время не поддерживается.


## <a name="microsoftnotebooks"></a>Microsoft. Notebooks

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | нотебукпроксиес | Нет | Нет |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да | Нет |
> | namespaces / notificationHubs | Да | Нет |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

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
> | делетедворкспацес | Нет | Нет |
> | linkTargets | Нет | Нет |
> | storageInsightConfigs | Нет | Нет |
> | workspaces | Да | Да |
> | рабочие области и экспорты | Нет | Нет |
> | рабочие области и источники данных | Нет | Нет |
> | рабочие области и linkedServices | Нет | Нет |
> | рабочие области и Линкедсторажеаккаунтс | Нет | Нет |
> | workspaces / metadata | Нет | Нет |
> | workspaces / query | Нет | Нет |
> | рабочие области и Скопедпривателинкпроксиес | Нет | Нет |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | managementassociations | Нет | Нет |
> | managementconfigurations | Да | Да |
> | solutions | Да | Да |
> | узел "Представления" | Да | Да |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | легаципирингс | Нет | Нет |
> | пираснс | Нет | Нет |
> | peerings | Да | Да |
> | пирингсервицекаунтриес | Нет | Нет |
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
> | привателинксервицесфорповерби | Да | Да |
> | tenants | Да | Да |
> | Клиенты и рабочие области | Нет | Нет |
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

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Нет | Нет |
> | Провидеррегистратионс/Дефаултроллаутс | Нет | Нет |
> | Провидеррегистратионс/Ресаурцетиперегистратионс | Нет | Нет |
> | rollouts | Да | Да |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Рабочие области | Да | Да |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Нет | Нет |
> | vaults | Да | Да |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | опеншифтклустерс | Да | Да |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да | Да |
> | namespaces / authorizationrules | Нет | Нет |
> | namespaces / hybridconnections | Нет | Нет |
> | namespaces / hybridconnections / authorizationrules | Нет | Нет |
> | пространства имен/Приватиндпоинтконнектионс | Нет | Нет |
> | namespaces / wcfrelays | Нет | Нет |
> | namespaces / wcfrelays / authorizationrules | Нет | Нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Запросы | Да | Да |
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
> | emergingissues | Нет | Нет |
> | события | Нет | Нет |
> | impactedResources | Нет | Нет |
> | метаданные | Нет | Нет |
> | Уведомления | Нет | Нет |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | калкулатетемплатехаш | Нет | Нет |
> | deployments | Да | Нет |
> | deployments / operations | Нет | Нет |
> | deploymentScripts | Да | Да |
> | deploymentScripts и журналы | Нет | Нет |
> | ссылки | Нет | Нет |
> | notifyResourceJobs | Нет | Нет |
> | providers | Нет | Нет |
> | resourceGroups | Да | Нет |
> | subscriptions | Да | Нет |
> | темплатеспекс | Да | Да |
> | Темплатеспекс и версии | Да | Да |
> | tenants | Нет | Нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | веб-масштабированием; | Да | Да |
> | saasresources | Нет | Нет |

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
> | алертссуппрессионрулес | Нет | Нет |
> | allowedConnections | Нет | Нет |
> | applicationWhitelistings | Нет | Нет |
> | ассессментметадата | Нет | Нет |
> | assessments | Нет | Нет |
> | аутодисмиссалертсрулес | Нет | Нет |
> | automations | Да | Да |
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
> | Иотсекуритисолутионс/Иоталертс | Нет | Нет |
> | Иотсекуритисолутионс/Иоталерттипес | Нет | Нет |
> | jitNetworkAccessPolicies | Нет | Нет |
> | политики | Нет | Нет |
> | pricings | Нет | Нет |
> | регулаторикомплианцестандардс | Нет | Нет |
> | Регулаторикомплианцестандардс/Регулаторикомплианцеконтролс | Нет | Нет |
> | Регулаторикомплианцестандардс/Регулаторикомплианцеконтролс/Регулаторикомплианцеассессментс | Нет | Нет |
> | секурескореконтролдефинитионс | Нет | Нет |
> | секурескореконтролс | Нет | Нет |
> | секурескорес | Нет | Нет |
> | Секурескорес/Секурескореконтролс | Нет | Нет |
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

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | aggregations | Нет | Нет |
> | alertRules | Нет | Нет |
> | алертрулетемплатес | Нет | Нет |
> | аутоматионрулес | Нет | Нет |
> | bookmarks | Нет | Нет |
> | cases | Нет | Нет |
> | Подключения к компонентам | Нет | Нет |
> | датаконнекторсчеккрекуирементс | Нет | Нет |
> | Сущности | Нет | Нет |
> | ентитикуериес | Нет | Нет |
> | incidents | Нет | Нет |
> | оффицеконсентс | Нет | Нет |
> | Параметры | Нет | Нет |
> | среатинтеллиженце | Нет | Нет |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | консолесервицес | Нет | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да | Да |
> | namespaces / authorizationrules | Нет | Нет |
> | namespaces / disasterrecoveryconfigs | Нет | Нет |
> | namespaces / eventgridfilters | Нет | Нет |
> | namespaces / networkrulesets | Нет | Нет |
> | namespaces / queues | Нет | Нет |
> | namespaces / queues / authorizationrules | Нет | Нет |
> | namespaces / topics | Нет | Нет |
> | namespaces / topics / authorizationrules | Нет | Нет |
> | namespaces / topics / subscriptions | Нет | Нет |
> | namespaces / topics / subscriptions / rules | Нет | Нет |
> | premiumMessagingRegions | Нет | Нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | веб-масштабированием; | Да | Да |
> | clusters | Да | Да |
> | clusters / applications | Нет | Нет |
> | containerGroups | Да | Да |
> | контаинерграупсетс | Да | Да |
> | edgeclusters | Да | Да |
> | еджеклустерс и приложения | Нет | Нет |
> | managedclusters | Да | Да |
> | манажедклустерс/NodeType | Нет | Нет |
> | networks | Да | Да |
> | secretstores | Да | Да |
> | секретсторес и сертификаты | Нет | Нет |
> | секретсторес/секреты | Нет | Нет |
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
> | providerRegistrations | Нет | Нет |
> | Провидеррегистратионс/Ресаурцетиперегистратионс | Нет | Нет |
> | rollouts | Да | Да |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SignalR | Да | Да |
> | SignalR/Евентгридфилтерс | Нет | Нет |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хибридусебенефитс | Нет | Нет |

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
> | Манажединстанцес/databases/БаккупшорттермретентионполиЦиес | Нет | Нет |
> | Манажединстанцес/базы данных, схемы, таблицы, столбцы и Сенситивитилабелс | Нет | Нет |
> | Манажединстанцес/databases/Вулнерабилитяссессментс | Нет | Нет |
> | Манажединстанцес/базы данных/Вулнерабилитяссессментс/правила и базовые планы | Нет | Нет |
> | Манажединстанцес/Енкриптионпротектор | Нет | Нет |
> | Манажединстанцес и ключи | Нет | Нет |
> | Манажединстанцес/Ресторабледроппеддатабасес/БаккупшорттермретентионполиЦиес | Нет | Нет |
> | Манажединстанцес/Вулнерабилитяссессментс | Нет | Нет |
> | servers | Да | Да |
> | servers / administrators | Нет | Нет |
> | серверы и Коммуникатионлинкс | Нет | Нет |
> | servers / databases | Да (см. [Примечание ниже](#sqlnote)) | Да |
> | серверы и Енкриптионпротектор | Нет | Нет |
> | servers / firewallRules | Нет | Нет |
> | servers / keys | Нет | Нет |
> | серверы и Ресторабледроппеддатабасес | Нет | Нет |
> | servers / serviceobjectives | Нет | Нет |
> | серверы и Тдецертификатес | Нет | Нет |
> | виртуалклустерс | Нет | Нет |

<a id="sqlnote"></a>

> [!NOTE]
> База данных master не поддерживает теги, а другие базы данных, включая базы данных Azure синапсе Analytics, поддерживают теги. Базы данных Azure синапсе Analytics должны находиться в активном состоянии (не приостановлено).

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
> | storageAccounts / blobServices | Нет | Нет |
> | storageAccounts / fileServices | Нет | Нет |
> | storageAccounts/Куеуесервицес | Нет | Нет |
> | storageAccounts и службы | Нет | Нет |
> | storageAccounts/Services/metricDefinitions | Нет | Нет |
> | storageAccounts/Таблесервицес | Нет | Нет |
> | usages | Нет | Нет |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | caches | Да | Да |
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
> | storageSyncServices / syncGroups | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | Нет | Нет |
> | Сторажесинксервицес и рабочие процессы | Нет | Нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да | Да |
> | Сторажесинксервицес/Регистередсерверс | Нет | Нет |
> | storageSyncServices / syncGroups | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | Нет | Нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | Нет | Нет |
> | Сторажесинксервицес и рабочие процессы | Нет | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да | Да |
> | Сторажесинксервицес/Регистередсерверс | Нет | Нет |
> | storageSyncServices / syncGroups | Нет | Нет |
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
> | clusters | Да | Да |
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
> | subscriptions | Нет | Нет |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | привателинкхубс | Да | Да |
> | workspaces | Да | Да |
> | рабочие области и Бигдатапулс | Да | Да |
> | рабочие области и Оператионстатусес | Нет | Нет |
> | рабочие области и Склпулс | Да | Да |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | environments | Да | Нет |
> | environments / accessPolicies | Нет | Нет |
> | environments / eventsources | Да | Нет |
> | environments / referenceDataSets | Да | нет |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | stores | Да | Да |
> | Магазины и accessPolicies | Нет | Нет |
> | stores / services | Нет | Нет |
> | stores / services / tokens | Нет | Нет |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | имажетемплатес | Да | Да |
> | Имажетемплатес/Рунаутпутс | Нет | Нет |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | аркзонес | Да | Да |
> | ресаурцепулс | Да | Да |
> | Серверами vCenter | Да | Да |
> | VirtualMachines | Да | Да |
> | виртуалмачинетемплатес | Да | Да |
> | VirtualNetworks | Да | Да |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | дедикатедклауднодес | Да | Да |
> | дедикатедклаудсервицес | Да | Да |
> | virtualMachines | Да | Да |

## <a name="microsoftvmwareonazure"></a>Microsoft. Вмвареоназуре

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | приватеклаудс | Да | Да |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | устройства | Да | Да |
> | регистередсубскриптионс | Нет | Нет |
> | разработчиков | Нет | Нет |
> | поставщики и номера SKU | Нет | Нет |
> | поставщики и внфс | Нет | Нет |
> | виртуалнетворкфунктионскус | Нет | Нет |
> | vnfs | Да | Да |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | Планы | Да | Да |
> | регистередсубскриптионс | Нет | Нет |

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
> | hostingEnvironments | Да | Да |
> | hostingEnvironments/Евентгридфилтерс | Нет | Нет |
> | hostingEnvironments/Мултиролепулс | Нет | Нет |
> | hostingEnvironments/внешнего размещения | Нет | Нет |
> | кубинвиронментс | Да | Да |
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
> | sites / premieraddons | Да | Да |
> | sites / slots | Да | Да |
> | сайты/слоты/Евентгридфилтерс | Нет | Нет |
> | сайты/слоты/Хостнамебиндингс | Нет | Нет |
> | сайты/слоты/файл networkconfig | Нет | Нет |
> | sourceControls | Нет | Нет |
> | статикситес | Да | Да |
> | validate | Нет | Нет |
> | verifyHostingEnvironmentVnet | Нет | Нет |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Нет | Нет |
> | diagnosticSettingsCategories | Нет | Нет |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | мултиплеактиватионкэйс | Да | Да |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Да | Да |

## <a name="microsoftworkloadbuilder"></a>Microsoft. Ворклоадбуилдер

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | рабочие нагрузки | Да | Да |
> | рабочие нагрузки и экземпляры | Нет | Нет |
> | рабочие нагрузки и версии | Нет | Нет |
> | рабочие нагрузки, версии и артефакты | Нет | Нет |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | components | Нет | Нет |
> | componentsSummary | Нет | Нет |
> | monitorInstances | Нет | Нет |
> | monitorInstancesSummary | Нет | Нет |
> | monitors | нет | Нет |
> | notificationSettings | Нет | Нет |

## <a name="next-steps"></a>Дальнейшие шаги

Сведения о том, как применить теги к ресурсам, см. в статье, посвященной [использованию тегов для организации ресурсов Azure](tag-resources.md).
