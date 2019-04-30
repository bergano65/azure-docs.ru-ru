---
title: Поддержка операции перемещения типом ресурсов Azure
description: Список типов ресурсов Azure, подлежащих перемещению в новую группу ресурсов или подписку.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 03/22/2019
ms.author: tomfitz
ms.openlocfilehash: d44b1bf778c7ec9551e2fd30f67083f8dded22d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729296"
---
# <a name="move-operation-support-for-resources"></a>Поддержка операции перемещения для ресурсов
В этой статье указано, поддерживается ли для тех или иных типов ресурсов Azure операция перемещения. Несмотря на то что тип ресурса поддерживает операцию перемещения, некоторые условия могут препятствовать перемещению ресурса. Дополнительные сведения об условиях, влияющих на операции перемещения, см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| domainservices | Нет | Нет |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| tenants | Нет | Нет |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| серверы | Да | Да |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| служба | Да | Да |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| apiapps | Нет | Нет |
| appidentities | Нет | Нет |
| gateways | Нет | Нет |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| policyassignments | Нет | Нет |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| automationaccounts | Да | Да |
| automationaccounts/configurations | Да | Да |
| automationaccounts/runbooks | Да | Да |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| b2cdirectories | Да | Да |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| registrations | Да | Да |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| backupvault | Нет | Нет |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| batchaccounts | Да | Да |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| clusters | Нет | Нет |
| fileservers | Нет | Нет |
| задания | Нет | Нет |
| workspaces | Нет | Нет |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| mapapis | Нет | Нет |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| Biztalk | Да | Да |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| blockchainmembers | Нет | Нет |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| blueprintassignments | Нет | Нет |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| botservices | Да | Да |

## <a name="microsoftcache"></a>Microsoft.Cache
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| redis | Да | Да |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| профили | Да | Да |
| profiles/endpoints | Да | Да |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| certificateorders | Да | Да |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| domainnames | Да | Нет |
| virtualmachines | Да | Нет |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Нет | Нет |
| reservedips | Нет | Нет |
| virtualnetworks | Нет | Нет |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storageaccounts | Да | Нет |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Да | Да |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| availabilitysets | Да | Да |
| диски | Да | Да |
| galleries | Нет | Нет |
| galleries/images | Нет | Нет |
| galleries/images/versions | Нет | Нет |
| образы | Да | Да |
| proximityplacementgroups | Нет | Нет |
| restorepointcollections | Нет | Нет |
| sharedvmimages | Нет | Нет |
| sharedvmimages/versions | Нет | Нет |
| моментальные снимки | Да | Да |
| virtualmachines | Да | Да |
| virtualmachines/extensions | Да | Да |
| virtualmachinescalesets | Да | Да |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| containergroups | Нет | Нет |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| containergroups | Нет | Нет |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| registries | Да | Да |
| registries/buildtasks | Да | Да |
| registries/replications | Нет | Нет |
| registries/tasks | Да | Да |
| registries/webhooks | Да | Да |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| containerservices | Нет | Нет |
| managedclusters | Нет | Нет |
| openshiftmanagedclusters | Нет | Нет |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| приложений | Да | Да |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Нет | Нет |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| соединители | Да | Да |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hubs | Да | Да |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| задания | Нет | Нет |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Нет | Нет |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| workspaces | Нет | Нет |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| catalogs | Да | Да |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| connectionmanagers | Нет | Нет |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| Пакеты | Нет | Нет |
| Планы | Нет | Нет |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| datafactories | Да | Да |
| factories | Да | Да |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Нет | Нет |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Да | Да |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Да | Да |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| services; | Нет | Нет |
| services/projects | Нет | Нет |
| slots | Нет | Нет |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| серверы | Да | Да |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| серверы | Да | Да |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| servergroups | Нет | Нет |
| серверы | Да | Да |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| artifactsources | Нет | Нет |
| rollouts | Нет | Нет |
| servicetopologies | Нет | Нет |
| servicetopologies/services | Нет | Нет |
| servicetopologies/services/serviceunits | Нет | Нет |
| steps | Нет | Нет |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| elasticpools | Нет | Нет |
| elasticpools/iothubtenants | Нет | Нет |
| iothubs | Да | Да |
| provisioningservices | Да | Да |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| controllers | Нет | Нет |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| labcenters | Нет | Нет |
| labs | Да | Нет |
| labs/servicerunners | Да | Да |
| labs/virtualmachines | Да | Нет |
| schedules | Нет | Нет |

## <a name="microsoftdns"></a>microsoft.dns
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| dnszones | Нет | Нет |
| dnszones/a | Нет | Нет |
| dnszones/aaaa | Нет | Нет |
| dnszones/cname | Нет | Нет |
| dnszones/mx | Нет | Нет |
| dnszones/ptr | Нет | Нет |
| dnszones/srv | Нет | Нет |
| dnszones/txt | Нет | Нет |
| trafficmanagerprofiles | Нет | Нет |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| databaseaccounts | Да | Да |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| domains | Да | Да |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| domains | Да | Да |
| topics | Да | Да |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| clusters | Да | Да |
| пространства имен | Да | Да |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Нет | Нет |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hanainstances | Да | Да |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| clusters | Да | Да |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| datamanagers | Да | Да |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| задания | Да | Да |

## <a name="microsoftinsights"></a>microsoft.insights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Нет | Нет |
| actiongroups | Да | Да |
| activitylogalerts | Нет | Нет |
| alertrules | Да | Да |
| autoscalesettings | Да | Да |
| components | Да | Да |
| guestdiagnosticsettings | Нет | Нет |
| metricalerts | Нет | Нет |
| notificationgroups | Нет | Нет |
| notificationrules | Нет | Нет |
| scheduledqueryrules | Нет | Нет |
| webtests | Да | Да |
| книги | Да | Да |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| iotapps | Да | Да |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| checknameavailability | Да | Да |
| Граф | Да | Да |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hsmpools | Нет | Нет |
| vaults | Да | Да |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| clusters | Да | Да |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| labaccounts | Нет | Нет |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Да | Да |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Да | Да |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hostingenvironments | Нет | Нет |
| integrationaccounts | Да | Да |
| integrationserviceenvironments | Нет | Нет |
| isolatedenvironments | Нет | Нет |
| workflows | Да | Да |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| commitmentplans | Да | Да |
| webservices | Да | Нет |
| workspaces | Да | Да |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Да | Да |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Да | Да |
| accounts/workspaces | Да | Да |
| accounts/workspaces/projects | Да | Да |
| teamaccounts | Да | Да |
| teamaccounts/workspaces | Да | Да |
| teamaccounts/workspaces/projects | Да | Да |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Да | Да |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hostingaccounts | Нет | Нет |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| workspaces | Нет | Нет |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| userassignedidentities | Да | Да |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Да | Да |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| classicdevservices | Нет | Нет |

## <a name="microsoftmedia"></a>Microsoft.Media
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| mediaservices | Да | Да |
| mediaservices/liveevents | Да | Да |
| mediaservices/streamingendpoints | Да | Да |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| assessmentprojects | Нет | Нет |
| migrateprojects | Нет | Нет |
| projects | Нет | Нет |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| netappaccounts | Нет | Нет |
| netappaccounts/capacitypools | Нет | Нет |
| netappaccounts/capacitypools/volumes | Нет | Нет |
| netappaccounts/capacitypools/volumes/mounttargets | Нет | Нет |
| netappaccounts/capacitypools/volumes/snapshots | Нет | Нет |

## <a name="microsoftnetwork"></a>Microsoft.Network.
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| applicationgateways | Нет | Нет |
| applicationsecuritygroups | Да | Да |
| azurefirewalls | Да | Да |
| bastionhosts | Нет | Нет |
| подключения | Да | Да |
| ddoscustompolicies | Да | Да |
| ddosprotectionplans | Нет | Нет |
| dnszones | Да | Да |
| expressroutecircuits | Нет | Нет |
| expressroutecrossconnections | Нет | Нет |
| expressroutegateways | Нет | Нет |
| expressrouteports | Нет | Нет |
| frontdoor | Да | Да |
| frontdoorwebapplicationfirewallpolicies | Да | Да |
| interfaceendpoints | Нет | Нет |
| loadbalancers | Да | Да |
| localnetworkgateways | Да | Да |
| natgateways | Да | Да |
| networkintentpolicies | Да | Да |
| networkinterfaces | Да | Да |
| networkprofiles | Нет | Нет |
| networksecuritygroups | Да | Да |
| networkwatchers | Да | Да |
| networkwatchers/connectionmonitors | Да | Да |
| networkwatchers/lenses | Да | Да |
| networkwatchers/pingmeshes | Да | Да |
| p2svpngateways | Нет | Нет |
| privatelinkservices | Нет | Нет |
| publicipaddresses | Да | Да |
| publicipprefixes | Да | Да |
| routefilters | Нет | Нет |
| routetables | Да | Да |
| securegateways | Нет | Нет |
| serviceendpointpolicies | Да | Да |
| trafficmanagerprofiles | Да | Да |
| virtualhubs | Нет | Нет |
| virtualnetworkgateways | Да | Да |
| virtualnetworks | Да | Да |
| virtualnetworktaps | Нет | Нет |
| virtualwans | Нет | Нет |
| vpngateways | Нет | Нет |
| vpnsites | Да | Да |
| webapplicationfirewallpolicies | Да | Да |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| пространства имен | Да | Да |
| namespaces/notificationhubs | Да | Да |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| workspaces | Да | Да |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| managementconfigurations | Да | Да |
| solutions | Да | Да |
| просмотры | Да | Да |

## <a name="microsoftportal"></a>Microsoft.Portal
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| dashboards | Да | Да |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| rootresources | Нет | Нет |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| workspacecollections | Да | Да |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| capacities | Да | Да |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетные записи | Нет | Нет |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| vaults | Да | Да |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| пространства имен | Да | Да |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| приложений | Да | Нет |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| Потоки | Да | Да |
| jobcollections | Да | Да |

## <a name="microsoftsearch"></a>Microsoft.Search
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| searchservices | Да | Да |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| gateways | Нет | Нет |
| Узлы | Нет | Нет |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| пространства имен | Да | Да |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| приложений | Нет | Нет |
| clusters | Да | Да |
| containergroups | Нет | Нет |
| containergroupsets | Нет | Нет |
| edgeclusters | Нет | Нет |
| Сети | Нет | Нет |
| secretstores | Нет | Нет |
| тома | Нет | Нет |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| приложений | Да | Да |
| containergroups | Нет | Нет |
| gateways | Да | Да |
| Сети | Да | Да |
| секреты | Да | Да |
| тома | Да | Да |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| signalr | Да | Да |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Нет | Нет |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Нет | Нет |
| appliances | Нет | Нет |
| applicationdefinitions | Нет | Нет |
| приложений | Нет | Нет |
| jitrequests | Нет | Нет |

## <a name="microsoftsql"></a>Microsoft.Sql
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| managedinstances | Да | Да |
| managedinstances/databases | Да | Да |
| серверы | Да | Да |
| servers/databases | Да | Да |
| servers/elasticpools | Да | Да |
| servers/jobaccounts | Нет | Нет |
| servers/jobagents | Нет | Нет |
| virtualclusters | Да | Да |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Да | Да |
| sqlvirtualmachines | Да | Да |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| dwvm | Нет | Нет |

## <a name="microsoftstorage"></a>Microsoft.Storage;
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storageaccounts | Да | Да |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storagesyncservices | Да | Да |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storagesyncservices | Нет | Нет |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storagesyncservices | Нет | Нет |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| managers | Нет | Нет |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| streamingjobs | Да | Да |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| environments | Нет | Нет |
| environments/eventsources | Нет | Нет |
| Экземпляры | Нет | Нет |
| instances/environments | Нет | Нет |
| instances/environments/eventsources | Нет | Нет |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| providerregistrations | Нет | Нет |
| ресурсы | Нет | Нет |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| environments | Да | Да |
| environments/eventsources | Да | Да |
| environments/referencedatasets | Да | Да |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| imagetemplates | Нет | Нет |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетная запись | Да | Да |
| account/extension | Да | Да |
| account/project | Да | Да |

## <a name="microsoftweb"></a>Microsoft.Web
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| сертификаты | Нет | Да |
| connectiongateways | Да | Да |
| подключения | Да | Да |
| customapis | Да | Да |
| hostingenvironments | Нет | Нет |
| serverfarms | Да | Да |
| сайты | Да | Да |
| sites/premieraddons | Да | Да |
| sites/slots | Да | Да |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| deviceservices | Да | Да |

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.

## <a name="next-steps"></a>Дальнейшие действия
Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).
