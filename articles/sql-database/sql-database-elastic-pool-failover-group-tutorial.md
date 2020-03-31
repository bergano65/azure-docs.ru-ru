---
title: 'Учебник: Добавьте эластичный пул в группу неудач'
description: Добавьте эластичный пул базы данных Azure S'L в группу неудач, используя портал Azure, PowerShell или Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: c57f9eed2147504dd7b3313d58468fb76ab40caa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268981"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Учебник: Добавление эластичного пула базы данных Azure S'L в группу неудач

Наверстывай группу неудач для эластичного пула базы данных Azure S'L и тест-неудачу с помощью портала Azure.  В этом учебнике рассматривается следующее.

> [!div class="checklist"]
> - Создайте единую базу данных базы данных Azure S'L.
> - Добавьте единую базу данных в эластичный пул. 
> - Создайте [группу сбоев](sql-database-auto-failover-group.md) для двух эластичных пулов между двумя логическими серверами S'L.
> - Тестирование отработки отказа.

## <a name="prerequisites"></a>Предварительные требования

В рамках этого руководства вам потребуются: 

- Подписка Azure. [Создайте бесплатную учетную запись,](https://azure.microsoft.com/free/) если у вас ее еще нет.


## <a name="1---create-a-single-database"></a>1 - Создание единой базы данных 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 - Добавить единую базу данных в эластичный пул
На этом этапе вы создадите эластичный пул и добавите к нему единую базу данных. 


# <a name="portal"></a>[Портал](#tab/azure-portal)

Создайте свой эластичный пул с помощью портала Azure. 


1. В левом меню портала Azure выберите **Azure S'L.** Если **Azure SQL** отсутствует в списке, выберите **Все службы** и в поле поиска введите Azure SQL. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Щелкните **+ Добавить**, чтобы открыть страницу **Выбор варианта развертывания SQL**. Чтобы просмотреть дополнительные сведения о различных базах данных, выберите Показать сведения на плитке Базы данных.
1. Выберите **упругий пул** из **типа ресурса,** выпадающий в плитке **баз данных S'L.** Выберите **Создать** для создания эластичного пула. 

    ![Выберите эластичный пул](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Наверстируйте упругий пул со следующими значениями:
   - **Имя**: Предоставьте уникальное имя для `myElasticPool`вашего эластичного бассейна, например. 
   - **Подписка**: Выберите подписку из выпадающих.
   - **ResourceGroup**: `myResourceGroup` Выберите из выпадения вниз, группу ресурсов, созданную в разделе 1. 
   - **Сервер**: Выберите сервер, созданный в разделе 1, из выпадения.  

       ![Создание нового сервера для эластичного пула](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Вычислите и накопите:** Выберите **настройку эластичного пула** для настройки вычислений, хранения и добавления единой базы данных в упругий пул. На вкладке **Настройки пула** оставьте по умолчанию Gen5, с 2 vCores и 32gb. 

1. На странице **Настройка** выберите вкладку **Базы данных,** а затем выберите **добавленную базу данных.** Выберите базу данных, созданную в разделе 1, а затем выберите **Apply,** чтобы добавить ее в свой эластичный пул. Выберите **применить** еще раз, чтобы применить ваши упругие настройки пула и закрыть страницу **Настройка.** 

    ![Добавление S'L DB в эластичный бассейн](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Выберите **Обзор и создайте** для просмотра упругих настроек пула, а затем выберите **Создать** для создания эластичного пула. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Создавайте эластичные пулы и вторичный сервер с помощью PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # The ip address range that you want to allow to access your server 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"
   
   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

Эта часть учебника использует следующие PowerShell cmdlets:

| Команда | Примечания |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Создает упругий пул баз данных для базы данных Azure S'L.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Определяет свойства базы данных или перемещает ее в эластичный пул. | 

---

## <a name="3---create-the-failover-group"></a>3 - Создание группы неудач 
На этом этапе вы создадите [группу сбоя](sql-database-auto-failover-group.md) между существующим сервером Azure S'L и новым сервером Azure S'L в другом регионе. Затем добавьте эластичный пул в группу failover. 


# <a name="portal"></a>[Портал](#tab/azure-portal)

Создайте свою группу неудач с помощью портала Azure. 

1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **Azure SQL** отсутствует в списке, выберите **Все службы** и в поле поиска введите Azure SQL. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите эластичный пул, созданный в предыдущем разделе, `myElasticPool`например. 
1. На панели **Обзора** выберите имя сервера под **именем сервера,** чтобы открыть настройки для сервера.
  
    ![Открытый сервер для эластичного пула](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Выберите **группы Failover** под панелью **настроек,** а затем выберите **группу добавления** для создания новой группы неудач. 

    ![Добавление новой группы неудач](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. На странице **Группы Failover** введите или выберите следующие значения, а затем выберите **Создать:**
    - **Имя группы Failover**: Введите уникальное `failovergrouptutorial`имя группы failover, например. 
    - **Вторичный сервер**: Выберите опцию *настройки требуемых настроек,* а затем выберите **создание нового сервера.** Кроме того, вы можете выбрать уже существующий сервер в качестве вторичного сервера. После ввода следующих значений для нового вторичного сервера **выберите.** 
        - **Имя сервера**: Введите уникальное имя `mysqlsecondary`для вторичного сервера, например. 
        - **Логин администратора сервера**: Тип`azureuser`
        - **Пароль**: Введите сложный пароль, отвечающий требованиям пароля.
        - **Расположение**: Выберите место из выпадения `East US`вниз, например . Это место не может быть таким же местом, как ваш основной сервер.

       > [!NOTE]
       > Настройки входа в сервер и брандмауэра должны соответствовать настройкам вашего основного сервера. 
    
       ![Создание вторичного сервера для группы failover](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Выберите **базы данных в группе,** а затем выберите упругий пул, созданный в разделе 2. Должно появиться предупреждение, что побудило вас создать эластичный пул на вторичном сервере. Выберите предупреждение, а затем выберите **OK** для создания эластичного пула на вторичном сервере. 
        
    ![Добавление эластичного пула в группу failover](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Выберите **Select,** чтобы применить параметры эластичного пула к группе failover, а затем выберите **Создать** для создания группы failover. Добавление эластичного пула в группу failover автоматически запустит процесс георепликации.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте свою группу неудач с помощью PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary logical server =" $drServerName
   
   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured" 
   
   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 
   
   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   $failoverGroup
   ```

Эта часть учебника использует следующие PowerShell cmdlets:

| Команда | Примечания |
|---|---|
| [Нью-АзСзлСервер](/powershell/module/az.sql/new-azsqlserver) | Создает сервер Базы данных SQL, на котором размещены отдельные базы данных и эластичные пулы. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Создание правила брандмауэра для логического сервера. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Создает упругий пул баз данных для базы данных Azure S'L.| 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Создает группу отработки отказа. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Добавляет одну или несколько баз данных SQL Azure в группу отработки отказа. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Возвращает или перечисляет группы отработки отказа для Базы данных SQL Azure. |

---


## <a name="4---test-failover"></a>4 - Тест неудачи 
На этом этапе вы сведет е-группу неудачи на вторичный сервер, а затем сбой сможете вернуться с помощью портала Azure. 


# <a name="portal"></a>[Портал](#tab/azure-portal)

Тест-сбой вашей группы неудачи с помощью портала Azure. 

1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **Azure SQL** отсутствует в списке, выберите **Все службы** и в поле поиска введите Azure SQL. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите эластичный пул, созданный в предыдущем разделе, `myElasticPool`например. 
1. Выберите имя сервера под **именем сервера,** чтобы открыть настройки для сервера.

    ![Открытый сервер для эластичного пула](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Выберите **группы Failover** под панелью **настроек,** а затем выберите группу неудач, созданную в разделе 2. 
  
   ![Выберите группу неудач на портале](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Просмотрите, какой сервер является первичным, а какой — вторичным. 
1. Выберите **Failover** из панели задач, чтобы потерпеть неудачу над вашей группой неудач, содержащей упругий пул. 
1. Выберите **«Да»** на предупреждении, уведомивающее вас о том, что сеансы TDS будут отключены. 

   ![Fail над вашей группой failover содержащ вашей базой данных S'L](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Просмотрите, какой сервер является основным, какой сервер является вторичным. Если неудача удалось, два сервера должны были поменяться ролями. 
1. Выберите **Failover** снова, чтобы потерпеть неудачу группы обратно в исходные настройки. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Тест неудача вашей группы failover с помощью PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName 
   ```

Не сбой вашей группы failover к вторичному серверу, а затем сбой обратно с помощью PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary" 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName 
   ```

Эта часть учебника использует следующие PowerShell cmdlets:

| Команда | Примечания |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Возвращает или перечисляет группы отработки отказа для Базы данных SQL Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Выполняет отработку отказа для группы отработки отказа Базы данных SQL Azure. |


---

## <a name="clean-up-resources"></a>Очистка ресурсов 

Очистка ресурсов путем удаляния группы ресурсов. 


# <a name="portal"></a>[Портал](#tab/azure-portal)


1. На [портале Azure](https://portal.azure.com) перейдите к используемой группе ресурсов.
1. Выберите **группу ресурсов Удалить** все ресурсы группы, а также саму группу ресурсов. 
1. Введите имя группы `myResourceGroup`ресурсов, в textbox, а затем выберите **Удалить,** чтобы удалить группу ресурсов. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Очистка ресурсов с помощью PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Эта часть учебника использует следующие PowerShell cmdlet:

| Команда | Примечания |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. | 

---

> [!IMPORTANT]
> Если вы хотите сохранить группу ресурсов, но удалить вторичную базу данных, удалите ее из группы failover, прежде чем удалить ее. Удаление вторичной базы данных до ее удаления из группы failover может привести к непредсказуемому поведению. 

## <a name="full-script"></a>Полный сценарий

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [Нью-АзСзлСервер](/powershell/module/az.sql/new-azsqlserver) | Создает сервер Базы данных SQL, на котором размещены отдельные базы данных и эластичные пулы. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Создание правила брандмауэра для логического сервера. | 
| [Новый-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Создание отдельной базы данных в Базе данных SQL Azure. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Создает упругий пул баз данных для базы данных Azure S'L.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Определяет свойства базы данных или перемещает ее в эластичный пул. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Создает группу отработки отказа. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Возвращает одну или несколько баз данных SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Добавляет одну или несколько баз данных SQL Azure в группу отработки отказа. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Возвращает или перечисляет группы отработки отказа для Базы данных SQL Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Выполняет отработку отказа для группы отработки отказа Базы данных SQL Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. | 


# <a name="portal"></a>[Портал](#tab/azure-portal)
Для портала Azure нет скриптов.

---

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы добавили эластичный пул базы данных Azure S'L в группу неудач, а тестируется неудача. Вы ознакомились с выполнением следующих задач:

> [!div class="checklist"]
> - Создайте единую базу данных базы данных Azure S'L.
> - Добавьте единую базу данных в эластичный пул. 
> - Создайте [группу сбоев](sql-database-auto-failover-group.md) для двух эластичных пулов между двумя логическими серверами S'L.
> - Тестирование отработки отказа.

Перейдите к следующему руководству о миграции с помощью DMS.

> [!div class="nextstepaction"]
> [Учебник: Мигрируйте сервер S'L в объединенную базу данных с помощью DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
