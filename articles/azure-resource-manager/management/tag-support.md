---
title: Поддержка тегов для ресурсов
description: В этой статье описываются типы ресурсов Azure, поддерживающие теги, а также приводятся сведения о всех службах Azure.
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: fb81ed303cf27add3a033021f017c03f4b9e9750
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005730"
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
> - [Microsoft. Агфудплатформ](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Управление](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft. Бареметалинфраструктуре](#microsoftbaremetalinfrastructure)
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
> - [Microsoft. Кодеспацес](#microsoftcodespaces)
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
> - [Microsoft. D365CustomerInsights](#microsoftd365customerinsights)
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
> - [Microsoft. Девицеупдате](#microsoftdeviceupdate)
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
> - [Microsoft. ScVmm](#microsoftscvmm)
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
> - [Microsoft. Единственное,](#microsoftsingularity)
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
> | DomainServices/ауконтаинер | нет | нет |

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
> | адвисорскоре | нет | нет |
> | конфигурации | нет | нет |
> | generateRecommendations | нет | нет |
> | метаданные | нет | нет |
> | к просмотру фильмов | нет | нет |
> | suppressions | нет | нет |

## <a name="microsoftagfoodplatform"></a>Microsoft. Агфудплатформ

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | фармбеатс | Да | Да |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | actionRules | Да | Да |
> | оповещения | нет | нет |
> | alertsList | нет | нет |
> | алертсметадата | нет | нет |
> | alertsSummary | нет | нет |
> | alertsSummaryList | нет | нет |
> | smartDetectorAlertRules | Да | Да |
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

> [!NOTE]
> Служба управления API Azure поддерживает создание не более 15 пар "имя-значение" для каждой службы.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | configurationStores | Да | нет |
> | Конфигуратионсторес/Евентгридфилтерс | нет | нет |
> | Конфигуратионсторес/Кэйвалуес | нет | нет |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Spring | Да | Да |
> | Пружина/приложения | нет | нет |
> | Пружина/приложения/развертывания | нет | нет |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Да | Да |
> | дефаултпровидерс | нет | нет |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | акцессревиевсчедуледефинитионс | нет | нет |
> | акцессревиевсчедулесеттингс | нет | нет |
> | classicAdministrators | нет | нет |
> | Псевдонимы | нет | нет |
> | denyAssignments | нет | нет |
> | elevateAccess | нет | нет |
> | финдорфанролеассигнментс | нет | нет |
> | locks | нет | нет |
> | разрешения | нет | нет |
> | policyAssignments | нет | нет |
> | policyDefinitions | нет | нет |
> | полициексемптионс | нет | нет |
> | policySetDefinitions | нет | нет |
> | привателинкассоЦиатионс | нет | нет |
> | providerOperations | нет | нет |
> | ресаурцеманажементпривателинкс | Да | Да |
> | roleAssignments | нет | нет |
> | ролеассигнментсусажеметрикс | нет | нет |
> | roleDefinitions | нет | нет |

## <a name="microsoftautomanage"></a>Microsoft. Управление

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | конфигуратионпрофилеассигнментс | нет | нет |
> | конфигуратионпрофилепреференцес | Да | Да |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Да | Да |
> | automationAccounts и конфигурации | Да | Да |
> | automationAccounts и задания | нет | нет |
> | automationAccounts/Приватиндпоинтконнектионпроксиес | нет | нет |
> | automationAccounts/Приватиндпоинтконнектионс | нет | нет |
> | automationAccounts/Привателинкресаурцес | нет | нет |
> | automationAccounts / runbooks | Да | Да |
> | automationAccounts/Софтвареупдатеконфигуратионс | нет | нет |
> | automationAccounts / webhooks | нет | нет |

> [!NOTE]
> Служба автоматизации Azure поддерживает создание не более 15 пар "имя-значение" тега для каждого ресурса службы автоматизации.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | приватеклаудс | Да | Да |
> | Приватеклаудс и надстройки | нет | нет |
> | Приватеклаудс и авторизация | нет | нет |
> | Приватеклаудс и кластеры | нет | нет |
> | Приватеклаудс/Глобалреачконнектионс | нет | нет |
> | Приватеклаудс/Хкксентерприсеситес | нет | нет |
> | Приватеклаудс/Ворклоаднетворкс | нет | нет |
> | Приватеклаудс/Ворклоаднетворкс/Дхкпконфигуратионс | нет | нет |
> | Приватеклаудс/Ворклоаднетворкс/шлюзы | нет | нет |
> | Приватеклаудс/Ворклоаднетворкс/Портмиррорингпрофилес | нет | нет |
> | Приватеклаудс/Ворклоаднетворкс/сегменты | нет | нет |
> | Приватеклаудс/Ворклоаднетворкс/virtualMachines | нет | нет |
> | Приватеклаудс/Ворклоаднетворкс/Вмграупс | нет | нет |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | environments | нет | нет |
> | среды и учетные записи | нет | нет |
> | среды, учетные записи и пространства имен | нет | нет |
> | среды, учетные записи, пространства имен и конфигурации | нет | нет |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Да | нет |
> | b2ctenants | нет | нет |
> | гуестусажес | Да | Да |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Контроллеры | Да | Да |
> | постгресинстанцес | Да | Да |
> | склманажединстанцес | Да | Да |
> | склсерверинстанцес | Да | Да |
> | склсерверрегистратионс | Да | Да |
> | Склсерверрегистратионс и sqlServer | нет | нет |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | клаудманифестфилес | нет | нет |
> | еджесубскриптионс | Да | Да |
> | линкедсубскриптионс | Да | Да |
> | registrations | Да | Да |
> | регистрации и Кустомерсубскриптионс | нет | нет |
> | registrations / products | нет | нет |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft. Бареметалинфраструктуре

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | бареметалинстанцес | Да | Да |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Да | Да |
> | batchAccounts / certificates | нет | нет |
> | batchAccounts / pools | нет | нет |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | billingAccounts | нет | нет |
> | Биллингаккаунтс и соглашения | нет | нет |
> | Биллингаккаунтс/Биллингпермиссионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингпермиссионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингролеассигнментс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингроледефинитионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Биллингсубскриптионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Креатебиллингролеассигнмент | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/клиенты | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/инструкции | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/счета | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/счета/прайс-лист | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/счета/транзакции | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингпермиссионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингролеассигнментс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингроледефинитионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Биллингсубскриптионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Креатебиллингролеассигнмент | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Инитиатетрансфер | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products/перемещение | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/Products/Упдатеауторенев | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/транзакции | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Инвоицесектионс/передача | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Патчоператионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Пайментмесодс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/политики | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/прайс-лист | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Прицешитдовнлоадоператионс | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Products | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/резервирования | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/транзакции | нет | нет |
> | Биллингаккаунтс/Биллингпрофилес/Валидатедетачпайментмесоделигибилити | нет | нет |
> | Биллингаккаунтс/Биллингролеассигнментс | нет | нет |
> | Биллингаккаунтс/Биллингроледефинитионс | нет | нет |
> | Биллингаккаунтс/Биллингсубскриптионс | нет | нет |
> | Биллингаккаунтс/Биллингсубскриптионс/счета | нет | нет |
> | Биллингаккаунтс/Креатебиллингролеассигнмент | нет | нет |
> | Биллингаккаунтс/Креатеинвоицесектионоператионс | нет | нет |
> | Биллингаккаунтс и клиенты | нет | нет |
> | Биллингаккаунтс/Customers/Биллингпермиссионс | нет | нет |
> | Биллингаккаунтс/Customers/Биллингсубскриптионс | нет | нет |
> | Биллингаккаунтс/Customers/Инитиатетрансфер | нет | нет |
> | Биллингаккаунтс, клиенты и политики | нет | нет |
> | Биллингаккаунтс/Customers/Products | нет | нет |
> | Биллингаккаунтс/Customers/Transactions | нет | нет |
> | Биллингаккаунтс/Customers/Transfers | нет | нет |
> | Биллингаккаунтс и отделы | нет | нет |
> | Биллингаккаунтс/Departments/Биллингпермиссионс | нет | нет |
> | Биллингаккаунтс/Departments/Биллингролеассигнментс | нет | нет |
> | Биллингаккаунтс/Departments/Биллингроледефинитионс | нет | нет |
> | Биллингаккаунтс/Енроллментаккаунтс | нет | нет |
> | Биллингаккаунтс/Енроллментаккаунтс/Биллингпермиссионс | нет | нет |
> | Биллингаккаунтс/Енроллментаккаунтс/Биллингролеассигнментс | нет | нет |
> | Биллингаккаунтс/Енроллментаккаунтс/Биллингроледефинитионс | нет | нет |
> | Биллингаккаунтс/счета | нет | нет |
> | Биллингаккаунтс/счета/транзакции | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионмовеоператионс | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионс | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Биллингсубскриптионс/перемещение | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/повышение прав | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Инитиатетрансфер | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Патчоператионс | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Продуктмовеоператионс | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Products | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/продукты/перемещение | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/Products/Упдатеауторенев | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/транзакции | нет | нет |
> | Биллингаккаунтс/Инвоицесектионс/передача | нет | нет |
> | Биллингаккаунтс/Линеофкредит | нет | нет |
> | Биллингаккаунтс/Патчоператионс | нет | нет |
> | Биллингаккаунтс/Пайментмесодс | нет | нет |
> | Биллингаккаунтс и продукты | нет | нет |
> | Биллингаккаунтс/резервирования | нет | нет |
> | Биллингаккаунтс/транзакции | нет | нет |
> | billingPeriods | нет | нет |
> | биллингпермиссионс | нет | нет |
> | billingProperty | нет | нет |
> | биллингролеассигнментс | нет | нет |
> | биллингроледефинитионс | нет | нет |
> | креатебиллингролеассигнмент | нет | нет |
> | departments | нет | нет |
> | enrollmentAccounts | нет | нет |
> | invoices | нет | нет |
> | transfers | нет | нет |
> | передачи/Акцепттрансфер | нет | нет |
> | передачи/Деклинетрансфер | нет | нет |
> | передачи/значение operationstatus | нет | нет |
> | передачи/Валидатетрансфер | нет | нет |
> | валидатеаддресс | нет | нет |

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
> | blockchainMembers | Да | Да |
> | кордамемберс | Да | Да |
> | watchers | Да | Да |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | токенсервицес | Да | Да |
> | Токенсервицес/Блоккчаиннетворкс | нет | нет |
> | Токенсервицес и группы | нет | нет |
> | Токенсервицес/группы/учетные записи | нет | нет |
> | Токенсервицес/Токентемплатес | нет | нет |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | нет | нет |
> | Блуепринтассигнментс/Ассигнментоператионс | нет | нет |
> | Блуепринтассигнментс/операции | нет | нет |
> | blueprints | нет | нет |
> | blueprints / artifacts | нет | нет |
> | blueprints / versions | нет | нет |
> | blueprints / versions / artifacts | нет | нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | botServices | Да | Да |
> | botServices / channels | нет | нет |
> | Ботсервицес и подключения | нет | нет |
> | языки | нет | нет |
> | шаблоны | нет | нет |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Redis | Да | Да |
> | Redis/Евентгридфилтерс | нет | нет |
> | Redis/Приватиндпоинтконнектионпроксиес | нет | нет |
> | Redis/Приватиндпоинтконнектионпроксиес/проверка | нет | нет |
> | Redis/Приватиндпоинтконнектионс | нет | нет |
> | Redis/Привателинкресаурцес | нет | нет |
> | редисентерприсе | Да | Да |
> | Редисентерприсе/Приватиндпоинтконнектионпроксиес | нет | нет |
> | Редисентерприсе/Приватиндпоинтконнектионпроксиес/проверка | нет | нет |
> | Редисентерприсе/Приватиндпоинтконнектионс | нет | нет |
> | Редисентерприсе/Привателинкресаурцес | нет | нет |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | appliedReservations | нет | нет |
> | аутокуотаинкреасе | нет | нет |
> | калкулатиксчанже | нет | нет |
> | calculatePrice | нет | нет |
> | калкулатепурчасеприце | нет | нет |
> | catalogs | нет | нет |
> | commercialReservationOrders | нет | нет |
> | обмен валюты | нет | нет |
> | овнресерватионс | нет | нет |
> | плацепурчасеордер | нет | нет |
> | reservationOrders | нет | нет |
> | Ресерватионордерс/Калкулатерефунд | нет | нет |
> | Ресерватионордерс/Merge | нет | нет |
> | Ресерватионордерс/резервирования | нет | нет |
> | Ресерватионордерс/резервирования/редакции | нет | нет |
> | Ресерватионордерс/Return | нет | нет |
> | Ресерватионордерс/Split | нет | нет |
> | Ресерватионордерс/swap | нет | нет |
> | reservations | нет | нет |
> | resourceProviders | нет | нет |
> | ресурсов | нет | нет |
> | validateReservationOrder | нет | нет |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | кднвебаппликатионфиреваллманажедрулесетс | нет | нет |
> | кднвебаппликатионфиреваллполиЦиес | Да | Да |
> | edgenodes | нет | нет |
> | профили | Да | Да |
> | profiles / endpoints | Да | Да |
> | profiles / endpoints / customdomains | нет | нет |
> | profiles / endpoints / origingroups | нет | нет |
> | profiles / endpoints / origins | нет | нет |
> | validateProbe | нет | нет |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Да | Да |
> | Certificateorder и сертификаты | нет | нет |
> | validateCertificateRegistrationInformation | нет | нет |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | профиль | нет | нет |
> | ресаурцечанжес | нет | нет |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | capabilities | нет | нет |
> | domainNames | нет | нет |
> | имя_домена/возможности | нет | нет |
> | имя_домена/Интерналлоадбаланцерс | нет | нет |
> | имя_домена/Сервицецертификатес | нет | нет |
> | имя_домена/слоты | нет | нет |
> | Доменные имя, слоты и роли | нет | нет |
> | имя_домена/слоты/роли/metricDefinitions | нет | нет |
> | имя_домена/слоты/роли/метрики | нет | нет |
> | moveSubscriptionResources | нет | нет |
> | operatingSystemFamilies | нет | нет |
> | operatingSystems | нет | нет |
> | quotas | нет | нет |
> | resourceTypes | нет | нет |
> | validateSubscriptionMoveAvailability | нет | нет |
> | virtualMachines | нет | нет |
> | virtualMachines/diagnosticSettings | нет | нет |
> | virtualMachines/metricDefinitions | нет | нет |
> | virtualMachines/метрики | нет | нет |

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
> | Експрессраутекроссконнектионс и пиринг | нет | нет |
> | gatewaySupportedDevices | нет | нет |
> | networkSecurityGroups | нет | нет |
> | quotas | нет | нет |
> | reservedIps | нет | нет |
> | virtualNetworks | нет | нет |
> | virtualNetworks/Ремотевиртуалнетворкпирингпроксиес | нет | нет |
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
> | storageAccounts / blobServices | нет | нет |
> | storageAccounts / fileServices | нет | нет |
> | storageAccounts/metricDefinitions | нет | нет |
> | storageAccounts/метрики | нет | нет |
> | storageAccounts/Куеуесервицес | нет | нет |
> | storageAccounts и службы | нет | нет |
> | storageAccounts/Services/diagnosticSettings | нет | нет |
> | storageAccounts/Services/metricDefinitions | нет | нет |
> | storageAccounts/Services/метрики | нет | нет |
> | storageAccounts/Таблесервицес | нет | нет |
> | storageAccounts/Вмимажес | нет | нет |
> | vmImages | нет | нет |

## <a name="microsoftcodespaces"></a>Microsoft. Кодеспацес

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Планы | Да | нет |
> | регистередсубскриптионс | нет | нет |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | Accounts/Приватиндпоинтконнектионпроксиес | нет | нет |
> | Accounts/Приватиндпоинтконнектионс | нет | нет |
> | Accounts/Привателинкресаурцес | нет | нет |

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
> | клаудсервицес | Да | Да |
> | Клаудсервицес/networkInterfaces | нет | нет |
> | Клаудсервицес/publicIPAddresses | нет | нет |
> | Клаудсервицес/Ролеинстанцес | нет | нет |
> | Клаудсервицес/Ролеинстанцес/networkInterfaces | нет | нет |
> | Клаудсервицес и роли | нет | нет |
> | дискакцессес | Да | Да |
> | diskEncryptionSets | Да | Да |
> | disks | Да | Да |
> | galleries | Да | Да |
> | galleries / applications | нет | нет |
> | коллекции, приложения и версии | нет | нет |
> | galleries / images | нет | нет |
> | galleries / images / versions | нет | нет |
> | хостграупс | Да | Да |
> | Хостграупс и узлы | Да | Да |
> | images | Да | Да |
> | проксимитиплацементграупс | Да | Да |
> | restorePointCollections | Да | Да |
> | Ресторепоинтколлектионс/Ресторепоинтс | нет | нет |
> | шаредвмекстенсионс | Да | Да |
> | Шаредвмекстенсионс и версии | нет | нет |
> | sharedVMImages | Да | Да |
> | Шаредвмимажес и версии | нет | нет |
> | snapshots | Да | Да |
> | сшпубликкэйс | Да | Да |
> | virtualMachines | Да | Да |
> | virtualMachines и расширения | Да | Да |
> | virtualMachines/metricDefinitions | нет | нет |
> | virtualMachines/Рункоммандс | Да | Да |
> | virtualMachineScaleSets | Да | Да |
> | virtualMachineScaleSets и расширения | нет | нет |
> | virtualMachineScaleSets/networkInterfaces | нет | нет |
> | virtualMachineScaleSets/publicIPAddresses | Да | нет |
> | virtualMachineScaleSets/virtualMachines | нет | нет |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | нет | нет |

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
> | AggregatedCost | нет | нет |
> | сведения о балансе. | нет | нет |
> | Бюджеты | нет | нет |
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
> | ресерватионрекоммендатиондетаилс | нет | нет |
> | ReservationRecommendations | нет | нет |
> | ReservationSummaries | нет | нет |
> | ReservationTransactions | нет | Нет |
> | Теги | Нет | нет |
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
> | реестры и Ажентпулс | Да | Да |
> | registries / builds | нет | нет |
> | registries / builds / cancel | нет | нет |
> | реестры/сборки/Жетлоглинк | нет | нет |
> | registries / buildTasks | Да | Да |
> | реестры, Буилдтаскс и шаги | нет | нет |
> | реестры и Евентгридфилтерс | нет | нет |
> | реестры и Експортпипелинес | нет | нет |
> | реестры и Женератекредентиалс | нет | нет |
> | реестры и Жетбуилдсаурцеуплоадурл | нет | нет |
> | реестры и учетные данные | нет | нет |
> | реестры и Импортимаже | нет | нет |
> | реестры и Импортпипелинес | нет | нет |
> | реестры и Пипелинерунс | нет | нет |
> | реестры и Приватиндпоинтконнектионпроксиес | нет | нет |
> | реестры/Приватиндпоинтконнектионпроксиес/проверка | нет | нет |
> | реестры и Приватиндпоинтконнектионс | нет | нет |
> | реестры и Привателинкресаурцес | нет | нет |
> | реестры и Куеуебуилд | нет | нет |
> | реестры и Реженератекредентиал | нет | нет |
> | реестры и Реженератекредентиалс | нет | нет |
> | registries / replications | Да | Да |
> | registries / runs | нет | нет |
> | registries / runs / cancel | нет | нет |
> | реестры и Счедулерун | нет | нет |
> | registries / scopeMaps | нет | нет |
> | реестры и Таскрунс | нет | нет |
> | registries / tasks | Да | Да |
> | registries / tokens | нет | нет |
> | реестры и УпдатеполиЦиес | нет | нет |
> | registries / webhooks | Да | Да |
> | реестры/веб-перехватчики/Жеткаллбаккконфиг | нет | нет |
> | registries / webhooks / ping | нет | нет |

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
> | видны узлы | нет | нет |
> | BillingAccounts | нет | нет |
> | Бюджеты | нет | нет |
> | клаудконнекторс | нет | нет |
> | Соединители | Да | Да |
> | косталлокатионрулес | нет | нет |
> | Departments | нет | нет |
> | Измерения | нет | нет |
> | EnrollmentAccounts | нет | нет |
> | Экспортируемые элементы | нет | нет |
> | екстерналбиллингаккаунтс | нет | нет |
> | Екстерналбиллингаккаунтс и оповещения | нет | нет |
> | Екстерналбиллингаккаунтс и измерения | нет | нет |
> | Екстерналбиллингаккаунтс/прогноз | нет | нет |
> | Екстерналбиллингаккаунтс/запрос | нет | нет |
> | екстерналсубскриптионс | нет | нет |
> | Екстерналсубскриптионс и оповещения | нет | нет |
> | Екстерналсубскриптионс и измерения | нет | нет |
> | Екстерналсубскриптионс/прогноз | нет | нет |
> | Екстерналсубскриптионс/запрос | нет | нет |
> | Прогноз | нет | нет |
> | Аналитика | нет | нет |
> | Запрос | нет | нет |
> | регистрация | нет | нет |
> | Reportconfigs | нет | нет |
> | Отчеты | нет | нет |
> | Параметры | нет | нет |
> | шовбаккрулес | нет | нет |
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
> | associations | нет | нет |
> | resourceProviders | Да | Да |

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | instances | Да | Да |

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
> | workspaces | Да | Да |
> | рабочие области и Дбворкспацес | нет | нет |
> | рабочие области и Виртуалнетворкпирингс | нет | нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | catalogs | Да | Да |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | dataFactories | Да | Да |
> | Коэффициенты и diagnosticSettings | нет | нет |
> | Коэффициенты и metricDefinitions | нет | нет |
> | dataFactorySchema | нет | нет |
> | factories | Да | Да |
> | factories / integrationRuntimes | нет | нет |

> [!NOTE]
> Если в фабрике данных есть среды выполнения интеграции Azure SSIS, то их стоимость работы будет помечена тегами фабрики данных. Запуск среды выполнения интеграции Azure SSIS должен быть остановлен и перезапущен для применения новых тегов фабрики данных к их затратам на выполнение.

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | accounts / dataLakeStoreAccounts | нет | нет |
> | accounts / storageAccounts | нет | нет |
> | Accounts/storageAccounts/Containers | нет | нет |
> | Accounts/Трансфераналитиксунитс | нет | нет |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | Accounts/Евентгридфилтерс | нет | нет |
> | accounts / firewallRules | нет | нет |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | нет | нет |
> | services / projects | нет | нет |

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
> | accounts / shares | нет | нет |
> | accounts / shares / datasets | нет | нет |
> | accounts / shares / invitations | нет | нет |
> | accounts / shares / providersharesubscriptions | нет | нет |
> | Accounts/shares/Синчронизатионсеттингс | нет | нет |
> | accounts / sharesubscriptions | нет | нет |
> | Accounts/шаресубскриптионс/Консумерсаурцедатасетс | нет | нет |
> | accounts / sharesubscriptions / datasetmappings | нет | нет |
> | accounts / sharesubscriptions / triggers | нет | нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | servers | Да | Да |
> | servers / advisors | нет | нет |
> | servers / keys | нет | нет |
> | серверы и Приватиндпоинтконнектионпроксиес | нет | нет |
> | серверы и Приватиндпоинтконнектионс | нет | нет |
> | серверы и Привателинкресаурцес | нет | нет |
> | серверы и Куеритекстс | нет | нет |
> | серверы и Рековераблесерверс | нет | нет |
> | серверы и запуск | нет | нет |
> | серверы/прерывать | нет | нет |
> | серверы и Топкуеристатистикс | нет | нет |
> | servers / virtualNetworkRules | нет | нет |
> | серверы и Ваитстатистикс | нет | нет |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | флексиблесерверс | Да | Да |
> | servers | Да | Да |
> | servers / advisors | нет | нет |
> | servers / keys | нет | нет |
> | серверы и Приватиндпоинтконнектионпроксиес | нет | нет |
> | серверы и Приватиндпоинтконнектионс | нет | нет |
> | серверы и Привателинкресаурцес | нет | нет |
> | серверы и Куеритекстс | нет | нет |
> | серверы и Рековераблесерверс | нет | нет |
> | серверы и запуск | нет | нет |
> | серверы/прерывать | нет | нет |
> | серверы и Топкуеристатистикс | нет | нет |
> | серверы и обновление | нет | нет |
> | servers / virtualNetworkRules | нет | нет |
> | серверы и Ваитстатистикс | нет | нет |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | флексиблесерверс | Да | Да |
> | серверграупс | Да | Да |
> | servers | Да | Да |
> | servers / advisors | нет | нет |
> | servers / keys | нет | нет |
> | серверы и Приватиндпоинтконнектионпроксиес | нет | нет |
> | серверы и Приватиндпоинтконнектионс | нет | нет |
> | серверы и Привателинкресаурцес | нет | нет |
> | серверы и Куеритекстс | нет | нет |
> | серверы и Рековераблесерверс | нет | нет |
> | серверы и Топкуеристатистикс | нет | нет |
> | servers / virtualNetworkRules | нет | нет |
> | серверы и Ваитстатистикс | нет | нет |
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

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Да | Да |
> | applicationgroups / applications | нет | нет |
> | applicationgroups / desktops | нет | нет |
> | applicationgroups / startmenuitems | нет | нет |
> | hostpools | Да | Да |
> | хостпулс/мсикспаккажес | нет | нет |
> | hostpools / sessionhosts | нет | нет |
> | hostpools / sessionhosts / usersessions | нет | нет |
> | hostpools / usersessions | нет | нет |
> | workspaces | Да | Да |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | еластикпулс | Да | Да |
> | Еластикпулс/Иосубтенантс | Да | Да |
> | Еластикпулс/Иосубтенантс/securitySettings | нет | нет |
> | IotHubs | Да | Да |
> | IotHubs/Евентгридфилтерс | нет | нет |
> | IotHubs/securitySettings | нет | нет |
> | ProvisioningServices | Да | Да |
> | usages | нет | нет |

## <a name="microsoftdeviceupdate"></a>Microsoft. Девицеупдате

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | учетные записи и экземпляры | Да | Да |

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
> | Дигиталтвинсинстанцес/конечные точки | нет | нет |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | нет | нет |
> | databaseAccounts | Да | Да |
> | ресторабледатабасеаккаунтс | нет | нет |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | domains | Да | Да |
> | домены/Домаиновнершипидентифиерс | нет | нет |
> | generateSsoRequest | нет | нет |
> | topLevelDomains | нет | нет |
> | validateDomainRegistrationInformation | нет | нет |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | lcsprojects | нет | нет |
> | лкспрожектс/клауддеплойментс | нет | нет |
> | лкспрожектс и соединители | нет | нет |

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
> | domains / topics | нет | нет |
> | eventSubscriptions | нет | нет |
> | extensionTopics | нет | нет |
> | партнернамеспацес | Да | Да |
> | Партнернамеспацес/eventChannels | нет | нет |
> | партнеррегистратионс | Да | Да |
> | партнертопикс | Да | Да |
> | Партнертопикс/eventSubscriptions | нет | нет |
> | системтопикс | Да | Да |
> | Системтопикс/eventSubscriptions | нет | нет |
> | topics | Да | Да |
> | topicTypes | нет | нет |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | пространства имен | Да | Да |
> | namespaces / authorizationrules | нет | нет |
> | namespaces / disasterrecoveryconfigs | нет | нет |
> | namespaces / eventhubs | нет | нет |
> | namespaces / eventhubs / authorizationrules | нет | нет |
> | namespaces / eventhubs / consumergroups | нет | нет |
> | namespaces / networkrulesets | нет | нет |
> | пространства имен/Приватиндпоинтконнектионс | нет | нет |

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
> | феатурепровидерс | нет | нет |
> | features | нет | нет |
> | providers | нет | нет |
> | субскриптионфеатуререгистратионс | нет | нет |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | enroll | нет | нет |
> | galleryitems | нет | нет |
> | generateartifactaccessuri | нет | нет |
> | myareas | нет | нет |
> | мяреас/области | нет | нет |
> | мяреас/области/области | нет | нет |
> | мяреас/области/области/галлеритемс | нет | нет |
> | мяреас/области/галлеритемс | нет | нет |
> | мяреас/галлеритемс | нет | нет |
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
> | аутоманажедаккаунтс | Да | Да |
> | аутоманажедвмконфигуратионпрофилес | Да | Да |
> | конфигуратионпрофилеассигнментс | нет | нет |
> | guestConfigurationAssignments | нет | нет |
> | software | нет | нет |
> | софтвареупдатепрофиле | нет | нет |
> | софтвареупдатес | нет | нет |

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
> | clusters / applications | нет | нет |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | services; | Да | Да |
> | службы/иомтконнекторс | нет | нет |
> | службы/иомтконнекторс/подключения | нет | нет |
> | службы/иомтконнекторс/сопоставления | нет | нет |
> | службы/Приватиндпоинтконнектионпроксиес | нет | нет |
> | службы/Приватиндпоинтконнектионс | нет | нет |
> | службы/Привателинкресаурцес | нет | нет |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | machines | Да | Да |
> | Machines/Ассесспатчес | нет | нет |
> | machines / extensions | Да | Да |
> | Machines/Инсталлпатчес | нет | нет |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Диспетчеры | Да | Да |

## <a name="microsofthybridnetwork"></a>Microsoft. Хибриднетворк

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | устройств | Да | Да |
> | нетворкфунктионс | Да | Да |
> | нетворкфунктионвендорс | нет | нет |
> | регистередсубскриптионс | нет | нет |
> | разработчиков | нет | нет |
> | поставщики и Вендорскус | нет | нет |
> | поставщики/Вендорскус/Превиевсубскриптионс | нет | нет |
> | виртуалнетворкфунктионс | Да | Да |
> | виртуалнетворкфунктионвендорс | нет | нет |

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
> | Components/Линкедсторажеаккаунтс | нет | нет |
> | Components/Проактиведетектионконфигс | нет | нет |
> | diagnosticSettings | нет | нет |
> | гуестдиагностиксеттингс | Да | Да |
> | гуестдиагностиксеттингсассоЦиатион | Да | Да |
> | logprofiles | Да | Да |
> | metricAlerts | Да | Да |
> | привателинкскопес | Да | Да |
> | Привателинкскопес/Приватиндпоинтконнектионс | нет | нет |
> | Привателинкскопес/Скопедресаурцес | нет | нет |
> | куерипаккс | Да | Да |
> | Куерипаккс/запросы | нет | нет |
> | счедуледкуерирулес | Да | Да |
> | webtests | Да | Да |
> | workbooks | Да | Да |
> | workbooktemplates | Да | Да |

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
> | апптемплатес | нет | нет |
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
> | хсмпулс | Да | Да |
> | манажедхсмс | Да | Да |
> | vaults | Да | Да |
> | хранилища и accessPolicies | нет | нет |
> | хранилища и Евентгридфилтерс | нет | нет |
> | хранилища и ключи | нет | нет |
> | хранилища/ключи/версии | нет | нет |
> | vaults / secrets | нет | нет |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | коннектедклустерс | Да | Да |
> | регистередсубскриптионс | нет | нет |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | extensions | нет | нет |
> | саурцеконтролконфигуратионс | нет | нет |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | clusters / attacheddatabaseconfigurations | нет | нет |
> | clusters / databases | нет | нет |
> | clusters / databases / dataconnections | нет | нет |
> | clusters / databases / eventhubconnections | нет | нет |
> | clusters / databases / principalassignments | нет | нет |
> | кластеры и подключения к соединениям | нет | нет |
> | clusters / principalassignments | нет | нет |
> | кластеры/шаредидентитиес | нет | нет |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | labaccounts | Да | нет |
> | users | нет | нет |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Да | Да |
> | integrationAccounts | Да | Да |
> | integrationServiceEnvironments | Да | Да |
> | integrationServiceEnvironments / managedApis | нет | нет |
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
> | рабочие области и Батчендпоинтс | Да | Да |
> | рабочие области, Батчендпоинтси и развертывания | Да | Да |
> | рабочие области и коды | нет | нет |
> | рабочие области, коды и версии | нет | нет |
> | workspaces / computes | нет | нет |
> | рабочие области и хранилища данных | нет | нет |
> | рабочие области и Евентгридфилтерс | нет | нет |
> | рабочие области и задания | нет | нет |
> | рабочие области и Лабелингжобс | нет | нет |
> | рабочие области и linkedServices | нет | нет |
> | рабочие области и модели | нет | нет |
> | рабочие области, модели и версии | нет | нет |
> | рабочие области и Онлининдпоинтс | Да | Да |
> | рабочие области, Онлининдпоинтси и развертывания | Да | Да |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | апплюпдатес | нет | нет |
> | конфигуратионассигнментс | нет | нет |
> | маинтенанцеконфигуратионс | Да | Да |
> | публикмаинтенанцеконфигуратионс | нет | нет |
> | updates | нет | нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Identities | нет | нет |
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
> | маркетплацерегистратиондефинитионс | нет | нет |
> | регистратионассигнментс | нет | нет |
> | регистратиондефинитионс | нет | нет |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | getEntities | нет | нет |
> | managementGroups | нет | нет |
> | Манажементграупс/параметры | нет | нет |
> | ресурсов | нет | нет |
> | startTenantBackfill | нет | нет |
> | tenantBackfillStatus | нет | нет |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | Accounts/Евентгридфилтерс | нет | нет |
> | Accounts/Приватеатласес | Да | Да |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | макк | нет | нет |
> | offers | нет | нет |
> | offerTypes | нет | нет |
> | Оффертипес и издатели | нет | нет |
> | Оффертипес, издатели и предложения | нет | нет |
> | Оффертипес, издатели, предложения и планы | нет | нет |
> | Оффертипес/Publishers/Offers/Plans/Agreement | нет | нет |
> | Оффертипес/Publishers/предложения, планы и конфигурации | нет | нет |
> | Оффертипес/Publishers/Offers/Plans/Configurations/Импортимаже | нет | нет |
> | privategalleryitems | нет | нет |
> | приватестореклиент | нет | нет |
> | приватесторес | нет | нет |
> | Приватесторес/предложения | нет | нет |
> | products | нет | нет |
> | publishers | нет | нет |
> | publishers / offers | нет | нет |
> | publishers / offers / amendments | нет | нет |
> | регистрация | нет | нет |

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
> | mediaservices/Аккаунтфилтерс | нет | нет |
> | mediaservices / assets | нет | нет |
> | mediaservices/активы/Ассетфилтерс | нет | нет |
> | mediaservices/КонтенткэйполиЦиес | нет | нет |
> | mediaservices/Евентгридфилтерс | нет | нет |
> | mediaservices/Лививентоператионс | нет | нет |
> | mediaservices / liveEvents | Да | Да |
> | mediaservices / liveEvents / liveOutputs | нет | нет |
> | mediaservices/Ливеаутпутоператионс | нет | нет |
> | mediaservices/Медиаграфс | нет | нет |
> | mediaservices/Приватиндпоинтконнектионоператионс | нет | нет |
> | mediaservices/Приватиндпоинтконнектионпроксиес | нет | нет |
> | mediaservices/Приватиндпоинтконнектионс | нет | нет |
> | mediaservices/Стреаминжендпоинтоператионс | нет | нет |
> | mediaservices / streamingEndpoints | Да | Да |
> | mediaservices/Стреаминглокаторс | нет | нет |
> | mediaservices/СтреамингполиЦиес | нет | нет |
> | mediaservices / transforms | нет | нет |
> | mediaservices / transforms / jobs | нет | нет |

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
> | нетаппаккаунтс | Да | нет |
> | Нетаппаккаунтс/Аккаунтбаккупс | нет | нет |
> | Нетаппаккаунтс/КапаЦитипулс | Да | нет |
> | Нетаппаккаунтс/КапаЦитипулс/тома | Да | нет |
> | Нетаппаккаунтс/КапаЦитипулс/тома/моментальные снимки | нет | нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Да | Да |
> | аппликатионгатевайвебаппликатионфиреваллполиЦиес | Да | Да |
> | applicationSecurityGroups | Да | Да |
> | azureFirewallFqdnTags | нет | нет |
> | azureFirewalls | Да | нет |
> | bastionHosts | Да | нет |
> | bgpServiceCommunities | нет | нет |
> | connections | Да | Да |
> | ddosCustomPolicies | Да | Да |
> | ddosProtectionPlans | Да | Да |
> | dnsOperationStatuses | нет | нет |
> | dnszones | Да | Да |
> | днсзонес/A | нет | нет |
> | днсзонес/AAAA | нет | нет |
> | dnszones / all | нет | нет |
> | днсзонес/CAA | нет | нет |
> | днсзонес и CNAME | нет | нет |
> | днсзонес/MX | нет | нет |
> | днсзонес/NS | нет | нет |
> | днсзонес/PTR | нет | нет |
> | dnszones / recordsets | нет | нет |
> | днсзонес/SOA | нет | нет |
> | днсзонес/SRV | нет | нет |
> | днсзонес/TXT | нет | нет |
> | expressRouteCircuits | Да | Да |
> | expressRouteCrossConnections | Да | Да |
> | експрессраутегатевайс | Да | Да |
> | експрессраутепортс | Да | Да |
> | expressRouteServiceProviders | нет | нет |
> | firewallPolicies | Да | Да |
> | frontdoors | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | Да |
> | фронтдурвебаппликатионфиреваллманажедрулесетс | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | нет |
> | frontdoorWebApplicationFirewallPolicies | Да, но ограничено (см. [Примечание ниже](#frontdoor)) | Да |
> | getDnsResourceReference | нет | нет |
> | internalNotify | нет | нет |
> | ипграупс | Да | Да |
> | loadBalancers | Да | Да |
> | localNetworkGateways | Да | Да |
> | natGateways | Да | Да |
> | networkIntentPolicies | Да | Да |
> | networkInterfaces | Да | Да |
> | networkProfiles | Да | Да |
> | networkSecurityGroups | Да | Да |
> | networkWatchers | Да | Да |
> | Нетворкватчерс/Коннектионмониторс | Да | нет |
> | Нетворкватчерс/панель | нет | нет |
> | Нетворкватчерс/lenses | Да | нет |
> | Нетворкватчерс/Пингмешес | Да | нет |
> | p2sVpnGateways | Да | Да |
> | приватеднсоператионстатусес | нет | нет |
> | privateDnsZones | Да | Да |
> | Приватеднсзонес/A | нет | нет |
> | Приватеднсзонес/AAAA | нет | нет |
> | Приватеднсзонес/все | нет | нет |
> | Приватеднсзонес и CNAME | нет | нет |
> | Приватеднсзонес/MX | нет | нет |
> | Приватеднсзонес/PTR | нет | нет |
> | Приватеднсзонес/SOA | нет | нет |
> | Приватеднсзонес/SRV | нет | нет |
> | Приватеднсзонес/TXT | нет | нет |
> | privateDnsZones / virtualNetworkLinks | Да | Да |
> | приватиндпоинтс | Да | Да |
> | privateLinkServices | Да | Да |
> | publicIPAddresses | Да | Да |
> | publicIPPrefixes | Да | Да |
> | routeFilters | Да | Да |
> | routeTables | Да | Да |
> | serviceEndpointPolicies | Да | Да |
> | trafficManagerGeographicHierarchies | нет | нет |
> | trafficmanagerprofiles | Да | Да |
> | trafficmanagerprofiles/heatMaps | нет | нет |
> | траффикманажерусерметрикскэйс | нет | нет |
> | virtualHubs | Да | Да |
> | virtualNetworkGateways | Да | Да |
> | virtualNetworks | Да | Да |
> | virtualNetworks и подсети | нет | нет |
> | virtualNetworkTaps | Да | Да |
> | virtualWans | Да | нет |
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
> | нотебукпроксиес | нет | нет |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да | нет |
> | namespaces / notificationHubs | Да | нет |

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
> | мастерситес | Да | Да |
> | серверситес | Да | Да |
> | вмвареситес | Да | Да |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | clusters | Да | Да |
> | делетедворкспацес | нет | нет |
> | linkTargets | нет | нет |
> | storageInsightConfigs | нет | нет |
> | workspaces | Да | Да |
> | рабочие области и экспорты | нет | нет |
> | рабочие области и источники данных | нет | нет |
> | рабочие области и linkedServices | нет | нет |
> | рабочие области и Линкедсторажеаккаунтс | нет | нет |
> | workspaces / metadata | нет | нет |
> | workspaces / query | нет | нет |
> | рабочие области и Скопедпривателинкпроксиес | нет | нет |

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
> | легаципирингс | нет | нет |
> | пираснс | нет | нет |
> | peerings | Да | Да |
> | пирингсервицекаунтриес | нет | нет |
> | пирингсервицепровидерс | нет | нет |
> | пирингсервицес | Да | Да |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | аттестации | нет | нет |
> | policyEvents | нет | нет |
> | полициметадата | нет | нет |
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
> | привателинксервицесфорповерби | Да | Да |
> | tenants | Да | Да |
> | Клиенты и рабочие области | нет | нет |
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
> | делетедаккаунтс | нет | нет |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | нет | нет |
> | Провидеррегистратионс/Дефаултроллаутс | нет | нет |
> | Провидеррегистратионс/Ресаурцетиперегистратионс | нет | нет |
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
> | backupProtectedItems | нет | нет |
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
> | namespaces / authorizationrules | нет | нет |
> | namespaces / hybridconnections | нет | нет |
> | namespaces / hybridconnections / authorizationrules | нет | нет |
> | пространства имен/Приватиндпоинтконнектионс | нет | нет |
> | namespaces / wcfrelays | нет | нет |
> | namespaces / wcfrelays / authorizationrules | нет | нет |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | Запросы | Да | Да |
> | ресаурцечанжедетаилс | нет | нет |
> | ресаурцечанжес | нет | нет |
> | ресурсов | нет | нет |
> | ресаурцешистори | нет | нет |
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
> | калкулатетемплатехаш | нет | нет |
> | deployments | Да | нет |
> | deployments / operations | нет | нет |
> | deploymentScripts | Да | Да |
> | deploymentScripts и журналы | нет | нет |
> | ссылки | нет | нет |
> | notifyResourceJobs | нет | нет |
> | providers | нет | нет |
> | resourceGroups | Да | нет |
> | subscriptions | Да | нет |
> | темплатеспекс | Да | Да |
> | Темплатеспекс и версии | Да | Да |
> | tenants | нет | нет |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | веб-масштабированием; | Да | Да |
> | saasresources | нет | нет |

## <a name="microsoftscvmm"></a>Microsoft. ScVmm

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | облака | Да | Да |
> | VirtualMachines | Да | Да |
> | виртуалмачинетемплатес | Да | Да |
> | VirtualNetworks | Да | Да |
> | вммсерверс | Да | Да |

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
> | адаптивенетворкхарденингс | нет | нет |
> | advancedThreatProtectionSettings | нет | нет |
> | оповещения | нет | нет |
> | алертссуппрессионрулес | нет | нет |
> | allowedConnections | нет | нет |
> | applicationWhitelistings | нет | нет |
> | ассессментметадата | нет | нет |
> | assessments | нет | нет |
> | аутодисмиссалертсрулес | нет | нет |
> | automations | Да | Да |
> | AutoProvisioningSettings | нет | нет |
> | Compliances | нет | нет |
> | соединители | нет | нет |
> | dataCollectionAgents | нет | нет |
> | девицесекуритиграупс | нет | нет |
> | discoveredSecuritySolutions | нет | нет |
> | externalSecuritySolutions | нет | нет |
> | InformationProtectionPolicies | нет | нет |
> | иотдефендерсеттингс | нет | нет |
> | иотсекуритисолутионс | Да | Да |
> | Иотсекуритисолутионс/Аналитиксмоделс | нет | нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедалертс | нет | нет |
> | Иотсекуритисолутионс/Аналитиксмоделс/Аггрегатедрекоммендатионс | нет | нет |
> | Иотсекуритисолутионс/Иоталертс | нет | нет |
> | Иотсекуритисолутионс/Иоталерттипес | нет | нет |
> | Иотсекуритисолутионс/Иотрекоммендатионс | нет | нет |
> | Иотсекуритисолутионс/Иотрекоммендатионтипес | нет | нет |
> | иотсенсорс | нет | нет |
> | jitNetworkAccessPolicies | нет | нет |
> | житполиЦиес | нет | нет |
> | политики | нет | нет |
> | pricings | нет | нет |
> | регулаторикомплианцестандардс | нет | нет |
> | Регулаторикомплианцестандардс/Регулаторикомплианцеконтролс | нет | нет |
> | Регулаторикомплианцестандардс/Регулаторикомплианцеконтролс/Регулаторикомплианцеассессментс | нет | нет |
> | секурескореконтролдефинитионс | нет | нет |
> | секурескореконтролс | нет | нет |
> | секурескорес | нет | нет |
> | Секурескорес/Секурескореконтролс | нет | нет |
> | securityContacts | нет | нет |
> | securitySolutions | нет | нет |
> | securitySolutionsReferenceData | нет | нет |
> | securityStatuses | нет | нет |
> | securityStatusesSummaries | нет | нет |
> | сервервулнерабилитяссессментс | нет | нет |
> | Параметры | нет | нет |
> | склвулнерабилитяссессментс | нет | нет |
> | подоценка | нет | нет |
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
> | алертрулетемплатес | нет | нет |
> | аутоматионрулес | нет | нет |
> | bookmarks | нет | нет |
> | cases | нет | нет |
> | Подключения к компонентам | нет | нет |
> | датаконнекторсчеккрекуирементс | нет | нет |
> | Сущности | нет | нет |
> | ентитикуериес | нет | нет |
> | incidents | нет | нет |
> | оффицеконсентс | нет | нет |
> | Параметры | нет | нет |
> | среатинтеллиженце | нет | нет |
> | ватчлистс | нет | нет |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | консолесервицес | нет | нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | пространства имен | Да | Да |
> | namespaces / authorizationrules | нет | нет |
> | namespaces / disasterrecoveryconfigs | нет | нет |
> | namespaces / eventgridfilters | нет | нет |
> | namespaces / networkrulesets | нет | нет |
> | пространства имен/Приватиндпоинтконнектионс | нет | нет |
> | namespaces / queues | нет | нет |
> | namespaces / queues / authorizationrules | нет | нет |
> | namespaces / topics | нет | нет |
> | namespaces / topics / authorizationrules | нет | нет |
> | namespaces / topics / subscriptions | нет | нет |
> | namespaces / topics / subscriptions / rules | нет | нет |
> | premiumMessagingRegions | нет | нет |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | веб-масштабированием; | Да | Да |
> | clusters | Да | Да |
> | clusters / applications | нет | нет |
> | containerGroups | Да | Да |
> | контаинерграупсетс | Да | Да |
> | edgeclusters | Да | Да |
> | еджеклустерс и приложения | нет | нет |
> | managedclusters | Да | Да |
> | манажедклустерс/NodeType | нет | нет |
> | networks | Да | Да |
> | secretstores | Да | Да |
> | секретсторес и сертификаты | нет | нет |
> | секретсторес/секреты | нет | нет |
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
> | Провидеррегистратионс/Ресаурцетиперегистратионс | нет | нет |
> | rollouts | Да | Да |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SignalR | Да | Да |
> | SignalR/Евентгридфилтерс | нет | нет |

## <a name="microsoftsingularity"></a>Microsoft. Единственное,

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | Да |
> | Accounts/АккаунткуотаполиЦиес | нет | нет |
> | Accounts/ГраупполиЦиес | нет | нет |
> | учетные записи и задания | нет | нет |
> | Accounts/Сторажеконтаинерс | нет | нет |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | хибридусебенефитс | нет | нет |

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
> | Манажединстанцес/databases/БаккупшорттермретентионполиЦиес | нет | нет |
> | Манажединстанцес/базы данных, схемы, таблицы, столбцы и Сенситивитилабелс | нет | нет |
> | Манажединстанцес/databases/Вулнерабилитяссессментс | нет | нет |
> | Манажединстанцес/базы данных/Вулнерабилитяссессментс/правила и базовые планы | нет | нет |
> | Манажединстанцес/Енкриптионпротектор | нет | нет |
> | Манажединстанцес и ключи | нет | нет |
> | Манажединстанцес/Ресторабледроппеддатабасес/БаккупшорттермретентионполиЦиес | нет | нет |
> | Манажединстанцес/Вулнерабилитяссессментс | нет | нет |
> | servers | Да | Да |
> | servers / administrators | нет | нет |
> | серверы и Коммуникатионлинкс | нет | нет |
> | servers / databases | Да (см. [Примечание ниже](#sqlnote)) | Да |
> | серверы и Енкриптионпротектор | нет | нет |
> | servers / firewallRules | нет | нет |
> | servers / keys | нет | нет |
> | серверы и Ресторабледроппеддатабасес | нет | нет |
> | servers / serviceobjectives | нет | нет |
> | серверы и Тдецертификатес | нет | нет |
> | виртуалклустерс | нет | нет |

<a id="sqlnote"></a>

> [!NOTE]
> База данных master не поддерживает теги, а другие базы данных, включая базы данных Azure синапсе Analytics, поддерживают теги. Базы данных Azure синапсе Analytics должны находиться в активном состоянии (не приостановлено).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Да | Да |
> | Склвиртуалмачинеграупс/Аваилабилитиграуплистенерс | нет | нет |
> | SqlVirtualMachines | Да | Да |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | делетедаккаунтс | нет | нет |
> | storageAccounts | Да | Да |
> | storageAccounts / blobServices | нет | нет |
> | storageAccounts / fileServices | нет | нет |
> | storageAccounts/Куеуесервицес | нет | нет |
> | storageAccounts и службы | нет | нет |
> | storageAccounts/Services/metricDefinitions | нет | нет |
> | storageAccounts/Таблесервицес | нет | нет |
> | usages | нет | нет |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | caches | Да | Да |
> | кэши/Сторажетаржетс | нет | нет |
> | усажемоделс | нет | нет |

## <a name="microsoftstoragereplication"></a>Microsoft. Сторажерепликатион

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | репликатионграупс | нет | нет |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да | Да |
> | Сторажесинксервицес/Регистередсерверс | нет | нет |
> | storageSyncServices / syncGroups | нет | нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | нет | нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | нет | нет |
> | Сторажесинксервицес и рабочие процессы | нет | нет |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да | Да |
> | Сторажесинксервицес/Регистередсерверс | нет | нет |
> | storageSyncServices / syncGroups | нет | нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | нет | нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | нет | нет |
> | Сторажесинксервицес и рабочие процессы | нет | нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Да | Да |
> | Сторажесинксервицес/Регистередсерверс | нет | нет |
> | storageSyncServices / syncGroups | нет | нет |
> | Сторажесинксервицес/Синкграупс/Клаудендпоинтс | нет | нет |
> | Сторажесинксервицес/Синкграупс/Серверендпоинтс | нет | нет |
> | Сторажесинксервицес и рабочие процессы | нет | нет |

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
> | кластеры/Приватиндпоинтс | нет | нет |
> | streamingjobs | Да (см. примечание ниже) | Да |

> [!NOTE]
> Невозможно добавить тег при выполнении streamingjobs. Остановите ресурс, чтобы добавить тег.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | акцептчанжетенант | нет | нет |
> | псевдонимы | нет | нет |
> | cancel | нет | нет |
> | чанжетенантрекуест | нет | нет |
> | чанжетенантстатус | нет | нет |
> | CreateSubscription | нет | нет |
> | enable | нет | нет |
> | переименовать | нет | нет |
> | SubscriptionDefinitions | нет | нет |
> | SubscriptionOperations | нет | нет |
> | subscriptions | нет | нет |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | привателинкхубс | Да | Да |
> | workspaces | Да | Да |
> | рабочие области и Бигдатапулс | Да | Да |
> | рабочие области и Оператионстатусес | нет | нет |
> | рабочие области и Склдатабасес | Да | Да |
> | рабочие области и Склпулс | Да | Да |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | environments | Да | нет |
> | environments / accessPolicies | нет | нет |
> | environments / eventsources | Да | нет |
> | environments / referenceDataSets | Да | нет |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | stores | Да | Да |
> | Магазины и accessPolicies | нет | нет |
> | stores / services | нет | нет |
> | stores / services / tokens | нет | нет |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | имажетемплатес | Да | Да |
> | Имажетемплатес/Рунаутпутс | нет | нет |

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

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | устройств | Да | Да |
> | регистередсубскриптионс | нет | нет |
> | разработчиков | нет | нет |
> | поставщики и номера SKU | нет | нет |
> | поставщики и внфс | нет | нет |
> | виртуалнетворкфунктионскус | нет | нет |
> | vnfs | Да | Да |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | accounts | Да | нет |
> | Планы | Да | нет |
> | регистередсубскриптионс | нет | нет |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | нет | нет |
> | Апиманажементаккаунтс/списков ACL для | нет | нет |
> | Апиманажементаккаунтс/API | нет | нет |
> | Апиманажементаккаунтс/API/списков ACL для | нет | нет |
> | Апиманажементаккаунтс/API/списков ACL для | нет | нет |
> | Апиманажементаккаунтс/API/подключения | нет | нет |
> | Апиманажементаккаунтс/API/Connections/списков ACL для | нет | нет |
> | Апиманажементаккаунтс/API/Локализеддефинитионс | нет | нет |
> | Апиманажементаккаунтс/списков ACL для | нет | нет |
> | Апиманажементаккаунтс и подключения | нет | нет |
> | billingMeters | нет | нет |
> | certificates | Да | Да |
> | connectionGateways | Да | Да |
> | connections | Да | Да |
> | customApis | Да | Да |
> | deletedSites | нет | нет |
> | hostingEnvironments | Да | Да |
> | hostingEnvironments/Евентгридфилтерс | нет | нет |
> | hostingEnvironments/Мултиролепулс | нет | нет |
> | hostingEnvironments/внешнего размещения | нет | нет |
> | кубинвиронментс | Да | Да |
> | publishingUsers | нет | нет |
> | к просмотру фильмов | нет | нет |
> | resourceHealthMetadata | нет | нет |
> | runtimes | нет | нет |
> | serverFarms | Да | Да |
> | serverFarms/Евентгридфилтерс | нет | нет |
> | serverFarms/Фирстпартяппс | нет | нет |
> | serverFarms/Фирстпартяппс/Кэйваултсеттингс | нет | нет |
> | sites | Да | Да |
> | сайты и конфигурация  | нет | нет |
> | Sites/Евентгридфилтерс | нет | нет |
> | Sites/Хостнамебиндингс | нет | нет |
> | Sites/файл networkconfig | нет | нет |
> | sites / premieraddons | Да | Да |
> | sites / slots | Да | Да |
> | сайты/слоты/Евентгридфилтерс | нет | нет |
> | сайты/слоты/Хостнамебиндингс | нет | нет |
> | сайты/слоты/файл networkconfig | нет | нет |
> | sourceControls | нет | нет |
> | статикситес | Да | Да |
> | validate | нет | нет |
> | verifyHostingEnvironmentVnet | нет | нет |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Тип ресурса | Поддержка тегов | Тег в отчете о затратах |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | нет | нет |
> | diagnosticSettingsCategories | нет | нет |

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
> | рабочие нагрузки и экземпляры | нет | нет |
> | рабочие нагрузки и версии | нет | нет |
> | рабочие нагрузки, версии и артефакты | нет | нет |

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
