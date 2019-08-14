---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 06/19/2019
ms.author: mathoma
ms.openlocfilehash: ffa48d55703347883edf96a0a27ee52d19189976
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935016"
---
На этом этапе вы создадите группу ресурсов и отдельную базу данных в службе "База данных SQL Azure".

> [!IMPORTANT]
> Убедитесь, что правила брандмауэра настроены для использования общедоступного IP-адреса компьютера, на котором выполняются действия из этой статьи. 
>
> Дополнительные сведения см. в статье [sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) (sp_set_database_firewall_rule (база данных SQL Azure)). Чтобы определить IP-адрес, используемый для правила брандмауэра уровня сервера для компьютера, обратитесь к статье [Краткое руководство. Создание правила брандмауэра на уровне сервера для базы данных SQL с помощью портала Azure](../sql-database-server-level-firewall-rule.md).  

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Создайте группу ресурсов и отдельную базу данных с помощью портала Azure.

1. Щелкните **Создать ресурс** в верхнем левом углу окна портала Azure.
2. Выберите **Базы данных**, **База данных SQL**, чтобы открыть страницу **Создать базу данных SQL**.

   ![Создание отдельной базы данных](../media/sql-database-get-started-portal/create-database-1.png)

3. На вкладке **Основные сведения**  в разделе **Сведения о проекте** введите или выберите следующие значения:

   - **Подписка**: Раскройте раскрывающийся список и выберите правильную подписку, если она не отобразится автоматически.
   - **Группа ресурсов.** Выберите **Создать**, введите `myResourceGroup` и щелкните **ОК**.

     ![Новая база данных SQL — вкладка основных сведений](../media/sql-database-get-started-portal/new-sql-database-basics.png)

4. В разделе **Сведения о базе данных** введите или выберите следующие значения:

   - **Имя базы данных**. Укажите `mySampleDatabase`.
   - **Сервер** — Выберите **Создать**, введите следующие значения и щелкните **Выбрать**.
       - **Имя сервера**. Введите `mysqlserver` и несколько цифр для уникальности имени.
       - **Учетные данные администратора сервера для входа**. Введите `azureuser`.
       - **Пароль**. Введите сложный пароль, который соответствует требованиям к паролю.
       - **Расположение.** Выберите расположение из раскрывающегося списка, например `West US 2`.

         ![Новый сервер](../media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Запомните или запишите имя входа и пароль администратора сервера, чтобы можно было войти на сервер и в базы данных для выполнения действий, описанных в этом и других кратких руководствах. Если вы забыли имя входа или пароль, получить имя входа или сбросить пароль можно на странице **сервера SQL server**. Чтобы открыть страницу **сервера SQL**, выберите имя сервера на странице **Обзор** для базы данных после создания базы данных.

   - **Хотите использовать пул эластичных БД SQL?** Выберите **Нет**.
   - **Вычисления и хранилище**. Выберите **Настройка базы данных**. 

     ![Сведения о Базе данных SQL](../media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - Выберите **Подготовлено** и **5-е поколение**.

     ![Элементы "Подготовлено" и "4-е поколение"](../media/sql-database-get-started-portal/create-database-provisioned.png)

   - Проверьте значения параметров **Максимальное число виртуальных ядер**, **Минимальное число виртуальных ядер**, **Задержка перед автоматической приостановкой** и **Максимальный размер данных**. Измените их при необходимости.
   - Примите условия использования предварительной версии и нажмите кнопку **ОК**.
   - Нажмите кнопку **Применить**.

5. Перейдите на вкладку **Дополнительные параметры**. 
6. В разделе **Источник данных** для параметра **Использовать имеющиеся данные** выберите `Sample`.

   ![Дополнительные параметры Базы данных SQL](../media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Не забудьте выбрать данные **Пример (AdventureWorksLT)** , чтобы изучить это руководство и другие краткие руководства по Базе данных SQL Azure, в которых используются эти данные.

7. Оставьте остальные значения заданными по умолчанию и нажмите кнопку **Просмотр и создание** в нижней части формы.
8. Проверьте параметры и нажмите кнопку **Создать**.

9. В форме **База данных SQL** щелкните **Создать**, чтобы развернуть и подготовить группу ресурсов, сервер и базу данных.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Создайте группу ресурсов и отдельную базу данных с помощью PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US 2"
   $adminLogin = "azureuser"
   $password = "PWD27!"+(New-Guid).Guid
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
   Write-host "Password is" $password  
   Write-host "Server name is" $serverName 

   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 

   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup

   # Create a server with a system wide unique server name
   Write-host "Creating primary logical server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create General Purpose Gen4 database with 1 vCore
   Write-host "Creating a gen5 2 vCore database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -VCore 2 `
      -ComputeGeneration Gen5 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
   ```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Создайте группу ресурсов и отдельную базу данных с помощью Azure CLI.

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   subscriptionID=<SubscriptionID>
   resourceGroupName=myResourceGroup-$RANDOM
   location=SouthCentralUS
   adminLogin=azureuser
   password="PWD27!"+`openssl rand -base64 18`
   serverName=mysqlserver-$RANDOM
   databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # The ip address range that you want to allow to access your DB. 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB
   startip=0.0.0.0
   endip=0.0.0.0
  
   # Connect to Azure
   az login

   # Set the subscription context for the Azure account
   az account set -s $subscriptionID

   # Create a resource group
   echo "Creating resource group..."
   az group create \
      --name $resourceGroupName \
      --location $location \
      --tags Owner[=SQLDB-Samples]

   # Create a logical server in the resource group
   echo "Creating primary logical server..."
   az sql server create \
      --name $serverName \
      --resource-group $resourceGroupName \
      --location $location  \
      --admin-user $adminLogin \
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $serverName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip

   # Create a gen5 1vCore database in the server 
   echo "Creating a gen5 2 vCore database..."
   az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2
   ```

---
