---
title: Типы соединения с управляемым экземпляром
description: Дополнительные сведения о типах соединений с управляемым экземпляром
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819463"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Типы подключений управляемого экземпляра базы данных SQL Azure

В этой статье объясняется, как клиенты подключаются к управляемому экземпляру базы данных SQL Azure в зависимости от типа соединения. Ниже приведены примеры сценариев для изменения типов подключений, а также соображения, связанные с изменением параметров подключения по умолчанию.

## <a name="connection-types"></a>Типы подключений

Управляемый экземпляр базы данных SQL Azure поддерживает следующие два типа подключений:

- **Перенаправление (рекомендуется).** Клиенты устанавливают подключения непосредственно к размещению узла базы данных. Чтобы включить подключение с помощью перенаправления, необходимо открыть брандмауэры и группы безопасности сети (NSG), чтобы разрешить доступ через порты 1433 и 11000-11999. Пакеты отправляются непосредственно в базу данных, поэтому при использовании перенаправления через прокси-сервер можно повысить производительность задержки и пропускной способности.
- **Прокси-сервер (по умолчанию):** В этом режиме все соединения используют компонент прокси-шлюза. Чтобы включить подключение, необходимо открыть только порт 1433 для частных сетей и порт 3342 для общедоступного подключения. Этот режим может стать результатом большего времени задержки и меньшей пропускной способности, в зависимости от характера рабочей нагрузки. Чтобы задержка была минимальной, а пропускная способность высокой, настоятельно рекомендуется использовать политику перенаправления подключения через политику подключения прокси-сервера.

## <a name="redirect-connection-type"></a>Тип подключения перенаправления

Тип подключения перенаправления означает, что после установки сеанса TCP в ядро SQL сеанс клиента получает целевой виртуальный IP-адрес узла виртуального кластера из балансировщика нагрузки. Последующие пакеты поступают непосредственно на узел виртуального кластера, минуя шлюз. Этот поток трафика представлен на схеме ниже.

![перенаправление. png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> Тип подключения перенаправления в настоящее время работает только для частной конечной точки. Независимо от параметра типа подключения подключения через общедоступную конечную точку проходят через прокси-сервер.

## <a name="proxy-connection-type"></a>Тип подключения прокси-сервера

Тип подключения прокси означает, что сеанс TCP устанавливается с помощью шлюза и все последующие пакеты проходят через него. Этот поток трафика представлен на схеме ниже.

![Proxy. png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Скрипт для изменения параметров типа соединения с помощью PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Следующий скрипт PowerShell показывает, как изменить тип соединения для управляемого экземпляра на перенаправление.

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

- [Краткое руководство. Восстановление базы данных в Управляемый экземпляр](sql-database-managed-instance-get-started-restore.md)
- Узнайте, как [настроить общедоступную конечную точку на управляемом экземпляре](sql-database-managed-instance-public-endpoint-configure.md)
- Сведения об [архитектуре подключения управляемого экземпляра](sql-database-managed-instance-connectivity-architecture.md)