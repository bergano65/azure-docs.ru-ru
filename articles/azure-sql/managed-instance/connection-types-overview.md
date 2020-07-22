---
title: Типы подключений
titleSuffix: Azure SQL Managed Instance
description: Сведения о типах подключений Управляемый экземпляр Azure SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 6c6774fb462a21e721b19ae53d1d018d780b28ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85517326"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Типы подключений Управляемый экземпляр Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

В этой статье объясняется, как клиенты подключаются к Azure SQL Управляемый экземпляр в зависимости от типа соединения. Ниже приведены примеры сценариев для изменения типов подключений, а также соображения, связанные с изменением параметров подключения по умолчанию.

## <a name="connection-types"></a>Типы подключений

Управляемый экземпляр Azure SQL поддерживает следующие два типа подключений:

- **Перенаправление (рекомендуется).** Клиенты устанавливают подключения непосредственно к размещению узла базы данных. Чтобы включить подключение с помощью перенаправления, необходимо открыть брандмауэры и группы безопасности сети (NSG), чтобы разрешить доступ через порты 1433 и 11000-11999. Пакеты отправляются непосредственно в базу данных, поэтому при использовании перенаправления через прокси-сервер можно повысить производительность задержки и пропускной способности.
- **Прокси-сервер (по умолчанию):** В этом режиме все соединения используют компонент прокси-шлюза. Чтобы обеспечить возможность подключения, необходимо открыть только порт 1433 для частных сетей и порт 3342 для общедоступного подключения. Этот режим может стать результатом большего времени задержки и меньшей пропускной способности, в зависимости от характера рабочей нагрузки. Мы настоятельно рекомендуем использовать политику перенаправления подключений в политике прокси-подключения для минимальной задержки и высокой пропускной способности.

## <a name="redirect-connection-type"></a>Тип подключения перенаправления

В типе подключения перенаправления после того, как сеанс TCP установлен для ядра SQL, клиентский сеанс получает целевой виртуальный IP-адрес узла виртуального кластера из балансировщика нагрузки. Последующие пакеты поступают непосредственно на узел виртуального кластера, минуя шлюз. Этот поток трафика представлен на схеме ниже.

![redirect.png](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> Тип подключения перенаправления в настоящее время работает только для частной конечной точки. Независимо от параметра типа подключения подключения через общедоступную конечную точку проходят через прокси-сервер.

## <a name="proxy-connection-type"></a>Тип подключения прокси-сервера

В типе подключения прокси-сервера сеанс TCP устанавливается с помощью шлюза, а все последующие пакеты проходят через него. Этот поток трафика представлен на схеме ниже.

![proxy.png](./media/connection-types-overview/proxy.png)

## <a name="changing-connection-type"></a>Изменение типа подключения

- **С помощью портала:** Чтобы изменить тип подключения с помощью портал Azure, откройте страницу виртуальная сеть и используйте параметр **тип подключения** , чтобы изменить тип соединения и сохранить изменения.

- **Скрипт для изменения параметров типа соединения с помощью PowerShell:**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

В следующем сценарии PowerShell показано, как изменить тип соединения для управляемого экземпляра на `Redirect` .

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

## <a name="next-steps"></a>Дальнейшие шаги

- [Восстановление базы данных в SQL Управляемый экземпляр](restore-sample-database-quickstart.md)
- Узнайте, как [настроить общедоступную конечную точку на SQL управляемый экземпляр](public-endpoint-configure.md)
- Сведения об [архитектуре подключения SQL управляемый экземпляр](connectivity-architecture-overview.md)
