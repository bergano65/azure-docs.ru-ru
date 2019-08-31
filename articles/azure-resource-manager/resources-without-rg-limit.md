---
title: Ресурсы Azure без ограничения числа 800
description: Список типов ресурсов Azure, которые могут иметь более 800 экземпляров в группе ресурсов.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: tomfitz
ms.openlocfilehash: eec448dcbf1c550b6b0a0ee0f17505fd6ddd3c74
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194735"
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

* Ботсервицес — обратитесь в службу поддержки, чтобы продлить ограничение.

## <a name="microsoftcompute"></a>Microsoft.Compute;

* диски
* образы
* моментальные снимки
* virtualMachines

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

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* guestConfigurationAssignments
* software
* софтвареупдатепрофиле
* софтвареупдатес

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

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
* publicIPAddresses — обратитесь в службу поддержки, чтобы продлить ограничение.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* Воркспацеколлектионс — обратитесь в службу поддержки, чтобы продлить ограничение.

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftstorage"></a>Microsoft.Storage;

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* сайты

## <a name="next-steps"></a>Следующие шаги

Полный список квот и ограничений см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md).
