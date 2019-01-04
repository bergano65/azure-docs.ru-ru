---
title: Поддержка операции перемещения по типу ресурсов Azure | Документация Майкрософт
description: Список типов ресурсов Azure, подлежащих перемещению в новую группу ресурсов или подписку.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/28/2018
ms.author: tomfitz
ms.openlocfilehash: 253cfd7ddeb04a12a3609ab5e14a37365015a568
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790883"
---
# <a name="move-operation-support-for-resources"></a>Поддержка операции перемещения для ресурсов

В этой статье указано, поддерживается ли для тех или иных типов ресурсов Azure операция перемещения. Несмотря на то что тип ресурса поддерживает операцию перемещения, некоторые условия могут препятствовать перемещению ресурса. Дополнительные сведения об условиях, влияющих на операции перемещения, см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

## <a name="find-resource-provider-and-resource-type"></a>Поиск поставщика ресурсов и типа ресурса

Чтобы определить, можно ли перемещать ресурс, необходимо найти поставщика и тип этого ресурса.

Для PowerShell используйте команду:

```azurepowershell-interactive
Get-AzureRmResource -ResourceGroupName demogroup | Select Name, ResourceType | Format-table
```

Для интерфейса командной строки Azure:

```azurecli-interactive
az resource list -g demogroup --query '[].{name:name, resourceType:type}' --output table
```

Тип ресурса возвращается в формате `<resource-provider>/<resource-type-name>`. Исходя из значения `Microsoft.OperationalInsights/workspaces`, поставщик ресурсов здесь — **Microsoft.OperationalInsights**, а тип ресурсов — **workspaces**.

Узнав поставщик ресурсов и тип ресурса, используйте таблицы, приведенные в этой статье, чтобы определить, поддерживается ли для вашего типа ресурса операция перемещения.

## <a name="microsoftaad"></a>Microsoft.AAD

| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | --------------- | ----------- |
| domainservices | Нет  | Нет  |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| servers | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | --------------- | ----------- |
| свойства | Yes | Yes |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | --------------- | ----------- |
| policyassignments | Нет  | Нет  |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| automationaccounts | Yes | Yes |
| automationaccounts/configurations | Yes | Yes |
| automationaccounts/runbooks | Yes | Yes |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| b2cdirectories | Yes | Yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| registrations | Yes | Yes |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| backupvault | Нет  | Нет  |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| batchaccounts | Yes | Yes |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| mapapis | Нет  | Нет  |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| biztalk | Yes | Yes |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| blueprintassignments | Нет  | Нет  |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| botservices | Yes | Yes |

## <a name="microsoftcache"></a>Microsoft.Cache
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| redis | Yes | Yes |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| профили | Yes | Yes |
| profiles/endpoints | Yes | Yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| certificateorders | Yes | Yes |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| domainnames | Yes | Нет  |
| virtualmachines | Yes | Нет  |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| networksecuritygroups | Нет  | Нет  |
| reservedips | Нет  | Нет  |
| virtualnetworks | Нет  | Нет  |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| storageaccounts | Yes | Нет  |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |

## <a name="microsoftcompute"></a>Microsoft.Compute;
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| availabilitysets | Yes | Yes |
| disks | Yes | Yes |
| galleries | Нет  | Нет  |
| galleries/images | Нет  | Нет  |
| galleries/images/versions | Нет  | Нет  |
| images | Yes | Yes |
| restorepointcollections | Нет  | Нет  |
| sharedvmimages | Нет  | Нет  |
| sharedvmimages/versions | Нет  | Нет  |
| snapshots | Yes | Yes |
| virtualmachines | Yes | Yes |
| virtualmachines/extensions | Yes | Yes |
| virtualmachinescalesets | Yes | Yes |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| containergroups | Нет  | Нет  |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| containergroups | Нет  | Нет  |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| registries | Yes | Yes |
| registries/buildtasks | Yes | Yes |
| registries/replications | Нет  | Нет  |
| registries/tasks | Yes | Yes |
| registries/webhooks | Yes | Yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| containerservices | Нет  | Нет  |
| managedclusters | Нет  | Нет  |
| openshiftmanagedclusters | Нет  | Нет  |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| веб-масштабированием; | Yes | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| соединители | Yes | Yes |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| hubs | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| jobs | Нет  | Нет  |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| databoxedgedevices | Нет  | Нет  |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| workspaces | Нет  | Нет  |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| catalogs | Yes | Yes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| datafactories | Yes | Yes |
| factories | Yes | Yes |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| datalakeaccounts | Нет  | Нет  |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| services; | Нет  | Нет  |
| services/projects | Нет  | Нет  |
| slots | Нет  | Нет  |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| servers | Нет  | Нет  |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| servers | Yes | Yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| servergroups | Нет  | Нет  |
| servers | Yes | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| artifactsources | Нет  | Нет  |
| rollouts | Нет  | Нет  |
| servicetopologies | Нет  | Нет  |
| servicetopologies/services | Нет  | Нет  |
| servicetopologies/services/serviceunits | Нет  | Нет  |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| iothubs | Yes | Yes |
| provisioningservices | Yes | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| labcenters | Нет  | Нет  |
| labs | Yes | Нет  |
| labs/servicerunners | Yes | Yes |
| labs/virtualmachines | Yes | Нет  |
| schedules | Нет  | Нет  |

## <a name="microsoftdns"></a>microsoft.dns
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
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
| ------------- | -------------- | ------------ |
| databaseaccounts | Yes | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| domains | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| topics | Yes | Yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| clusters | Yes | Yes |
| namespaces | Yes | Yes |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| hanainstances | Yes | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| dedicatedhsms | Нет  | Нет  |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| clusters | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| jobs | Yes | Yes |

## <a name="microsoftinsights"></a>microsoft.insights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| actiongroups | Yes | Yes |
| activitylogalerts | Нет  | Нет  |
| alertrules | Yes | Yes |
| autoscalesettings | Yes | Yes |
| components | Yes | Yes |
| metricalerts | Нет  | Нет  |
| scheduledqueryrules | Yes | Yes |
| webtests | Yes | Yes |
| workbooks | Yes | Yes |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| iotapps | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| vaults | Yes | Yes |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| labaccounts | Yes | Yes |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| integrationaccounts | Yes | Yes |
| workflows | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| commitmentplans | Yes | Yes |
| webservices | Yes | Нет  |
| workspaces | Yes | Yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| operationalizationclusters | Yes | Yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |
| accounts/workspaces | Yes | Yes |
| accounts/workspaces/projects | Yes | Yes |
| teamaccounts | Yes | Yes |
| teamaccounts/workspaces | Yes | Yes |
| teamaccounts/workspaces/projects | Yes | Yes |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| workspaces | Yes | Yes |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| userassignedidentities | Yes | Yes |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| classicdevservices | Нет  | Нет  |

## <a name="microsoftmedia"></a>Microsoft.Media
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| mediaservices | Yes | Yes |
| mediaservices/liveevents | Yes | Yes |
| mediaservices/streamingendpoints | Yes | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| projects | Нет  | Нет  |

## <a name="microsoftnetwork"></a>Microsoft.Network.
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| applicationgateways | Нет  | Нет  |
| applicationsecuritygroups | Yes | Yes |
| azurefirewalls | Нет  | Нет  |
| connections | Yes | Yes |
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
| networkintentpolicies | Yes | Yes |
| networkinterfaces | Yes | Yes |
| networkprofiles | Нет  | Нет  |
| networksecuritygroups | Yes | Yes |
| networkwatchers | Yes | Yes |
| networkwatchers/connectionmonitors | Yes | Yes |
| networkwatchers/lenses | Yes | Yes |
| networkwatchers/pingmeshes | Yes | Yes |
| publicipaddresses | Yes | Yes |
| publicipprefixes | Yes | Yes |
| routefilters | Нет  | Нет  |
| routetables | Yes | Yes |
| serviceendpointpolicies | Yes | Yes |
| trafficmanagerprofiles | Yes | Yes |
| virtualhubs | Yes | Yes |
| virtualnetworkgateways | Yes | Yes |
| virtualnetworks | Yes | Yes |
| virtualnetworktaps | Нет  | Нет  |
| virtualwans | Yes | Yes |
| vpngateways | Yes | Yes |
| vpnsites | Yes | Yes |
| webapplicationfirewallpolicies | Yes | Yes |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| namespaces | Yes | Yes |
| namespaces/notificationhubs | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| workspaces | Yes | Yes |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| managementconfigurations | Yes | Yes |
| solutions | Yes | Yes |
| узел "Представления" | Yes | Yes |

## <a name="microsoftportal"></a>Microsoft.Portal
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| dashboards | Yes | Yes |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| workspacecollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| capacities | Yes | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| vaults | Yes | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| namespaces | Yes | Yes |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| веб-масштабированием; | Yes | Нет  |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| flows | Yes | Yes |
| jobcollections | Yes | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| searchservices | Yes | Yes |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| namespaces | Yes | Yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| clusters | Yes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| веб-масштабированием; | Yes | Yes |
| networks | Yes | Yes |
| volumes. | Yes | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| signalr | Yes | Yes |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| siterecoveryvault | Нет  | Нет  |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| appliancedefinitions | Нет  | Нет  |
| appliances | Нет  | Нет  |
| applicationdefinitions | Нет  | Нет  |
| веб-масштабированием; | Нет  | Нет  |

## <a name="microsoftsql"></a>Microsoft.Sql
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| managedinstances | Yes | Yes |
| managedinstances/databases | Yes | Yes |
| servers | Yes | Yes |
| servers/databases | Yes | Yes |
| servers/elasticpools | Yes | Yes |
| virtualclusters | Yes | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage;
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| storageaccounts | Yes | Yes |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| storagesyncservices | Yes | Yes |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| managers | Нет  | Нет  |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| streamingjobs | Yes | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| environments | Yes | Yes |
| environments/eventsources | Yes | Yes |
| environments/referencedatasets | Yes | Yes |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| учетная запись | Yes | Yes |
| account/extension | Yes | Yes |
| account/project | Yes | Yes |

## <a name="microsoftweb"></a>Microsoft.Web
| Тип ресурса | Группа ресурсов | Подписка |
| ------------- | -------------- | ------------ |
| certificates | Нет  | Yes |
| classicmobileservices | Нет  | Нет  |
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
| ------------- | -------------- | ------------ |
| deviceservices | Yes | Yes |


## <a name="third-party-services"></a>Сторонние службы

Сейчас для сторонних служб не поддерживается операция перемещения. Поставщики сторонних служб:

* 84codes.CloudAMQP
* AppDynamics.APM
* Aspera.Transfers
* Auth0.Cloud
* Citrix.Cloud
* Citrix.Services
* CloudSimple.PrivateCloudIaaS
* Cloudyn.Analytics
* Conexlink.MyCloudIT
* Crypteron.DataSecurity
* Dynatrace.DynatraceSaaS
* Dynatrace.Ruxit
* LiveArena.Broadcast
* Lombiq.DotNest
* Mailjet.Email
* Myget.PackageManagement
* NewRelic.APM
* nuubit.nextgencdn
* Paraleap.CloudMonix
* Pokitdok.Platform
* RavenHq.Db
* Raygun.CrashReporting
* RevAPM.MobileCDN
* Sendgrid.Email
* Sparkpost.Basic
* stackify.retrace
* SuccessBricks.ClearDB
* TrendMicro.DeepSecurity
* U2uconsult.TheIdentityHub


## <a name="next-steps"></a>Дополнительная информация

* Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).
