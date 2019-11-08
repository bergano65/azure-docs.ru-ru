---
title: Учебник. Добавление отдельной базы данных в группу отработки отказа
description: Добавьте одну базу данных SQL Azure в группу отработки отказа с помощью портал Azure, PowerShell или Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 6e3b4be836699cc200d30168c14462f81136646b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821101"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Учебник. Добавление отдельной базы данных SQL Azure в группу отработки отказа

Настройте группу отработки отказа для отдельной базы данных SQL Azure и тестовую отработку отказа с помощью портал Azure, PowerShell или Azure CLI.  Из этого учебника вы узнаете следующее:

> [!div class="checklist"]
> - Создайте отдельную базу данных SQL Azure.
> - Создайте [группу отработки отказа](sql-database-auto-failover-group.md) для отдельной базы данных между двумя логическими серверами SQL.
> - Тестовая отработка отказа.

## <a name="prerequisites"></a>Предварительные требования

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)
В рамках этого руководства вам потребуются: 

- Подписка Azure. [Создайте бесплатную учетную запись](https://azure.microsoft.com/free/) , если она еще не создана.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Чтобы завершить работу с этим руководством, убедитесь, что у вас есть следующие элементы:

- Подписка Azure. [Создайте бесплатную учетную запись](https://azure.microsoft.com/free/) , если она еще не создана.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)
Чтобы завершить работу с этим руководством, убедитесь, что у вас есть следующие элементы:

- Подписка Azure. [Создайте бесплатную учетную запись](https://azure.microsoft.com/free/) , если она еще не создана.
- Последняя версия [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1\. Создание отдельной базы данных 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2\. Создание группы отработки отказа 
На этом шаге вы создадите [группу отработки отказа](sql-database-auto-failover-group.md) между существующим сервером Azure SQL Server и новым сервером Azure SQL Server в другом регионе. Затем добавьте пример базы данных в группу отработки отказа. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)
Создайте группу отработки отказа и добавьте в нее отдельную базу данных с помощью портал Azure. 

1. На **портале Azure** в меню слева выберите [Azure SQL](https://portal.azure.com). Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите одну базу данных, созданную в разделе 1, например `mySampleDatabase`. 
1. Выберите имя сервера в поле **имя сервера** , чтобы открыть параметры для сервера.

   ![Открыть сервер для отдельной базы данных](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Выберите **группы отработки отказа** в области **Параметры** , а затем щелкните **Добавить группу** , чтобы создать новую группу отработки отказа. 

    ![Добавить новую группу отработки отказа](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. На странице **Группа отработки отказа** введите или выберите следующие значения, а затем щелкните **создать**.
    - **Имя группы отработки отказа**: введите уникальное имя группы отработки отказа, например `failovergrouptutorial`. 
    - **Сервер-получатель**: выберите параметр для *настройки обязательных параметров* , а затем выберите **Создание нового сервера**. Кроме того, можно выбрать уже существующий сервер в качестве сервера-получателя. После ввода следующих значений нажмите **кнопку Выбрать**. 
        - **Имя сервера**: введите уникальное имя сервера-получателя, например `mysqlsecondary`. 
        - **Имя для входа администратора сервера**: введите `azureuser`
        - **Пароль**. Введите сложный пароль, соответствующий требованиям к паролю.
        - **Расположение**: выберите расположение из раскрывающегося списка, например `East US`. Это расположение не может совпадать с расположением основного сервера.

    > [!NOTE]
    > Параметры имени для входа и брандмауэра сервера должны совпадать с именем сервера источника. 
    
      ![Создание сервера-получателя для группы отработки отказа](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Базы данных в группе**. После выбора сервера-получателя этот параметр становится незаблокированным. Выберите его, чтобы **выбрать базы данных** , а затем выберите базу данных, созданную в разделе 1. Добавление базы данных в группу отработки отказа автоматически начнет процесс георепликации. 
        
    ![Добавление базы данных SQL в группу отработки отказа](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Создайте группу отработки отказа и добавьте в нее отдельную базу данных с помощью PowerShell. 

   > [!NOTE]
   > Параметры имени для входа и брандмауэра сервера должны совпадать с именем сервера источника. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule   
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

В этой части руководства используются следующие командлеты PowerShell:

| Команда | Примечания |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Создает сервер Базы данных SQL, на котором размещены отдельные базы данных и эластичные пулы. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Создание правила брандмауэра для логического сервера. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Создание отдельной базы данных в Базе данных SQL Azure. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Создает группу отработки отказа. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Возвращает одну или несколько баз данных SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Добавляет одну или несколько баз данных SQL Azure в группу отработки отказа. |

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)
Создайте группу отработки отказа и добавьте в нее отдельную базу данных с помощью команды AZ CLI. 

   > [!NOTE]
   > Параметры имени для входа и брандмауэра сервера должны совпадать с именем сервера источника. 

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   # subscriptionID=<SubscriptionID>
   # resourceGroupName=myResourceGroup-$RANDOM
   # location=SouthCentralUS
   # adminLogin=azureuser
   # password="PWD27!"+`openssl rand -base64 18`
   # serverName=mysqlserver-$RANDOM
   # databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $drServerName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --add-db $databaseName
      --failover-policy Automatic
   ```

В этой части руководства используются следующие командлеты AZ CLI:

| Команда | Примечания |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Создает сервер Базы данных SQL, на котором размещены отдельные базы данных и эластичные пулы. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Создает правила брандмауэра сервера. | 
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Создает группу отработки отказа. | 

---

## <a name="3---test-failover"></a>3\. тестовая отработка отказа 
На этом шаге группа отработки отказа будет передаваться на сервер-получатель, а затем восстановлена с помощью портал Azure. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)
Тестовая отработка отказа с помощью портал Azure. 

1. На **портале Azure** в меню слева выберите [Azure SQL](https://portal.azure.com). Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите одну базу данных, созданную в разделе 2, например `mySampleDatbase`. 
1. Выберите имя сервера в поле **имя сервера** , чтобы открыть параметры для сервера.

   ![Открыть сервер для отдельной базы данных](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Выберите **группы отработки отказа** в области **Параметры** , а затем выберите группу отработки отказа, созданную в разделе 2. 
  
   ![Выбор группы отработки отказа на портале](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Проверьте, какой сервер является основным, а какой — сервером-получателем. 
1. Выберите **отработка отказа** в области задач для отработки отказа группы отработки отказа, содержащей пример отдельной базы данных 
1. Нажмите кнопку **Да** в предупреждении, уведомляющее о том, что сеансы TDS будут отключены. 

   ![Переключение группы отработки отказа, содержащей базу данных SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Проверьте, какой сервер является основным и какой сервер является вторичным. Если отработка отказа завершилась успешно, два сервера должны иметь переключенные роли. 
1. Снова установите флажок **отработка отказа** , чтобы вернуть серверы к исходным ролям. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Тестовая отработка отказа с помощью PowerShell. 


Проверьте роль вторичной реплики: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Отработка отказа на сервер-получатель: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName 
   ```

Восстановите группу отработки отказа на основной сервер:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

В этой части руководства используются следующие командлеты PowerShell:

| Команда | Примечания |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Возвращает или перечисляет группы отработки отказа для Базы данных SQL Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Выполняет отработку отказа для группы отработки отказа Базы данных SQL Azure. |



# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)
Тестовая отработка отказа с помощью AZ CLI. 

Проверьте, какой сервер является вторичным:

   
   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

Отработка отказа на сервер-получатель: 

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # drServerName=mysqlsecondary-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

Восстановите группу отработки отказа на основной сервер:

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

В этой части руководства используются следующие командлеты AZ CLI:

| Команда | Примечания |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Перечисляет группы отработки отказа на сервере. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Задает первичную точку группы отработки отказа. Для этого выполняется отработка отказа всех баз данных с текущего сервера-источника. | 

---

## <a name="clean-up-resources"></a>Очистка ресурсов 
Очистите ресурсы, удалив группу ресурсов. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)
Удалите группу ресурсов с помощью портал Azure. 

1. Перейдите к группе ресурсов в [портал Azure](https://portal.azure.com).
1. Выберите **Удалить группу ресурсов** , чтобы удалить все ресурсы в группе, а также саму группу ресурсов. 
1. Введите имя группы ресурсов, `myResourceGroup`, в текстовом поле и нажмите кнопку **Удалить** , чтобы удалить группу ресурсов.  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Удалите группу ресурсов с помощью PowerShell. 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

В этой части руководства используются следующие командлеты PowerShell:

| Команда | Примечания |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. | 

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Удалите группу ресурсов с помощью команды AZ CLI. 


   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   
   # Clean up resources by removing the resource group
   echo "Cleaning up resources by removing the resource group..."
   az group delete \
     --name $resourceGroupName
   echo "Successfully removed resource group" $resourceGroupName
   ```

В этой части руководства используются следующие командлеты AZ CLI:

| Команда | Примечания |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

---


## <a name="full-scripts"></a>Полные скрипты

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Создает сервер Базы данных SQL, на котором размещены отдельные базы данных и эластичные пулы. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Создание правила брандмауэра для логического сервера. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Создание отдельной базы данных в Базе данных SQL Azure. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Создает группу отработки отказа. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Возвращает одну или несколько баз данных SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Добавляет одну или несколько баз данных SQL Azure в группу отработки отказа. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Возвращает или перечисляет группы отработки отказа для Базы данных SQL Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Выполняет отработку отказа для группы отработки отказа Базы данных SQL Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. | 

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Позволяет указать подписку в качестве текущей активной подписки. | 
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Создает сервер Базы данных SQL, на котором размещены отдельные базы данных и эластичные пулы. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Создает правила брандмауэра сервера. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Создает базу данных. | 
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Создает группу отработки отказа. | 
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Перечисляет группы отработки отказа на сервере. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Задает первичную точку группы отработки отказа. Для этого выполняется отработка отказа всех баз данных с текущего сервера-источника. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)
Для портал Azure нет доступных скриптов. 
 
---

Другие скрипты базы данных SQL Azure можно найти здесь: [Azure PowerShell](sql-database-powershell-samples.md) и [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы добавили одну базу данных SQL Azure в группу отработки отказа и проверили отработку отказа. Вы научились выполнять следующие задачи: 

> [!div class="checklist"]
> - Создайте отдельную базу данных SQL Azure. 
> - Создайте [группу отработки отказа](sql-database-auto-failover-group.md) для отдельной базы данных между двумя логическими серверами SQL.
> - Тестовая отработка отказа.

Перейдите к следующему руководству по добавлению эластичного пула в группу отработки отказа. 

> [!div class="nextstepaction"]
> [Учебник. Добавление эластичного пула базы данных SQL Azure в группу отработки отказа](sql-database-elastic-pool-failover-group-tutorial.md)
