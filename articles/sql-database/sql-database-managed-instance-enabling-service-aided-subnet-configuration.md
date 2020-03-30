---
title: Включение конфигурации подсетевого подсети с поддержкой службы для управляемой группы данных Базы данных Azure S'L
description: Включение конфигурации подсетевого подсети с поддержкой службы для управляемой группы данных Базы данных Azure S'L
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: efc2b8578651f68d052f227694f85348853e191f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533272"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>Включение конфигурации подсетевого подсети с поддержкой службы для управляемой группы данных Базы данных Azure S'L
Конфигурация подсети с поддержкой обслуживания обеспечивает автоматическое управление конфигурацией сети для подсетей, хозяйничать управляемых экземпляров. При поддержке подсети с помощью служб пользователь остается в полном контроле доступа к данным (потоки трафика TDS), в то время как управляемый экземпляр берет на себя ответственность за обеспечение бесперебойного потока трафика управления для выполнения SLA.

Автоматически настроенные группы сетевой безопасности и правила таблицы маршрутов видны клиенту и аннотированы префиксом _Microsoft.Sql-managedInstances_UseOnly._

Конфигурация с поддержкой сервиса включается автоматически, как `Microsoft.Sql/managedInstances` только вы включаете [субнет-делегацию](../virtual-network/subnet-delegation-overview.md) для поставщика ресурсов.

> [!IMPORTANT] 
> После того, как делегация подсети включена, вы не сможете выключить ее, пока не удалите последний виртуальный кластер из подсети. Для получения более подробной информации о [article](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal)том, как удалить виртуальный кластер см.

> [!NOTE] 
> Поскольку конфигурация подсети с помощью сервисов является важной особенностью для поддержания SLA, начиная с 1 мая 2020 года, невозможно будет развернуть управляемые экземпляры в подсетах, которые не делегированы поставщику ресурсов управляемого экземпляра. С 1 июля 2020 года все подсети, содержащие управляемые экземпляры, будут автоматически делегированы поставщику ресурсов управляемого экземпляра. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Включение субнет-делегации для новых развертываний
Для развертывания управляемого экземпляра в пустую подсеть необходимо делегировать его поставщику `Microsoft.Sql/managedInstances` ресурсов, как описано в [следующей статье.](../virtual-network/manage-subnet-delegation.md) _Пожалуйста, обратите внимание, что в ссылке на статью используется, `Microsoft.DBforPostgreSQL/serversv2` например, поставщик ресурсов. Вместо этого необходимо `Microsoft.Sql/managedInstances` использовать поставщика ресурсов._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Включение субнет-делегирования для существующих развертываний

Для включения делегации подсети для существующего управляемого развертывания экземпляра необходимо выяснить виртуальную сетевую подсеть, где она находится. 

Чтобы узнать это, вы можете проверить `Virtual network/subnet` на портале `Overview` лезвие вашего управляемого экземпляра.

В качестве альтернативы можно выполнить следующие команды PowerShell, чтобы узнать это. Замените **идентификатор подписки** на идентификатор подписки. Также замените **rg-name** группой ресурсов для управляемого экземпляра и замените **mi-name** именем управляемого экземпляра.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

Как только вы найдете управляемую `Microsoft.Sql/managedInstances` подсеть экземпляра, вам нужно делегировать ее поставщику ресурсов, как описано в [следующей статье.](../virtual-network/manage-subnet-delegation.md) _Пожалуйста, обратите внимание, что в ссылке на статью используется, `Microsoft.DBforPostgreSQL/serversv2` например, поставщик ресурсов. Вместо этого необходимо `Microsoft.Sql/managedInstances` использовать поставщика ресурсов._


> [!IMPORTANT]
> Включение конфигурации с поддержкой службы не приводит к сбою или прерыванию подключения для управляемых экземпляров, которые уже находятся в подсети.
