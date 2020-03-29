---
title: Ресурсы без лимита 800
description: Перечисляет типы ресурсов Azure, которые могут иметь более 800 экземпляров в группе ресурсов.
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 735cad0bfa936c41f603e42bdb9be77a1562cc1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937940"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Ресурсы, не ограниченные 800 экземплярами на группу ресурсов

По умолчанию в каждой группе ресурсов можно развернуть до 800 экземпляров типа ресурсов. Однако некоторые типы ресурсов освобождаются от лимита 800 экземпляров. В этой статье перечислены типы ресурсов Azure, которые могут иметь более 800 экземпляров в группе ресурсов. Все остальные типы ресурсов ограничены 800 экземплярами.

Для некоторых типов ресурсов необходимо связаться с службой поддержки, чтобы удалить ограничение 800 экземпляров. Эти типы ресурсов отмечены в этой статье.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registrations
* registrations/customerSubscriptions
* registrations/products
* verificationKeys

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices - по умолчанию, ограниченный 800 экземплярами. Этот лимит можно увеличить, обратившись в службу поддержки.

## <a name="microsoftcompute"></a>Microsoft.Compute;

* disks
* images
* snapshots
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* реестры/сборкаTasks/списокИсточникИ
* registries/buildTasks/steps
* реестры/сборкаTasks/шаги/listBuildАргументы
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* сервергруппы
* servers
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services;

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* пространства имен

## <a name="microsoftexperimentation"></a>Microsoft.Эксперименты

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileНазначенияи
* guestConfigurationAssignments
* software
* программное обеспечениеUpdateProfile
* softwareUpdates

## <a name="microsoftinsights"></a>Microsoft.Insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/snapshots

## <a name="microsoftnetwork"></a>Microsoft.Network.

* приложениеGatewayWebApplicationFirewallПолитики
* applicationSecurityGroups
* бастионХомсты
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsзоны
* privateDnszones/A
* privateDnszones/AAAA
* privateDnszones/CNAME
* privateDnszones/MX
* privateDnszones/PTR
* privateDnszones/SOA
* privateDnszones/SRV
* privateDnszones/TXT
* privateDnsзоны/все
* privateDnszones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses - По умолчанию, ограничивается 800 экземпляров. Этот лимит можно увеличить, обратившись в службу поддержки.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections - По умолчанию, ограничивается 800 экземпляров. Этот лимит можно увеличить, обратившись в службу поддержки.

## <a name="microsoftrelay"></a>Microsoft.Relay

* пространства имен

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* пространства имен

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* веб-масштабированием;
* containerGroups
* gateways
* networks
* секретные коды
* volumes.

## <a name="microsoftstorage"></a>Microsoft.Storage;

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>Дальнейшие действия

Для полного списка квот и ограничений [см.](azure-subscription-service-limits.md)
