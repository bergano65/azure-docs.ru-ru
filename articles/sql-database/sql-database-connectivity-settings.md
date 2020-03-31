---
title: Настройки подключения для базы данных Azure S'L и хранилища данных
description: В этом документе объясняется выбор версии TLS и настройка прокси против перенаправления для Azure S'L
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366093"
---
# <a name="azure-sql-connectivity-settings"></a>Настройки подключения Azure S'L
> [!NOTE]
> Эта статья относится к Azure SQL Server, а также к базам данных SQL и хранилища данных SQL, создаваемым на сервере SQL Azure. Для простоты база данных SQL используется как для базы данных SQL, так и для хранилища данных SQL.

> [!IMPORTANT]
> Данная статья *не* распространяется на **управляемые данные базы данных Azure S'L**

В этой статье представлены параметры, которые управляют подключением к базе данных Azure S'L на уровне сервера. Эти параметры применяются ко **всем** базам данных S'L и базам данных, связанным с сервером.

> [!NOTE]
> После применения этих параметров они **встретиваются немедленно** и могут привести к потере соединения для ваших клиентов, если они не отвечают требованиям для каждого параметра.

Настройки подключения доступны из **брандмауэров и виртуальных сетей** лезвия, как показано на скриншоте ниже:

 ![Скриншот настроек подключения][1]


## <a name="deny-public-network-access"></a>Отказ в доступе к общедоступной сети
На портале Azure, когда установка **общедоступного доступа к сети Deny** установлена на **Yes,** разрешены только соединения через частные конечные точки. Когда эта настройка установлена **в No,** клиенты могут подключиться с помощью частной или публичной конечной точки.

После установки **Deny общедоступный доступ к сети** **Yes,** попытки входа от клиентов, использующих публичную конечную точку, не смогут сделать следующий этап:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Изменение доступа к общественной сети через PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных Azure S'L, но все будущие разработки предназначены для модуля Az.Sql. Для этих cmdlets, см [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле Az и в модулях Azrm существенно идентичны. Следующий скрипт требует [модуля Azure PowerShell.](/powershell/azure/install-az-ps)

Следующий скрипт PowerShell `Get` `Set` показывает, как и свойство **доступа к общественной сети** на логическом уровне сервера:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Изменение доступа к общественной сети через CLI
> [!IMPORTANT]
> Все скрипты в этом разделе требуют [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli)

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI в оболочке Bash
Следующий скрипт CLI показывает, как изменить доступ к **общественной сети** в оболочке Bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Политика подключения
[Политика подключения](sql-database-connectivity-architecture.md#connection-policy) определяет, как клиенты подключаются к серверу Azure S'L Server. 

## <a name="change-connection-policy-via-powershell"></a>Изменение политики подключения через PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных Azure S'L, но все будущие разработки предназначены для модуля Az.Sql. Для этих cmdlets, см [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле Az и в модулях Azrm существенно идентичны. Следующий скрипт требует [модуля Azure PowerShell.](/powershell/azure/install-az-ps)

Следующий скрипт PowerShell показывает, как изменить политику соединения с помощью PowerShell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>Изменение политики подключения через Azure CLI
> [!IMPORTANT]
> Все скрипты в этом разделе требуют [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli)

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI в оболочке Bash
Следующий скрипт CLI показывает, как изменить политику соединения в оболочке Bash: 

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI из запроса команды Windows
Следующий скрипт CLI показывает, как изменить политику соединения с запросом команды Windows (с установкой Azure CLI).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Дальнейшие действия
- Для получения обзора того, как работает подключение в базе данных Azure S'L, обратитесь к [архитектуре подключения Azure S'L](sql-database-connectivity-architecture.md)
- Дополнительные сведения о том, как изменить политику подключения Базы данных SQL Azure для сервера Базы данных SQL Azure, см. в статье о команде [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
