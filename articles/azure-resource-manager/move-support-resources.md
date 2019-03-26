---
title: Поддержка операции перемещения типом ресурсов Azure
description: Список типов ресурсов Azure, подлежащих перемещению в новую группу ресурсов или подписку.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 03/22/2019
ms.author: tomfitz
ms.openlocfilehash: d44b1bf778c7ec9551e2fd30f67083f8dded22d1
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438474"
---
# <a name="move-operation-support-for-resources"></a>Поддержка операции перемещения для ресурсов
В этой статье указано, поддерживается ли для тех или иных типов ресурсов Azure операция перемещения. Несмотря на то что тип ресурса поддерживает операцию перемещения, некоторые условия могут препятствовать перемещению ресурса. Дополнительные сведения об условиях, влияющих на операции перемещения, см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

Чтобы получить данные, идентичные значению файла с разделителями-запятыми, необходимо скачать [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| domainservices | Нет  | Нет  |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| tenants | Нет  | Нет  |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| servers | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| свойства | Yes | Yes |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| apiapps | Нет  | Нет  |
| appidentities | Нет  | Нет  |
| gateways | Нет  | Нет  |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| policyassignments | Нет  | Нет  |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| automationaccounts | Yes | Yes |
| automationaccounts/configurations | Yes | Yes |
| automationaccounts/runbooks | Yes | Yes |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| b2cdirectories | Yes | Yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| registrations | Yes | Yes |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| backupvault | Нет  | Нет  |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| batchaccounts | Yes | Yes |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| clusters | Нет  | Нет  |
| fileservers | Нет  | Нет  |
| jobs | Нет  | Нет  |
| workspaces | Нет  | Нет  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| mapapis | Нет  | Нет  |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| biztalk | Yes | Yes |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| blockchainmembers | Нет  | Нет  |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| blueprintassignments | Нет  | Нет  |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| botservices | Yes | Yes |

## <a name="microsoftcache"></a>Microsoft.Cache
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| redis | Yes | Yes |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| профили | Yes | Yes |
| profiles/endpoints | Yes | Yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| certificateorders | Yes | Yes |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| domainnames | Yes | Нет  |
| virtualmachines | Yes | Нет  |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Нет  | Нет  |
| reservedips | Нет  | Нет  |
| virtualnetworks | Нет  | Нет  |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storageaccounts | Yes | Нет  |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftcompute"></a>Microsoft.Compute;
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| availabilitysets | Yes | Yes |
| disks | Yes | Yes |
| galleries | Нет  | Нет  |
| galleries/images | Нет  | Нет  |
| galleries/images/versions | Нет  | Нет  |
| images | Yes | Yes |
| proximityplacementgroups | Нет  | Нет  |
| restorepointcollections | Нет  | Нет  |
| sharedvmimages | Нет  | Нет  |
| sharedvmimages/versions | Нет  | Нет  |
| snapshots | Yes | Yes |
| virtualmachines | Yes | Yes |
| virtualmachines/extensions | Yes | Yes |
| virtualmachinescalesets | Yes | Yes |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| containergroups | Нет  | Нет  |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| containergroups | Нет  | Нет  |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| registries | Yes | Yes |
| registries/buildtasks | Yes | Yes |
| registries/replications | Нет  | Нет  |
| registries/tasks | Yes | Yes |
| registries/webhooks | Yes | Yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| containerservices | Нет  | Нет  |
| managedclusters | Нет  | Нет  |
| openshiftmanagedclusters | Нет  | Нет  |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| веб-масштабированием; | Yes | Yes |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| accounts | Нет  | Нет  |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| соединители | Yes | Yes |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hubs | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| jobs | Нет  | Нет  |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Нет  | Нет  |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| workspaces | Нет  | Нет  |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| catalogs | Yes | Yes |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| connectionmanagers | Нет  | Нет  |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| Пакеты | Нет  | Нет  |
| Планы | Нет  | Нет  |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| datafactories | Yes | Yes |
| factories | Yes | Yes |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Нет  | Нет  |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| services; | Нет  | Нет  |
| services/projects | Нет  | Нет  |
| slots | Нет  | Нет  |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| servers | Yes | Yes |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| servers | Yes | Yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| servergroups | Нет  | Нет  |
| servers | Yes | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| artifactsources | Нет  | Нет  |
| rollouts | Нет  | Нет  |
| servicetopologies | Нет  | Нет  |
| servicetopologies/services | Нет  | Нет  |
| servicetopologies/services/serviceunits | Нет  | Нет  |
| steps | Нет  | Нет  |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| elasticpools | Нет  | Нет  |
| elasticpools/iothubtenants | Нет  | Нет  |
| iothubs | Yes | Yes |
| provisioningservices | Yes | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| controllers | Нет  | Нет  |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| labcenters | Нет  | Нет  |
| labs | Yes | Нет  |
| labs/servicerunners | Yes | Yes |
| labs/virtualmachines | Yes | Нет  |
| schedules | Нет  | Нет  |

## <a name="microsoftdns"></a>microsoft.dns
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| dnszones | Нет  | Нет  |
| dnszones/a | Нет  | Нет  |
| dnszones/aaaa | Нет  | Нет  |
| dnszones/cname | Нет  | Нет  |
| dnszones/mx | Нет  | Нет  |
| dnszones/ptr | Нет  | Нет  |
| dnszones/srv | Нет  | Нет  |
| dnszones/txt | Нет  | Нет  |
| trafficmanagerprofiles | Нет  | Нет  |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| databaseaccounts | Yes | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| domains | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| domains | Yes | Yes |
| topics | Yes | Yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| clusters | Yes | Yes |
| namespaces | Yes | Yes |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| accounts | Нет  | Нет  |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hanainstances | Yes | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| clusters | Yes | Yes |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| datamanagers | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| jobs | Yes | Yes |

## <a name="microsoftinsights"></a>microsoft.insights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| accounts | Нет  | Нет  |
| actiongroups | Yes | Yes |
| activitylogalerts | Нет  | Нет  |
| alertrules | Yes | Yes |
| autoscalesettings | Yes | Yes |
| components | Yes | Yes |
| guestdiagnosticsettings | Нет  | Нет  |
| metricalerts | Нет  | Нет  |
| notificationgroups | Нет  | Нет  |
| notificationrules | Нет  | Нет  |
| scheduledqueryrules | Нет  | Нет  |
| webtests | Yes | Yes |
| workbooks | Yes | Yes |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| iotapps | Yes | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| checknameavailability | Yes | Yes |
| graph | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hsmpools | Нет  | Нет  |
| vaults | Yes | Yes |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| clusters | Yes | Yes |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| labaccounts | Нет  | Нет  |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hostingenvironments | Нет  | Нет  |
| integrationaccounts | Yes | Yes |
| integrationserviceenvironments | Нет  | Нет  |
| isolatedenvironments | Нет  | Нет  |
| workflows | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| commitmentplans | Yes | Yes |
| webservices | Yes | Нет  |
| workspaces | Yes | Yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Yes | Yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |
| accounts/workspaces | Yes | Yes |
| accounts/workspaces/projects | Yes | Yes |
| teamaccounts | Yes | Yes |
| teamaccounts/workspaces | Yes | Yes |
| teamaccounts/workspaces/projects | Yes | Yes |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| hostingaccounts | Нет  | Нет  |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| workspaces | Нет  | Нет  |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| userassignedidentities | Yes | Yes |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| classicdevservices | Нет  | Нет  |

## <a name="microsoftmedia"></a>Microsoft.Media
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| mediaservices | Yes | Yes |
| mediaservices/liveevents | Yes | Yes |
| mediaservices/streamingendpoints | Yes | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| assessmentprojects | Нет  | Нет  |
| migrateprojects | Нет  | Нет  |
| projects | Нет  | Нет  |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| netappaccounts | Нет  | Нет  |
| netappaccounts/capacitypools | Нет  | Нет  |
| netappaccounts/capacitypools/volumes | Нет  | Нет  |
| netappaccounts/capacitypools/volumes/mounttargets | Нет  | Нет  |
| netappaccounts/capacitypools/volumes/snapshots | Нет  | Нет  |

## <a name="microsoftnetwork"></a>Microsoft.Network.
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| applicationgateways | Нет  | Нет  |
| applicationsecuritygroups | Yes | Yes |
| azurefirewalls | Yes | Yes |
| bastionhosts | Нет  | Нет  |
| connections | Yes | Yes |
| ddoscustompolicies | Yes | Yes |
| ddosprotectionplans | Нет  | Нет  |
| dnszones | Yes | Yes |
| expressroutecircuits | Нет  | Нет  |
| expressroutecrossconnections | Нет  | Нет  |
| expressroutegateways | Нет  | Нет  |
| expressrouteports | Нет  | Нет  |
| frontdoors | Yes | Yes |
| frontdoorwebapplicationfirewallpolicies | Yes | Yes |
| interfaceendpoints | Нет  | Нет  |
| loadbalancers | Yes | Yes |
| localnetworkgateways | Yes | Yes |
| natgateways | Yes | Yes |
| networkintentpolicies | Yes | Yes |
| networkinterfaces | Yes | Yes |
| networkprofiles | Нет  | Нет  |
| networksecuritygroups | Yes | Yes |
| networkwatchers | Yes | Yes |
| networkwatchers/connectionmonitors | Yes | Yes |
| networkwatchers/lenses | Yes | Yes |
| networkwatchers/pingmeshes | Yes | Yes |
| p2svpngateways | Нет  | Нет  |
| privatelinkservices | Нет  | Нет  |
| publicipaddresses | Yes | Yes |
| publicipprefixes | Yes | Yes |
| routefilters | Нет  | Нет  |
| routetables | Yes | Yes |
| securegateways | Нет  | Нет  |
| serviceendpointpolicies | Yes | Yes |
| trafficmanagerprofiles | Yes | Yes |
| virtualhubs | Нет  | Нет  |
| virtualnetworkgateways | Yes | Yes |
| virtualnetworks | Yes | Yes |
| virtualnetworktaps | Нет  | Нет  |
| virtualwans | Нет  | Нет  |
| vpngateways | Нет  | Нет  |
| vpnsites | Yes | Yes |
| webapplicationfirewallpolicies | Yes | Yes |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| namespaces | Yes | Yes |
| namespaces/notificationhubs | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| workspaces | Yes | Yes |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| managementconfigurations | Yes | Yes |
| solutions | Yes | Yes |
| узел "Представления" | Yes | Yes |

## <a name="microsoftportal"></a>Microsoft.Portal
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| dashboards | Yes | Yes |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| rootresources | Нет  | Нет  |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| workspacecollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| capacities | Yes | Yes |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| accounts | Нет  | Нет  |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| vaults | Yes | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| namespaces | Yes | Yes |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| веб-масштабированием; | Yes | Нет  |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| flows | Yes | Yes |
| jobcollections | Yes | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| searchservices | Yes | Yes |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| gateways | Нет  | Нет  |
| Узлы | Нет  | Нет  |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| namespaces | Yes | Yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| веб-масштабированием; | Нет  | Нет  |
| clusters | Yes | Yes |
| containergroups | Нет  | Нет  |
| containergroupsets | Нет  | Нет  |
| edgeclusters | Нет  | Нет  |
| networks | Нет  | Нет  |
| secretstores | Нет  | Нет  |
| volumes. | Нет  | Нет  |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| веб-масштабированием; | Yes | Yes |
| containergroups | Нет  | Нет  |
| gateways | Yes | Yes |
| networks | Yes | Yes |
| секретные коды | Yes | Yes |
| volumes. | Yes | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| signalr | Yes | Yes |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Нет  | Нет  |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Нет  | Нет  |
| appliances | Нет  | Нет  |
| applicationdefinitions | Нет  | Нет  |
| веб-масштабированием; | Нет  | Нет  |
| jitrequests | Нет  | Нет  |

## <a name="microsoftsql"></a>Microsoft.Sql
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| managedinstances | Yes | Yes |
| managedinstances/databases | Yes | Yes |
| servers | Yes | Yes |
| servers/databases | Yes | Yes |
| servers/elasticpools | Yes | Yes |
| servers/jobaccounts | Нет  | Нет  |
| servers/jobagents | Нет  | Нет  |
| virtualclusters | Yes | Yes |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Yes | Yes |
| sqlvirtualmachines | Yes | Yes |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| dwvm | Нет  | Нет  |

## <a name="microsoftstorage"></a>Microsoft.Storage;
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storageaccounts | Yes | Yes |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storagesyncservices | Yes | Yes |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storagesyncservices | Нет  | Нет  |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| storagesyncservices | Нет  | Нет  |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| managers | Нет  | Нет  |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| streamingjobs | Yes | Yes |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| environments | Нет  | Нет  |
| environments/eventsources | Нет  | Нет  |
| instances | Нет  | Нет  |
| instances/environments | Нет  | Нет  |
| instances/environments/eventsources | Нет  | Нет  |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| providerregistrations | Нет  | Нет  |
| ресурсов | Нет  | Нет  |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| environments | Yes | Yes |
| environments/eventsources | Yes | Yes |
| environments/referencedatasets | Yes | Yes |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| imagetemplates | Нет  | Нет  |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| учетная запись | Yes | Yes |
| account/extension | Yes | Yes |
| account/project | Yes | Yes |

## <a name="microsoftweb"></a>Microsoft.Web
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| certificates | Нет  | Yes |
| connectiongateways | Yes | Yes |
| connections | Yes | Yes |
| customapis | Yes | Yes |
| hostingenvironments | Нет  | Нет  |
| serverfarms | Yes | Yes |
| sites | Yes | Yes |
| sites/premieraddons | Yes | Yes |
| sites/slots | Yes | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | ----------- | ---------- |
| deviceservices | Yes | Yes |

## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения.

## <a name="next-steps"></a>Дальнейшие действия
Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).
