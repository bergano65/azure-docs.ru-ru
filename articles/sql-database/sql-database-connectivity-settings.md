---
title: Параметры подключения для базы данных и хранилища данных SQL Azure
description: В этом документе объясняется выбор версии TLS и настройка прокси-сервера и перенаправления для Azure SQL.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: 8d40dd09144bddc41347947c0123988530f93f90
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945433"
---
# <a name="azure-sql-connectivity-settings"></a>Параметры подключения к SQL Azure
> [!NOTE]
> Эта статья относится к Azure SQL Server, а также к базам данных SQL и хранилища данных SQL, создаваемым на сервере SQL Azure. Для простоты база данных SQL используется как для базы данных SQL, так и для хранилища данных SQL.

> [!IMPORTANT]
> Эта статья *не* относится к **управляемый экземпляр базы данных SQL Azure**

В этой статье описываются параметры, управляющие подключением к базе данных SQL Azure на уровне сервера. Эти параметры применяются ко **всем** базам данных SQL и хранилищам данных SQL, связанным с сервером.

> [!NOTE]
> После применения этих параметров они вступают в **силу немедленно** и могут привести к утрате подключения для клиентов, если они не соответствуют требованиям для каждого параметра.

Параметры подключения доступны в колонке **брандмауэры и виртуальные сети** , как показано на снимке экрана ниже:

 ![Снимок экрана параметров подключения][1]


## <a name="deny-public-network-access"></a>Запретить доступ к общедоступной сети
В портал Azure, если для параметра **запретить доступ к общедоступной сети** задано значение **Да**, разрешены только подключения через частные конечные точки. Если для этого параметра установлено значение **нет**, клиенты могут подключаться с помощью частной или общедоступной конечной точки.

После установки параметра **запретить доступ к общедоступной сети** для **Да**, попытки входа от клиентов, использующих общедоступную конечную точку, будут завершаться следующей ошибкой:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Изменение доступа к общедоступной сети с помощью PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны. Для следующего скрипта требуется [модуль Azure PowerShell](/powershell/azure/install-az-ps).

В следующем сценарии PowerShell показано, как `Get` и `Set` свойство **доступа к общедоступной сети** на уровне логического сервера.

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Изменение доступа к общедоступной сети с помощью интерфейса командной строки
> [!IMPORTANT]
> Для всех скриптов в этом разделе требуется [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI в оболочке bash
В следующем сценарии интерфейса командной строки показано, как изменить **доступ к** общедоступной сети в оболочке bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```

## <a name="minimal-tls-version"></a>Минимальная версия TLS 
Минимальная версия TLS позволяет клиенту управлять версией [безопасности транспортного уровня](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) для SQL Server Azure.

Рекомендуется задать для минимальной версии TLS значение 1,2. Для клиентов с приложениями, которые используют более старую версию TLS, рекомендуется установить минимальную версию TLS в соответствии с требованиями приложений. Для клиентов, которые используют приложения для подключения с использованием незашифрованного соединения, рекомендуется не задавать минимальную версию TLS. Дополнительные сведения см. в статье [рекомендации по TLS для подключения к базе данных SQL](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

После установки минимальной версии TLS попытки входа от клиентов, которые используют версию TLS меньше, чем минимальная версия TLS сервера, завершатся сбоем со следующей ошибкой:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Установка минимальной версии TLS с помощью PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны. Для следующего скрипта требуется [модуль Azure PowerShell](/powershell/azure/install-az-ps).

В следующем сценарии PowerShell показано, как `Get` и `Set` свойство **минимальной версии TLS** на уровне логического сервера.

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Установка минимальной версии TLS с помощью Azure CLI
> [!IMPORTANT]
> Для всех скриптов в этом разделе требуется [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI в оболочке bash
В следующем сценарии интерфейса командной строки показано, как изменить параметр **минимальной версии протокола TLS** в оболочке bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="connection-policy"></a>Политика подключения
[Политика подключения](sql-database-connectivity-architecture.md#connection-policy) определяет, как клиенты подключаются к Azure SQL Server. 

## <a name="change-connection-policy-via-powershell"></a>Изменение политики подключения с помощью PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны. Для следующего скрипта требуется [модуль Azure PowerShell](/powershell/azure/install-az-ps).

В следующем сценарии PowerShell показано, как изменить политику подключения с помощью PowerShell.

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

## <a name="change-connection-policy-via-azure-cli"></a>Изменение политики подключения с помощью Azure CLI
> [!IMPORTANT]
> Для всех скриптов в этом разделе требуется [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI в оболочке bash
В следующем сценарии интерфейса командной строки показано, как изменить политику подключения в оболочке bash: 

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI из командной строки Windows
В следующем сценарии CLI показано, как изменить политику подключения из командной строки Windows (с Azure CLI установленным).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Дальнейшие действия
- Общие сведения о том, как работает подключение в базе данных SQL Azure, см. в статье [Архитектура подключения SQL Azure](sql-database-connectivity-architecture.md) .
- Дополнительные сведения о том, как изменить политику подключения Базы данных SQL Azure для сервера Базы данных SQL Azure, см. в статье о команде [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
