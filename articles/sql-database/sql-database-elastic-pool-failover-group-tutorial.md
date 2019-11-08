---
title: Учебник. Добавление эластичного пула в группу отработки отказа
description: Добавьте эластичный пул базы данных SQL Azure в группу отработки отказа с помощью портал Azure, PowerShell или Azure CLI.
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
ms.openlocfilehash: e2ae9afaf7c1dcc1794b90d4851fdd60298b5ad6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823879"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Учебник. Добавление эластичного пула базы данных SQL Azure в группу отработки отказа

Настройте группу отработки отказа для эластичного пула базы данных SQL Azure и тестовую отработку отказа с помощью портал Azure.  Из этого учебника вы узнаете следующее:

> [!div class="checklist"]
> - Создайте отдельную базу данных SQL Azure.
> - Добавьте отдельную базу данных в эластичный пул. 
> - Создайте [группу отработки отказа](sql-database-auto-failover-group.md) для двух эластичных пулов между двумя логическими серверами SQL.
> - Тестовая отработка отказа.

## <a name="prerequisites"></a>Предварительные требования

В рамках этого руководства вам потребуются: 

- Подписка Azure. [Создайте бесплатную учетную запись](https://azure.microsoft.com/free/) , если она еще не создана.


## <a name="1---create-a-single-database"></a>1\. Создание отдельной базы данных 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2\. Добавление отдельной базы данных в эластичный пул
На этом шаге вы создадите эластичный пул и добавите в него отдельную базу данных. 


# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Создайте эластичный пул с помощью портал Azure. 


1. На портале Azure в меню слева выберите **Azure SQL**. Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Щелкните **+ Добавить**, чтобы открыть страницу **Выбор варианта развертывания SQL**. Чтобы просмотреть дополнительные сведения о различных базах данных, выберите Показать сведения в плитке базы данных.
1. Выберите **эластичный пул** в раскрывающемся списке **тип ресурса** на плитке **базы данных SQL** . Выберите **создать** , чтобы создать пул эластичных БД. 

    ![Выбор эластичного пула](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Настройте эластичный пул со следующими значениями:
   - **Имя**: Укажите уникальное имя для эластичного пула, например `myElasticPool`. 
   - **Подписка**. Выберите подписку в раскрывающемся списке.
   - **ResourceGroup**: выберите `myResourceGroup` в раскрывающемся списке Группа ресурсов, созданная в разделе 1. 
   - **Сервер**. Выберите сервер, созданный в разделе 1, из раскрывающегося списка.  

       ![Создание нового сервера для эластичного пула](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Вычисление и хранение**. Выберите **Настройка эластичного пула** для настройки вычислений, хранения и добавления отдельной базы данных в эластичный пул. На вкладке **параметры пула** оставьте значение по умолчанию го поколения, равное 2 виртуальных ядер и 32 ГБ. 

1. На странице **Настройка** перейдите на вкладку **базы данных** , а затем выберите **Добавить базу данных**. Выберите базу данных, созданную в разделе 1, а затем нажмите кнопку **Применить** , чтобы добавить ее в эластичный пул. Нажмите кнопку **Применить** еще раз, чтобы применить параметры эластичного пула, и закройте страницу **Настройка** . 

    ![Добавление базы данных SQL в эластичный пул](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Выберите **проверить и создать** , чтобы проверить параметры эластичного пула, а затем щелкните **создать** , чтобы создать пул эластичных БД. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Создайте эластичные пулы и сервер-получатель с помощью PowerShell. 

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

В этой части руководства используются следующие командлеты PowerShell:

| Команда | Примечания |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Создает пул эластичных баз данных для базы данных SQL Azure.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Определяет свойства базы данных или перемещает ее в эластичный пул. | 

---

## <a name="3---create-the-failover-group"></a>3\. Создание группы отработки отказа 
На этом шаге вы создадите [группу отработки отказа](sql-database-auto-failover-group.md) между существующим сервером Azure SQL Server и новым сервером Azure SQL Server в другом регионе. Затем добавьте эластичный пул в группу отработки отказа. 


# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Создайте группу отработки отказа с помощью портал Azure. 

1. На **портале Azure** в меню слева выберите [Azure SQL](https://portal.azure.com). Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите эластичный пул, созданный в предыдущем разделе, например `myElasticPool`. 
1. В области **Обзор** выберите имя сервера в поле **имя сервера** , чтобы открыть параметры для сервера.
  
    ![Открытие сервера для эластичного пула](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Выберите **группы отработки отказа** в области **Параметры** , а затем щелкните **Добавить группу** , чтобы создать новую группу отработки отказа. 

    ![Добавить новую группу отработки отказа](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. На странице **Группа отработки отказа** введите или выберите следующие значения, а затем щелкните **создать**.
    - **Имя группы отработки отказа**: введите уникальное имя группы отработки отказа, например `failovergrouptutorial`. 
    - **Сервер-получатель**: выберите параметр для *настройки обязательных параметров* , а затем выберите **Создание нового сервера**. Кроме того, можно выбрать уже существующий сервер в качестве сервера-получателя. После ввода следующих значений для нового сервера-получателя нажмите **кнопку Выбрать**. 
        - **Имя сервера**: введите уникальное имя сервера-получателя, например `mysqlsecondary`. 
        - **Имя для входа администратора сервера**: введите `azureuser`
        - **Пароль**. Введите сложный пароль, соответствующий требованиям к паролю.
        - **Расположение**: выберите расположение из раскрывающегося списка, например `East US`. Это расположение не может совпадать с расположением основного сервера.

       > [!NOTE]
       > Параметры имени для входа и брандмауэра сервера должны совпадать с именем сервера источника. 
    
       ![Создание сервера-получателя для группы отработки отказа](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Выберите **базы данных в группе** , а затем выберите эластичный пул, созданный в разделе 2. Должно появиться предупреждение, предлагающее создать эластичный пул на сервере-получателе. Выберите предупреждение и нажмите кнопку **ОК** , чтобы создать эластичный пул на сервере-получателе. 
        
    ![Добавление эластичного пула в группу отработки отказа](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Выберите **выбрать** , чтобы применить параметры эластичного пула к группе отработки отказа, а затем щелкните **создать** , чтобы создать группу отработки отказа. Добавление эластичного пула в группу отработки отказа автоматически начнет процесс георепликации.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте группу отработки отказа с помощью PowerShell. 

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

В этой части руководства используются следующие командлеты PowerShell:

| Команда | Примечания |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Создает сервер Базы данных SQL, на котором размещены отдельные базы данных и эластичные пулы. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Создание правила брандмауэра для логического сервера. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Создает пул эластичных баз данных для базы данных SQL Azure.| 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Создает группу отработки отказа. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Добавляет одну или несколько баз данных SQL Azure в группу отработки отказа. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Возвращает или перечисляет группы отработки отказа для Базы данных SQL Azure. |

---


## <a name="4---test-failover"></a>4\. тестовая отработка отказа 
На этом шаге группа отработки отказа будет передаваться на сервер-получатель, а затем восстановлена с помощью портал Azure. 


# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Тестовая отработка отказа группы отработки отказа с помощью портал Azure. 

1. На **портале Azure** в меню слева выберите [Azure SQL](https://portal.azure.com). Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите эластичный пул, созданный в предыдущем разделе, например `myElasticPool`. 
1. Выберите имя сервера в поле **имя сервера** , чтобы открыть параметры для сервера.

    ![Открытие сервера для эластичного пула](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Выберите **группы отработки отказа** в области **Параметры** , а затем выберите группу отработки отказа, созданную в разделе 2. 
  
   ![Выбор группы отработки отказа на портале](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Проверьте, какой сервер является основным, а какой — сервером-получателем. 
1. Выберите **отработка отказа** в области задач для переключения группы отработки отказа, содержащей эластичный пул. 
1. Нажмите кнопку **Да** в предупреждении, уведомляющее о том, что сеансы TDS будут отключены. 

   ![Переключение группы отработки отказа, содержащей базу данных SQL](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Проверьте, какой сервер является основным, какой сервер является вторичным. Если отработка отказа прошла успешно, у двух серверов должны быть переключенные роли. 
1. Снова установите флажок **отработка отказа** , чтобы вернуть группу отработки отказа к исходным параметрам. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Тестовая отработка отказа группы отработки отказа с помощью PowerShell. 

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

Завершите работу группы отработки отказа на сервере-получателе, а затем выполните аварийное завершение работы с помощью PowerShell. 

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

В этой части руководства используются следующие командлеты PowerShell:

| Команда | Примечания |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Возвращает или перечисляет группы отработки отказа для Базы данных SQL Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Выполняет отработку отказа для группы отработки отказа Базы данных SQL Azure. |


---

## <a name="clean-up-resources"></a>Очистка ресурсов 

Очистите ресурсы, удалив группу ресурсов. 


# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)


1. Перейдите к группе ресурсов в [портал Azure](https://portal.azure.com).
1. Выберите **Удалить группу ресурсов** , чтобы удалить все ресурсы в группе, а также саму группу ресурсов. 
1. Введите имя группы ресурсов, `myResourceGroup`, в текстовом поле и нажмите кнопку **Удалить** , чтобы удалить группу ресурсов. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Очистите ресурсы с помощью PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```
---

В этой части руководства используется следующий командлет PowerShell:

| Команда | Примечания |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. | 

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

## <a name="full-script"></a>Полный сценарий

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Создает сервер Базы данных SQL, на котором размещены отдельные базы данных и эластичные пулы. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Создание правила брандмауэра для логического сервера. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Создание отдельной базы данных в Базе данных SQL Azure. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Создает пул эластичных баз данных для базы данных SQL Azure.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Определяет свойства базы данных или перемещает ее в эластичный пул. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Создает группу отработки отказа. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Возвращает одну или несколько баз данных SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Добавляет одну или несколько баз данных SQL Azure в группу отработки отказа. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Возвращает или перечисляет группы отработки отказа для Базы данных SQL Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Выполняет отработку отказа для группы отработки отказа Базы данных SQL Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. | 


# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)
Для портал Azure нет доступных скриптов.

---

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы добавили эластичный пул базы данных SQL Azure в группу отработки отказа и проверили отработку отказа. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> - Создайте отдельную базу данных SQL Azure.
> - Добавьте отдельную базу данных в эластичный пул. 
> - Создайте [группу отработки отказа](sql-database-auto-failover-group.md) для двух эластичных пулов между двумя логическими серверами SQL.
> - Тестовая отработка отказа.

Перейдите к следующему руководству о миграции с помощью DMS.

> [!div class="nextstepaction"]
> [Руководство. Миграция SQL Server в базу данных в составе пула с помощью DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
