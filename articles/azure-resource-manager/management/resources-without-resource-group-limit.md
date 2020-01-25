---
title: Ресурсы без ограничения числа 800
description: Список типов ресурсов Azure, которые могут иметь более 800 экземпляров в группе ресурсов.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 42e8ddeebcd1dda4fc67e4e7be137c5e01decdf4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715625"
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
* верификатионкэйс

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

* серверы

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* серверы

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* серверграупс
* серверы
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. Ентерприсекновледжеграф

* services;

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* пространства имен

## <a name="microsoftexperimentation"></a>Microsoft. экспериментирование

* експериментворкспацес

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* аутоманажедвмконфигуратионпрофилес
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

* пространства имен

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* пространства имен

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* приложений
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

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="next-steps"></a>Дальнейшие действия

Полный список квот и ограничений см. в статье [Подписка Azure, границы, квоты и ограничения службы](azure-subscription-service-limits.md).
