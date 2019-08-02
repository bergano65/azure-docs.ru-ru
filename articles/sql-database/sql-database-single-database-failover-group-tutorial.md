---
title: Учебник. Добавление отдельной базы данных SQL Azure в группу отработки отказа | Документация Майкрософт
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
ms.openlocfilehash: d11dd72c65ea32fb5a262f325bdcad0b5a8ab863
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566650"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Учебник. Добавление отдельной базы данных SQL Azure в группу отработки отказа

Настройте группу отработки отказа для отдельной базы данных SQL Azure и тестовую отработку отказа с помощью портал Azure, PowerShell или Azure CLI.  Из этого учебника вы узнаете следующее:

> [!div class="checklist"]
> - Создайте отдельную базу данных SQL Azure.
> - Создайте [группу](sql-database-auto-failover-group.md) отработки отказа для отдельной базы данных между двумя логическими серверами SQL.
> - Тестовая отработка отказа.

## <a name="prerequisites"></a>предварительные требования

# <a name="azure-portaltabazure-portal"></a>[портал Azure](#tab/azure-portal)
В рамках этого руководства вам потребуются: 

- Подписка Azure. [Создайте бесплатную учетную запись](https://azure.microsoft.com/free/) , если она еще не создана.


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Чтобы завершить работу с этим руководством, убедитесь, что у вас есть следующие элементы:

- Подписка Azure. [Создайте бесплатную учетную запись](https://azure.microsoft.com/free/) , если она еще не создана.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="az-clitabbash"></a>[Azure CLI](#tab/bash)
Чтобы завершить работу с этим руководством, убедитесь, что у вас есть следующие элементы:

- Подписка Azure. [Создайте бесплатную учетную запись](https://azure.microsoft.com/free/) , если она еще не создана.
- Последняя версия [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1\. Создание отдельной базы данных 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2\. Создание группы отработки отказа 
На этом шаге вы создадите [группу](sql-database-auto-failover-group.md) отработки отказа между существующим сервером Azure SQL Server и новым сервером Azure SQL Server в другом регионе. Затем добавьте пример базы данных в группу отработки отказа. 

# <a name="azure-portaltabazure-portal"></a>[портал Azure](#tab/azure-portal)
Создайте группу отработки отказа и добавьте в нее отдельную базу данных с помощью портал Azure. 


1. Выберите **все службы** в левом верхнем углу [портал Azure](https://portal.azure.com). 
1. Введите `sql servers` в поле поиска. 
1. Используемых Щелкните значок звездочки рядом с пунктом серверы SQL Server для избранных **серверов SQL Server** и добавьте его в область навигации слева. 
    
    ![Обнаружение серверов SQL Server](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. Выберите **серверы SQL Server** и выберите сервер, созданный в разделе 1, например `mysqlserver`.
1. Выберите **группы** отработки отказа в области **Параметры** , а затем щелкните **Добавить группу** , чтобы создать новую группу отработки отказа. 

    ![Добавить новую группу отработки отказа](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. На странице **Группа** отработки отказа введите или выберите следующие значения, а затем щелкните **создать**.
    - **Имя группы**отработки отказа: Введите уникальное имя группы отработки отказа, `failovergrouptutorial`например. 
    - **Сервер-получатель**: Выберите параметр для *настройки обязательных параметров* , а затем выберите **Создание нового сервера**. Кроме того, можно выбрать уже существующий сервер в качестве сервера-получателя. После ввода следующих значений нажмите **кнопку Выбрать**. 
        - **Имя сервера**. Введите уникальное имя сервера-получателя, например `mysqlsecondary`. 
        - **Учетные данные администратора сервера для входа**. Тип`azureuser`
        - **Пароль**. Введите сложный пароль, который соответствует требованиям к паролю.
        - **Расположение.** Выберите расположение из раскрывающегося списка, например Восточная часть США 2. Это расположение не может совпадать с расположением основного сервера.

    > [!NOTE]
    > Параметры имени для входа и брандмауэра сервера должны совпадать с именем сервера источника. 
    
      ![Создание сервера-получателя для группы отработки отказа](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

   - **Базы данных в группе**: После выбора сервера-получателя этот параметр будет разблокирован. Выберите его, чтобы **выбрать базы данных** , а затем выберите базу данных, созданную в разделе 1. Добавление базы данных в группу отработки отказа автоматически начнет процесс георепликации. 
        
    ![Добавление базы данных SQL в группу отработки отказа](media/sql-database-single-database-create-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Создайте группу отработки отказа и добавьте в нее отдельную базу данных с помощью PowerShell. 

   > [!NOTE]
   > Параметры имени для входа и брандмауэра сервера должны совпадать с именем сервера источника. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US 2"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US 2"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

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

# <a name="az-clitabbash"></a>[Azure CLI](#tab/bash)
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
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --failover-policy Automatic
   ```

---

## <a name="3---test-failover"></a>3\. тестовая отработка отказа 
На этом шаге группа отработки отказа будет передаваться на сервер-получатель, а затем восстановлена с помощью портал Azure. 

# <a name="azure-portaltabazure-portal"></a>[портал Azure](#tab/azure-portal)
Тестовая отработка отказа с помощью портал Azure. 

1. Перейдите к серверу **SQL Server** в [портал Azure](https://portal.azure.com). 
1. Выберите **группы** отработки отказа в области **Параметры** , а затем выберите группу отработки отказа, созданную в разделе 2. 
  
   ![Выбор группы отработки отказа на портале](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. Проверьте, какой сервер является основным, а какой — сервером-получателем. 
1. Выберите отработка отказа в области задач для отработки отказа группы отработки отказа, содержащей пример отдельной базы данных 
1. Нажмите кнопку **Да** в предупреждении, уведомляющее о том, что сеансы TDS будут отключены. 

   ![Переключение группы отработки отказа, содержащей базу данных SQL](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. Проверьте, какой сервер является основным и какой сервер является вторичным. Если отработка отказа завершилась успешно, два сервера должны иметь переключенные роли. 
1. Снова установите флажок отработка отказа, чтобы вернуть серверы к исходным ролям. 

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
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
   Write-host "Failed failover group to sucessfully to" $drServerName 
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
   Write-host "Failed failover group to successfully to back to" $serverName
   ```

# <a name="az-clitabbash"></a>[Azure CLI](#tab/bash)
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

---

## <a name="clean-up-resources"></a>Очистка ресурсов 
Очистите ресурсы, удалив группу ресурсов. 

# <a name="azure-portaltabazure-portal"></a>[портал Azure](#tab/azure-portal)
Удалите группу ресурсов с помощью портал Azure. 


1. Перейдите к группе ресурсов в [портал Azure](https://portal.azure.com).
1. Выберите **Удалить группу ресурсов** , чтобы удалить все ресурсы в группе, а также саму группу ресурсов. 
1. Введите имя группы `myResourceGroup`ресурсов, в текстовом поле и нажмите кнопку **Удалить** , чтобы удалить группу ресурсов.  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Удалите группу ресурсов с помощью PowerShell. 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

# <a name="az-clitabbash"></a>[Azure CLI](#tab/bash)
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

---


## <a name="full-scripts"></a>Полные скрипты

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

# <a name="az-clitabbash"></a>[Azure CLI](#tab/bash)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Create SQL Database")]

# <a name="azure-portaltabazure-portal"></a>[портал Azure](#tab/azure-portal)
Для портал Azure нет доступных скриптов.
 
---

Другие скрипты базы данных SQL Azure можно найти здесь: [Azure PowerShell](sql-database-powershell-samples.md) и [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы добавили одну базу данных SQL Azure в группу отработки отказа и проверили отработку отказа. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> - Создайте отдельную базу данных SQL Azure. 
> - Создайте [группу](sql-database-auto-failover-group.md) отработки отказа для отдельной базы данных между двумя логическими серверами SQL.
> - Тестовая отработка отказа.

Перейдите к следующему руководству по добавлению эластичного пула в группу отработки отказа. 

> [!div class="nextstepaction"]
> [Учебник. Добавление эластичного пула базы данных SQL Azure в группу отработки отказа](sql-database-elastic-pool-failover-group-tutorial.md)
