---
title: 'Учебник: Добавьте единую базу данных в группу неудач'
description: Добавьте единую базу данных базы данных Azure S'L в группу сбоев с помощью портала Azure, PowerShell или Azure CLI.
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
ms.openlocfilehash: c5ce6a1c2f231d372a2a8113eb9043a236090388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061695"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Учебник: Добавление единой базы данных базы данных Azure S'L в группу сбоя

[Группа failover](sql-database-auto-failover-group.md) — это декларативный слой абстракции, позволяющий группировать георепкопированные базы данных mulitple. Научитесь настраивать группу неудач для единой базы данных Azure S'L и тестировать сбой с помощью портала Azure, PowerShell или Azure CLI.  В этом учебнике рассматривается следующее.

> [!div class="checklist"]
> - Создайте единую базу данных базы данных Azure S'L.
> - Создайте группу сбоев для единой базы данных между двумя логическими серверами S'L.
> - Тестирование отработки отказа.

## <a name="prerequisites"></a>Предварительные требования

# <a name="portal"></a>[Портал](#tab/azure-portal)
В рамках этого руководства вам потребуются: 

- Подписка Azure. [Создайте бесплатную учетную запись,](https://azure.microsoft.com/free/) если у вас ее еще нет.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Чтобы завершить обучение, убедитесь, что у вас есть следующие элементы:

- Подписка Azure. [Создайте бесплатную учетную запись,](https://azure.microsoft.com/free/) если у вас ее еще нет.
- [Лазурная силаШелл](/powershell/azureps-cmdlets-docs)


# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)
Чтобы завершить обучение, убедитесь, что у вас есть следующие элементы:

- Подписка Azure. [Создайте бесплатную учетную запись,](https://azure.microsoft.com/free/) если у вас ее еще нет.
- Последняя версия [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1 - Создание единой базы данных 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - Создание группы неудач 
На этом этапе вы создадите [группу сбоя](sql-database-auto-failover-group.md) между существующим сервером Azure S'L и новым сервером Azure S'L в другом регионе. Затем добавьте пример базы данных в группу отработки отказа. 

# <a name="portal"></a>[Портал](#tab/azure-portal)
Создайте свою группу неудач и добавьте к ней единую базу данных с помощью портала Azure. 

1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **Azure SQL** отсутствует в списке, выберите **Все службы** и в поле поиска введите Azure SQL. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите единую базу данных, `mySampleDatabase`созданную в разделе 1, например. 
1. Группы Failover могут быть настроены на уровне сервера. Выберите имя сервера под **именем сервера,** чтобы открыть настройки для сервера.

   ![Открытый сервер для одного db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Выберите **группы Failover** под панелью **настроек,** а затем выберите **группу добавления** для создания новой группы неудач. 

    ![Добавление новой группы неудач](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. На странице **Группы Failover** введите или выберите следующие значения, а затем выберите **Создать:**
    - **Имя группы Failover**: Введите уникальное `failovergrouptutorial`имя группы failover, например. 
    - **Вторичный сервер**: Выберите опцию *настройки требуемых настроек,* а затем выберите **создание нового сервера.** Кроме того, вы можете выбрать уже существующий сервер в качестве вторичного сервера. После ввода следующих значений **выберите**. 
        - **Имя сервера**: Введите уникальное имя `mysqlsecondary`для вторичного сервера, например. 
        - **Логин администратора сервера**: Тип`azureuser`
        - **Пароль**: Введите сложный пароль, отвечающий требованиям пароля.
        - **Расположение**: Выберите место из выпадения `East US`вниз, например . Это место не может быть таким же местом, как ваш основной сервер.

    > [!NOTE]
    > Настройки входа в сервер и брандмауэра должны соответствовать настройкам вашего основного сервера. 
    
      ![Создание вторичного сервера для группы failover](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Базы данных в группе:** После выбора вторичного сервера эта опция становится разблокированной. Выберите его для **выбора баз данных, чтобы добавить,** а затем выбрать базу данных, созданную в разделе 1. Добавление базы данных в группу failover автоматически запустит процесс георепликации. 
        
    ![Добавьте S'L DB в группу неудач](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Создайте свою группу неудач и добавьте к ней единую базу данных с помощью PowerShell. 

   > [!NOTE]
   > Настройки входа в сервер и брандмауэра должны соответствовать настройкам вашего основного сервера. 

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

Эта часть учебника использует следующие PowerShell cmdlets:

| Команда | Примечания |
|---|---|
| [Нью-АзСзлСервер](/powershell/module/az.sql/new-azsqlserver) | Создает сервер Базы данных SQL, на котором размещены отдельные базы данных и эластичные пулы. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Создание правила брандмауэра для логического сервера. | 
| [Новый-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Создание отдельной базы данных в Базе данных SQL Azure. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Создает группу отработки отказа. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Возвращает одну или несколько баз данных SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Добавляет одну или несколько баз данных SQL Azure в группу отработки отказа. |

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)
Создайте свою группу неудачи и добавьте к ней единую базу данных с помощью A CLI. 

   > [!NOTE]
   > Настройки входа в сервер и брандмауэра должны соответствовать настройкам вашего основного сервера. 

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary logical server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password
   
   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

Эта часть учебника использует следующие Az CLI cmdlets:

| Команда | Примечания |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Создает сервер Базы данных SQL, на котором размещены отдельные базы данных и эластичные пулы. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Создает правила брандмауэра сервера. | 
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Создает группу отработки отказа. | 

---

## <a name="3---test-failover"></a>3 - Тест неудачи 
На этом этапе вы сведет е-группу неудачи на вторичный сервер, а затем сбой сможете вернуться с помощью портала Azure. 

# <a name="portal"></a>[Портал](#tab/azure-portal)
Тест-неудача с помощью портала Azure. 

1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **Azure SQL** отсутствует в списке, выберите **Все службы** и в поле поиска введите Azure SQL. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите единую базу данных, `mySampleDatbase`созданную в разделе 2, например. 
1. Выберите имя сервера под **именем сервера,** чтобы открыть настройки для сервера.

   ![Открытый сервер для одного db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Выберите **группы Failover** под панелью **настроек,** а затем выберите группу неудач, созданную в разделе 2. 
  
   ![Выберите группу неудач на портале](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Просмотрите, какой сервер является первичным, а какой — вторичным. 
1. Выберите **Failover** из панели задач, чтобы потерпеть неудачу в вашей группе неудач, содержащей одну выборку базы данных. 
1. Выберите **«Да»** на предупреждении, уведомивающее вас о том, что сеансы TDS будут отключены. 

   ![Fail над вашей группой failover содержащ вашей базой данных S'L](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Просмотрите, какой сервер теперь является основным, а какой — вторичным. Если неудача закончилась, два сервера должны были поменяться ролями. 
1. Выберите **Failover** снова, чтобы выйти из строя серверов обратно в свои первоначально роли. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Тест неудача с помощью PowerShell. 


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

Не сбой на вторичном сервере: 

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

Эта часть учебника использует следующие PowerShell cmdlets:

| Команда | Примечания |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Возвращает или перечисляет группы отработки отказа для Базы данных SQL Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Выполняет отработку отказа для группы отработки отказа Базы данных SQL Azure. |



# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)
Сбой теста с использованием АК CLI. 

Проверьте, какой сервер является вторичным:

   
   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Не сбой на вторичном сервере: 

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Восстановите группу отработки отказа на основной сервер:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

Эта часть учебника использует следующие Az CLI cmdlets:

| Команда | Примечания |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Перечисляет группы отработки отказа на сервере. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Задает первичную точку группы отработки отказа. Для этого выполняется отработка отказа всех баз данных с текущего сервера-источника. | 

---

## <a name="clean-up-resources"></a>Очистка ресурсов 
Очистка ресурсов путем удаляния группы ресурсов. 

# <a name="portal"></a>[Портал](#tab/azure-portal)
Удалите группу ресурсов с помощью портала Azure. 

1. На [портале Azure](https://portal.azure.com) перейдите к используемой группе ресурсов.
1. Выберите **группу ресурсов Удалить** все ресурсы группы, а также саму группу ресурсов. 
1. Введите имя группы `myResourceGroup`ресурсов, в textbox, а затем выберите **Удалить,** чтобы удалить группу ресурсов.  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Удалите группу ресурсов с помощью PowerShell. 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Эта часть учебника использует следующие PowerShell cmdlets:

| Команда | Примечания |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. | 

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Удалите группу ресурсов с помощью A CLI. 


   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

Эта часть учебника использует следующие Az CLI cmdlets:

| Команда | Примечания |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

---


> [!IMPORTANT]
> Если вы хотите сохранить группу ресурсов, но удалить вторичную базу данных, удалите ее из группы failover, прежде чем удалить ее. Удаление вторичной базы данных до ее удаления из группы failover может привести к непредсказуемому поведению. 


## <a name="full-scripts"></a>Полные скрипты

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [Нью-АзСзлСервер](/powershell/module/az.sql/new-azsqlserver) | Создает сервер Базы данных SQL, на котором размещены отдельные базы данных и эластичные пулы. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Создание правила брандмауэра для логического сервера. | 
| [Новый-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Создание отдельной базы данных в Базе данных SQL Azure. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Создает группу отработки отказа. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Возвращает одну или несколько баз данных SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Добавляет одну или несколько баз данных SQL Azure в группу отработки отказа. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Возвращает или перечисляет группы отработки отказа для Базы данных SQL Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Выполняет отработку отказа для группы отработки отказа Базы данных SQL Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. | 

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

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

# <a name="portal"></a>[Портал](#tab/azure-portal)
Для портала Azure нет скриптов. 
 
---

Вы можете найти другие скрипты базы данных Azure S'L здесь: [Azure PowerShell](sql-database-powershell-samples.md) и [Azure CLI.](sql-database-cli-samples.md) 

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы добавили единую базу данных базы данных Azure S'L в группу неудачников и протестировали неудачу. Вы ознакомились с выполнением следующих задач: 

> [!div class="checklist"]
> - Создайте единую базу данных базы данных Azure S'L. 
> - Создайте [группу сбоев](sql-database-auto-failover-group.md) для единой базы данных между двумя логическими серверами S'L.
> - Тестирование отработки отказа.

Заранее к следующему учебнику о том, как добавить ваш эластичный бассейн в группу failover. 

> [!div class="nextstepaction"]
> [Учебник: Добавление эластичного пула базы данных Azure S'L в группу неудач](sql-database-elastic-pool-failover-group-tutorial.md)
