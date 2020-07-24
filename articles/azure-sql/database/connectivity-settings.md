---
title: Параметры подключения для базы данных и хранилища данных SQL Azure
description: В этом документе объясняется выбор версии протокола TLS и настройка прокси-сервера и перенаправления для базы данных SQL Azure и Azure синапсе Analytics.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/06/2020
ms.openlocfilehash: 6297f7797758069ee75fe14960d72f9c497b0fe6
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132982"
---
# <a name="azure-sql-connectivity-settings"></a>Параметры подключения к SQL Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

В этой статье описываются параметры, управляющие подключением к серверу для базы данных SQL Azure и Azure синапсе Analytics. Эти параметры применяются ко **всем** базам данных SQL и Azure синапсе, связанным с сервером.

> [!IMPORTANT]
> Эта статья *не* относится к **Azure SQL управляемый экземпляр**

Параметры подключения доступны на экране **брандмауэры и виртуальные сети** , как показано на следующем снимке экрана:

 ![Снимок экрана параметров подключения][1]

> [!NOTE]
> После применения этих параметров они вступают в **силу немедленно** и могут привести к утрате подключения для клиентов, если они не соответствуют требованиям для каждого параметра.

## <a name="deny-public-network-access"></a>Запретить доступ к общедоступной сети

Если параметр **запрета доступа к общедоступной сети** имеет значение **Да**, разрешены только подключения через частные конечные точки. Если для этого параметра задано значение **нет** (по умолчанию), клиенты могут подключаться с помощью общедоступных конечных точек (правила брандмауэра на основе IP-адресов, правила брандмауэра на основе виртуальной сети) или частных конечных точек (с использованием частной ссылки), как описано в статье [Обзор доступа к сети](network-access-controls-overview.md). 

 ![Снимок экрана подключения с запретом доступа к общедоступной сети][2]

Любая попытка задать для параметра **запретить общий доступ к сети** значение **Да** без каких бы то ни было существующих частных конечных точек на логическом сервере завершится с сообщением об ошибке следующего вида:  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server. 
Please set up private endpoints and retry the operation. 
```

Если параметр **запрета доступа к общедоступной сети** имеет значение **Да**, разрешены только подключения через частные конечные точки, а все подключения через общедоступные конечные точки отклоняются с сообщением об ошибке следующего вида:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Если параметр **запретить доступ к общедоступной сети** имеет значение **Да**, любые попытки добавления или обновления правил брандмауэра будут отклонены с сообщением об ошибке следующего вида:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Изменение доступа к общедоступной сети с помощью PowerShell

> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны. Для следующего скрипта требуется [модуль Azure PowerShell](/powershell/azure/install-az-ps).

В следующем сценарии PowerShell показано, как `Get` и `Set` свойство **доступа к общедоступной сети** на уровне сервера.

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

Параметр минимальной версии протокола [TLS](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) позволяет клиентам управлять версией TLS, используемой базой данных SQL Azure.

В настоящее время мы поддерживаем TLS 1,0, 1,1 и 1,2. Установка минимальной версии TLS гарантирует, что будут поддерживаться следующие более новые версии TLS. Например, вы выбрали версию TLS, превышающую 1,1. означает, что принимаются только соединения с TLS 1,1 и 1,2, и TLS 1,0 отклоняется. После тестирования, чтобы убедиться, что приложения поддерживаются, рекомендуется установить минимальную версию TLS 1,2, так как она включает исправления уязвимостей, обнаруженных в предыдущих версиях, и является самой высокой версией протокола TLS, поддерживаемой в базе данных SQL Azure.

> [!IMPORTANT]
> Значение по умолчанию для минимальной версии TLS — разрешить все версии. Однако после применения версии TLS восстановить значение по умолчанию невозможно.

Для клиентов с приложениями, которые используют более старые версии TLS, рекомендуется установить минимальную версию TLS в соответствии с требованиями приложений. Для клиентов, которые используют приложения для подключения с использованием незашифрованного соединения, рекомендуется не задавать минимальную версию TLS.

Дополнительные сведения см. в статье [рекомендации по TLS для подключения к базе данных SQL](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

После установки минимальной версии TLS попытки входа от клиентов, использующих версию TLS ниже минимальной версии сервера, будут завершаться сбоем со следующей ошибкой:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Установка минимальной версии TLS с помощью PowerShell

> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны. Для следующего скрипта требуется [модуль Azure PowerShell](/powershell/azure/install-az-ps).

В следующем сценарии PowerShell показано, как `Get` и `Set` свойство **минимальной версии TLS** на уровне логического сервера.

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# # Update Minimal TLS Version to 1.2
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


## <a name="change-connection-policy"></a>Изменение политики подключения

[Политика подключения](connectivity-architecture.md#connection-policy) определяет способ подключения клиентов к базе данных SQL Azure.


## <a name="change-connection-policy-via-powershell"></a>Изменение политики подключения с помощью PowerShell

> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны. Для следующего скрипта требуется [модуль Azure PowerShell](/powershell/azure/install-az-ps).

В следующем сценарии PowerShell показано, как изменить политику подключения с помощью PowerShell.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

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

- Общие сведения о том, как работает подключение в базе данных SQL Azure, см. в статье [Архитектура подключения](connectivity-architecture.md) .
- Сведения о том, как изменить политику подключения для сервера, см. в разделе [conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
