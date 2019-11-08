---
title: Ресурсы Azure без ограничения числа 800
description: Список типов ресурсов Azure, которые могут иметь более 800 экземпляров в группе ресурсов.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: tomfitz
ms.openlocfilehash: c08b80a841199ed2737f6fe8d7f2b76943c5b269
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795658"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Ресурсы, не ограниченные 800 экземплярами на группу ресурсов

По умолчанию в каждой группе ресурсов можно развернуть до 800 экземпляров типа ресурса. Однако некоторые типы ресурсов исключены из пределов числа экземпляров 800. В этой статье перечислены типы ресурсов Azure, которые могут содержать более 800 экземпляров в группе ресурсов. Все остальные типы ресурсов ограничены 800 экземплярами.

Для некоторых типов ресурсов необходимо обратиться в службу поддержки, чтобы снять ограничение на число экземпляров 800. Эти типы ресурсов указаны в этой статье.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* Ботсервицес — по умолчанию ограничено до 800 экземпляров. Это ограничение можно увеличить, обратившись в службу поддержки.

## <a name="microsoftcompute"></a>Microsoft.Compute;

* disks
* images
* snapshots
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* реестров/Буилдтаскс/Листсаурцерепоситорипропертиес
* registries/buildTasks/steps
* реестры/Буилдтаскс/шаги/Листбуилдаргументс
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* серверграупс
* servers
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф

* services;

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* namespaces

## <a name="microsoftexperimentation"></a>Microsoft. экспериментирование

* експериментворкспацес

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* конфигуратионпрофилеассигнментс
* guestConfigurationAssignments
* software
* софтвареупдатепрофиле
* софтвареупдатес

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* нетаппаккаунтс
* Нетаппаккаунтс/КапаЦитипулс
* Нетаппаккаунтс/КапаЦитипулс/тома
* Нетаппаккаунтс/КапаЦитипулс/Volumes/Маунттаржетс
* Нетаппаккаунтс/КапаЦитипулс/тома/моментальные снимки

## <a name="microsoftnetwork"></a>Microsoft.Network.

* аппликатионгатевайвебаппликатионфиреваллполиЦиес
* applicationSecurityGroups
* бастионхостс
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
* приватеднсзонес
* Приватеднсзонес/A
* Приватеднсзонес/AAAA
* Приватеднсзонес и CNAME
* Приватеднсзонес/MX
* Приватеднсзонес/PTR
* Приватеднсзонес/SOA
* Приватеднсзонес/SRV
* Приватеднсзонес/TXT
* Приватеднсзонес/все
* Приватеднсзонес/Виртуалнетворклинкс
* приватиндпоинтс
* privateLinkServices
* publicIPAddresses — по умолчанию ограничено до 800 экземпляров. Это ограничение можно увеличить, обратившись в службу поддержки.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* Воркспацеколлектионс — по умолчанию ограничено до 800 экземпляров. Это ограничение можно увеличить, обратившись в службу поддержки.

## <a name="microsoftrelay"></a>Microsoft.Relay

* namespaces

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* namespaces

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

Полный список квот и ограничений см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md).
