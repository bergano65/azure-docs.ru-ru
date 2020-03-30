---
title: Управляемые типы соединений экземпляров
description: Узнайте о типах управляемых примеров соединения
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819463"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Управляемые типы соединений безопасности Azure S'L

В этой статье объясняется, как клиенты подключаются к управляемому экземпляру базы данных Azure S'L в зависимости от типа соединения. Ниже приведены образцы скриптов для изменения типов соединений, а также соображения, связанные с изменением настроек подключения по умолчанию.

## <a name="connection-types"></a>Типы подключений

Управляемый экземпляр базы данных Azure S'L поддерживает следующие два типа соединений:

- **Перенаправление (рекомендуется).** Клиенты устанавливают подключения непосредственно к размещению узла базы данных. Для обеспечения возможности подключения с помощью перенаправления необходимо открыть брандмауэры и группы сетевой безопасности (NSG), чтобы обеспечить доступ в портах 1433 и 11000-11999. Пакеты идут непосредственно в базу данных, и, следовательно, есть задержка и повышение производительности пропускной способности с помощью Перенаправления через Прокси.
- **Прокси (по умолчанию):** В этом режиме все соединения используют компонент прокси-шлюза. Для обеспечения подключения необходимо открыть только порт 1433 для частных сетей и порт 3342 для подключения к общественному доступу. Этот режим может стать результатом большего времени задержки и меньшей пропускной способности, в зависимости от характера рабочей нагрузки. Чтобы задержка была минимальной, а пропускная способность высокой, настоятельно рекомендуется использовать политику перенаправления подключения через политику подключения прокси-сервера.

## <a name="redirect-connection-type"></a>Перенаправление типа соединения

Перенаправление типа соединения означает, что после того, как сеанс TCP установлен в движке S'L, клиентская сессия получает назначение виртуального IP виртуального IP виртуального кластерного узла от балансиватора нагрузки. Последующие пакеты перетекают непосредственно в виртуальный кластерный узлы, минуя шлюз. Этот поток трафика представлен на схеме ниже.

![redirect.png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> Тип соединения перенаправления в настоящее время работает только для частной конечных точек. Независимо от настройки типа соединения, соединения, проходящие через публичную конечную точку, будут проходить через прокси-

## <a name="proxy-connection-type"></a>Тип прокси-соединения

Тип прокси-соединения означает, что сеанс TCP устанавливается с помощью шлюза, и все последующие пакеты протекают через него. Этот поток трафика представлен на схеме ниже.

![proxy.png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Сценарий для изменения параметров типа соединения с помощью PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Следующий скрипт PowerShell показывает, как изменить тип соединения для управляемого экземпляра для перенаправления.

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>Дальнейшие действия

- [Восстановление базы данных в управляемом экземпляре](sql-database-managed-instance-get-started-restore.md)
- Узнайте, как [настроить общедоступную точку на управляемом экземпляре](sql-database-managed-instance-public-endpoint-configure.md)
- Узнайте об [архитектуре управляемых экземпляров подключения](sql-database-managed-instance-connectivity-architecture.md)