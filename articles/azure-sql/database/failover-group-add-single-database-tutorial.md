---
title: Учебник. Добавление базы данных в группу отработки отказа
description: Добавьте базу данных в базу данных SQL Azure в группу автоперехода на другой ресурс с помощью портал Azure, PowerShell или Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: ad4ef93b34639abffaf15c806fd86d550e03f0bc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87045259"
---
# <a name="tutorial-add-an-azure-sql-database-to-an-autofailover-group"></a>Учебник. Добавление базы данных SQL Azure в группу автоперехода на другой ресурс
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Группа отработки отказа](auto-failover-group-overview.md) — это декларативный слой абстракции, который позволяет группировать несколько геореплицированных баз данных. Узнайте, как настроить группу отработки отказа для базы данных SQL Azure и протестировать отработку отказа с помощью портал Azure, PowerShell или Azure CLI.  Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
>
> - Создание базы данных в службе "База данных SQL Azure"
> - Создайте группу отработки отказа для базы данных между двумя серверами.
> - Тестовая отработка отказа.

## <a name="prerequisites"></a>Обязательные условия

# <a name="the-portal"></a>[Портал](#tab/azure-portal)

В рамках этого руководства вам потребуются:

- Подписка Azure. [Создайте бесплатную учетную запись](https://azure.microsoft.com/free/) , если она еще не создана.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы завершить работу с этим руководством, убедитесь, что у вас есть следующие элементы:

- Подписка Azure. [Создайте бесплатную учетную запись](https://azure.microsoft.com/free/) , если она еще не создана.
- [Azure PowerShell](/powershell/azure/)

# <a name="the-azure-cli"></a>[CLI Azure.](#tab/azure-cli)

Чтобы завершить работу с этим руководством, убедитесь, что у вас есть следующие элементы:

- Подписка Azure. [Создайте бесплатную учетную запись](https://azure.microsoft.com/free/) , если она еще не создана.
- Последняя версия [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

---

## <a name="1---create-a-database"></a>1. Создание базы данных

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2. Создание группы отработки отказа

На этом шаге вы создадите [группу отработки отказа](auto-failover-group-overview.md) между существующим сервером и новым сервером в другом регионе. Затем добавьте пример базы данных в группу отработки отказа.

# <a name="the-portal"></a>[Портал](#tab/azure-portal)

Создайте группу отработки отказа и добавьте в нее базу данных с помощью портал Azure.

1. Выберите **Azure SQL** в меню слева [портал Azure](https://portal.azure.com). Если **Azure SQL** нет в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева.
1. Выберите базу данных, созданную в разделе 1, например `mySampleDatabase` .
1. Группы отработки отказа можно настроить на уровне сервера. Выберите имя сервера в поле **имя сервера** , чтобы открыть параметры для сервера.

   ![Открыть сервер для базы данных](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Выберите **группы отработки отказа** в области **Параметры** , а затем щелкните **Добавить группу** , чтобы создать новую группу отработки отказа.

   ![Добавить новую группу отработки отказа](./media/failover-group-add-single-database-tutorial/sqldb-add-new-failover-group.png)

1. На странице **Группа отработки отказа** введите или выберите следующие значения, а затем щелкните **создать**.

   - **Имя группы отработки отказа**: введите уникальное имя группы отработки отказа, например `failovergrouptutorial` .
   - **Сервер-получатель**: выберите параметр для *настройки обязательных параметров* , а затем выберите **Создание нового сервера**. Кроме того, можно выбрать уже существующий сервер в качестве сервера-получателя. После ввода следующих значений нажмите **кнопку Выбрать**.
      - **Имя сервера**: введите уникальное имя сервера-получателя, например `mysqlsecondary` .
      - **Имя для входа администратора сервера**: введите`azureuser`
      - **Пароль**. Введите сложный пароль, соответствующий требованиям к паролю.
      - **Расположение**: выберите расположение из раскрывающегося списка, например `East US` . Это расположение не может совпадать с расположением основного сервера.

     > [!NOTE]
     > Параметры имени для входа и брандмауэра сервера должны совпадать с именем сервера источника.

     ![Создание сервера-получателя для группы отработки отказа](./media/failover-group-add-single-database-tutorial/create-secondary-failover-server.png)

   - **Базы данных в группе**. После выбора сервера-получателя этот параметр становится незаблокированным. Выберите его, чтобы **выбрать базы данных** , а затем выберите базу данных, созданную в разделе 1. Добавление базы данных в группу отработки отказа автоматически начнет процесс георепликации.

   ![Добавление базы данных SQL в группу отработки отказа](./media/failover-group-add-single-database-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте группу отработки отказа и добавьте в нее базу данных с помощью PowerShell.

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
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
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

| Get-Help | Примечания |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Создает сервер в базе данных SQL Azure, где размещаются отдельные базы данных и эластичные пулы. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Создает правило брандмауэра для сервера в базе данных SQL Azure. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Создает новую отдельную базу данных в базе данных SQL Azure. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Создает новую группу отработки отказа в базе данных SQL Azure. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Возвращает одну или несколько баз данных в базе данных SQL Azure. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Добавляет одну или несколько баз данных в группу отработки отказа в базе данных SQL Azure. |

# <a name="the-azure-cli"></a>[CLI Azure.](#tab/azure-cli)

Создайте группу отработки отказа и добавьте в нее базу данных с помощью Azure CLI.

   > [!NOTE]
   > Параметры имени для входа и брандмауэра сервера должны совпадать с именем сервера источника.

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password

   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

В этой части руководства используются следующие командлеты Azure CLI:

| Get-Help | Примечания |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Создает сервер, на котором размещены базы данных и эластичные пулы. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Создает правила брандмауэра сервера. |
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Создает группу отработки отказа. |

---

## <a name="3---test-failover"></a>3. тестовая отработка отказа

На этом шаге группа отработки отказа перейдет на сервер-получатель, а затем завершится с помощью портал Azure.

# <a name="the-portal"></a>[Портал](#tab/azure-portal)

Тестовая отработка отказа с помощью портал Azure.

1. Выберите **Azure SQL** в меню слева [портал Azure](https://portal.azure.com). Если **Azure SQL** нет в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева.
1. Выберите базу данных, созданную в разделе 2, например `mySampleDatbase` .
1. Выберите имя сервера в поле **имя сервера** , чтобы открыть параметры для сервера.

   ![Открыть сервер для базы данных](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Выберите **группы отработки отказа** в области **Параметры** , а затем выберите группу отработки отказа, созданную в разделе 2.
  
   ![Выбор группы отработки отказа на портале](./media/failover-group-add-single-database-tutorial/select-failover-group.png)

1. Проверьте, какой сервер является основным, а какой — сервером-получателем.
1. Выберите **отработка отказа** в области задач для переключения группы отработки отказа, содержащей образец базы данных.
1. Нажмите кнопку **Да** в предупреждении, уведомляющее о том, что сеансы TDS будут отключены.

   ![Отработка отказа группы перехода на другой ресурс с базой данных](./media/failover-group-add-single-database-tutorial/failover-sql-db.png)

1. Проверьте, какой сервер является основным и какой сервер является вторичным. Если отработка отказа прошла успешно, у двух серверов должны быть переключенные роли.
1. Снова установите флажок **отработка отказа** , чтобы вернуть серверы к исходным ролям.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

| Get-Help | Примечания |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Возвращает или перечисляет группы отработки отказа для Базы данных SQL Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Выполняет отработку отказа для группы отработки отказа Базы данных SQL Azure. |

# <a name="the-azure-cli"></a>[CLI Azure.](#tab/azure-cli)

Тестовая отработка отказа с помощью Azure CLI.

Проверьте, какой сервер является вторичным:

   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Отработка отказа на сервер-получатель:

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

В этой части руководства используются следующие командлеты Azure CLI:

| Get-Help | Примечания |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Перечисляет группы отработки отказа на сервере. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Задает первичную точку группы отработки отказа. Для этого выполняется отработка отказа всех баз данных с текущего сервера-источника. |

---

## <a name="clean-up-resources"></a>Очистка ресурсов

Очистите ресурсы, удалив группу ресурсов.

# <a name="the-portal"></a>[Портал](#tab/azure-portal)

Удалите группу ресурсов с помощью портал Azure.

1. На [портале Azure](https://portal.azure.com) перейдите к используемой группе ресурсов.
1. Выберите **Удалить группу ресурсов** , чтобы удалить все ресурсы в группе, а также саму группу ресурсов.
1. Введите имя группы ресурсов, `myResourceGroup` в текстовом поле и нажмите кнопку **Удалить** , чтобы удалить группу ресурсов.  

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

В этой части руководства используются следующие командлеты PowerShell:

| Get-Help | Примечания |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. |

# <a name="the-azure-cli"></a>[CLI Azure.](#tab/azure-cli)

Удалите группу ресурсов с помощью Azure CLI.

   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

В этой части руководства используются следующие командлеты Azure CLI:

| Get-Help | Примечания |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

---

> [!IMPORTANT]
> Если вы хотите, чтобы группа ресурсов оставалась без базы данных-получателя, удалите ее из группы отработки отказа, прежде чем удалять ее. Удаление базы данных-получателя до ее удаления из группы отработки отказа может привести к непредсказуемому поведению.

## <a name="full-scripts"></a>Полные скрипты

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add database to a failover group")]

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Создает сервер, на котором размещаются отдельные базы данных и эластичные пулы в базе данных SQL Azure. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Создает правило брандмауэра для сервера в базе данных SQL Azure. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Создает новую базу данных в базе данных SQL Azure. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Создает новую группу отработки отказа в базе данных SQL Azure. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Возвращает одну или несколько баз данных в базе данных SQL Azure. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Добавляет одну или несколько баз данных в группу отработки отказа в базе данных SQL Azure. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Возвращает или перечисляет группы отработки отказа в базе данных SQL Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Выполняет отработку отказа группы отработки отказа в базе данных SQL Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов в базе данных SQL Azure.|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add database to a failover group")]

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Позволяет указать подписку в качестве текущей активной подписки. |
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Создает сервер, на котором размещаются отдельные базы данных и эластичные пулы в базе данных SQL Azure. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Создает правила брандмауэра IP на уровне сервера в базе данных SQL Azure. |
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Создает базу данных в базе данных SQL Azure. |
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Создает группу отработки отказа в базе данных SQL Azure. |
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Список групп отработки отказа на сервере в базе данных SQL Azure. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Задает первичную точку группы отработки отказа. Для этого выполняется отработка отказа всех баз данных с текущего сервера-источника. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

# <a name="the-portal"></a>[Портал](#tab/azure-portal)

Для портал Azure нет доступных скриптов.

---

Другие скрипты базы данных SQL Azure можно найти здесь: [Azure PowerShell](powershell-script-content-guide.md) и [Azure CLI](az-cli-script-samples-content-guide.md).

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы добавили базу данных в базу данных SQL Azure в группу отработки отказа и проверили отработку отказа. Вы ознакомились с выполнением следующих задач:

> [!div class="checklist"]
>
> - Создание базы данных в службе "База данных SQL Azure"
> - Создайте группу отработки отказа для базы данных между двумя серверами.
> - Тестовая отработка отказа.

Перейдите к следующему руководству по добавлению эластичного пула в группу отработки отказа.

> [!div class="nextstepaction"]
> [Учебник. Добавление эластичного пула базы данных SQL Azure в группу отработки отказа](failover-group-add-elastic-pool-tutorial.md)
