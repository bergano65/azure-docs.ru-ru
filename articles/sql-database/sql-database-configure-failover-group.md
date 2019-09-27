---
title: Настройка группы отработки отказа для базы данных SQL Azure
description: Узнайте, как настроить группу автоматической отработки отказа для отдельной базы данных SQL Azure, эластичного пула и управляемого экземпляра с помощью портал Azure, командной строки AZ CLI и PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 9206fd264854cd9e5d8e46473dd60b05a3362fdd
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329361"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Настройка группы отработки отказа для базы данных SQL Azure

В этом разделе рассказывается, как настроить [группу автоматической отработки отказа](sql-database-auto-failover-group.md) для отдельной базы данных SQL Azure, эластичного пула и управляемого экземпляра с помощью портал Azure или PowerShell. 

## <a name="single-database"></a>Отдельная база данных
Создайте группу отработки отказа и добавьте в нее отдельную базу данных с помощью портал Azure или PowerShell.

### <a name="prerequisites"></a>Предварительные требования

Рассмотрим следующие предварительные требования.

- Параметры входа и брандмауэра сервера для сервера-получателя должны соответствовать серверу-источнику. 

### <a name="create-failover-group"></a>Создание группы отработки отказа

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)
Создайте группу отработки отказа и добавьте в нее отдельную базу данных с помощью портал Azure.

1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите отдельную базу данных, которую необходимо добавить в группу отработки отказа. 
1. Выберите имя сервера в поле **имя сервера** , чтобы открыть параметры для сервера.

   ![Открыть сервер для отдельной базы данных](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Выберите **группы отработки отказа** в области **Параметры** , а затем щелкните **Добавить группу** , чтобы создать новую группу отработки отказа. 

    ![Добавить новую группу отработки отказа](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. На странице **Группа отработки отказа** введите или выберите необходимые значения, а затем нажмите кнопку **создать**.

   - **Базы данных в группе**: Выберите базу данных, которую необходимо добавить в группу отработки отказа. Добавление базы данных в группу отработки отказа автоматически начнет процесс георепликации. 
        
    ![Добавление базы данных SQL в группу отработки отказа](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Создайте группу отработки отказа и добавьте в нее отдельную базу данных с помощью PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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

---

### <a name="test-failover"></a>Тестовая отработка отказа 

Тестовая отработка отказа группы отработки отказа с помощью портал Azure или PowerShell. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Тестовая отработка отказа группы отработки отказа с помощью портал Azure. 

1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите отдельную базу данных, которую необходимо добавить в группу отработки отказа. 

   ![Открыть сервер для отдельной базы данных](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Выберите **группы отработки отказа** в области **Параметры** , а затем выберите группу отработки отказа, которую вы только что создали. 
  
   ![Выбор группы отработки отказа на портале](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Проверьте, какой сервер является основным, а какой — сервером-получателем. 
1. Выберите **отработка отказа** в области задач для переключения группы отработки отказа, содержащей одну базу данных. 
1. Нажмите кнопку **Да** в предупреждении, уведомляющее о том, что сеансы TDS будут отключены. 

   ![Переключение группы отработки отказа, содержащей базу данных SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Проверьте, какой сервер является основным и какой сервер является вторичным. Если отработка отказа прошла успешно, у двух серверов должны быть переключенные роли. 
1. Снова установите флажок **отработка отказа** , чтобы вернуть серверы к исходным ролям. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Тестовая отработка отказа группы отработки отказа с помощью PowerShell.  

Проверьте роль вторичной реплики: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
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
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

Восстановите группу отработки отказа на основной сервер:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

## <a name="elastic-pool"></a>Эластичный пул
Создайте группу отработки отказа и добавьте в нее эластичный пул с помощью портал Azure или PowerShell.  

### <a name="prerequisites"></a>Предварительные требования

Рассмотрим следующие предварительные требования.

- Параметры входа и брандмауэра сервера для сервера-получателя должны соответствовать серверу-источнику. 

### <a name="create-the-failover-group"></a>Создание группы отработки отказа 

Создайте группу отработки отказа для эластичного пула с помощью портал Azure или PowerShell. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)
Создайте группу отработки отказа и добавьте в нее эластичный пул с помощью портал Azure.

1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите эластичный пул, который необходимо добавить в группу отработки отказа. 
1. В области **Обзор** выберите имя сервера в поле **имя сервера** , чтобы открыть параметры для сервера.
  
    ![Открытие сервера для эластичного пула](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Выберите **группы отработки отказа** в области **Параметры** , а затем щелкните **Добавить группу** , чтобы создать новую группу отработки отказа. 

    ![Добавить новую группу отработки отказа](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. На странице **Группа отработки отказа** введите или выберите необходимые значения, а затем нажмите кнопку **создать**. Либо создайте новый сервер-получатель, либо выберите существующий сервер-получатель. 

1. Выберите **базы данных в группе** , а затем выберите эластичный пул, который необходимо добавить в группу отработки отказа. Если эластичный пул еще не существует на сервере-получателе, отобразится предупреждение с запросом на создание эластичного пула на сервере-получателе. Выберите предупреждение и нажмите кнопку **ОК** , чтобы создать эластичный пул на сервере-получателе. 
        
    ![Добавление эластичного пула в группу отработки отказа](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Выберите **выбрать** , чтобы применить параметры эластичного пула к группе отработки отказа, а затем щелкните **создать** , чтобы создать группу отработки отказа. Добавление эластичного пула в группу отработки отказа автоматически начнет процесс георепликации. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте группу отработки отказа и добавьте в нее эластичный пул с помощью PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>Тестовая отработка отказа

Тестовая отработка отказа эластичного пула с помощью портал Azure или PowerShell. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Завершите работу группы отработки отказа на сервере-получателе, а затем выполните аварийное копирование с помощью портал Azure. 

1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите эластичный пул, который необходимо добавить в группу отработки отказа. 
1. В области **Обзор** выберите имя сервера в поле **имя сервера** , чтобы открыть параметры для сервера.
  
    ![Открытие сервера для эластичного пула](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. Выберите **группы отработки отказа** в области **Параметры** , а затем выберите группу отработки отказа, созданную в разделе 2. 
  
   ![Выбор группы отработки отказа на портале](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Проверьте, какой сервер является основным, а какой — сервером-получателем. 
1. Выберите **отработка отказа** в области задач для переключения группы отработки отказа, содержащей эластичный пул. 
1. Нажмите кнопку **Да** в предупреждении, уведомляющее о том, что сеансы TDS будут отключены. 

   ![Переключение группы отработки отказа, содержащей базу данных SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Проверьте, какой сервер является основным, какой сервер является вторичным. Если отработка отказа прошла успешно, у двух серверов должны быть переключенные роли. 
1. Снова установите флажок **отработка отказа** , чтобы вернуть группу отработки отказа к исходным параметрам. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Тестовая отработка отказа группы отработки отказа с помощью PowerShell.

Проверьте роль вторичной реплики: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
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
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

## <a name="managed-instance"></a>Управляемый экземпляр

Создайте группу отработки отказа между двумя управляемыми экземплярами с помощью портал Azure или PowerShell. 

Вам потребуется создать шлюз для виртуальной сети каждого управляемого экземпляра, подключить два шлюза, а затем создать группу отработки отказа.

### <a name="prerequisites"></a>Предварительные требования
Рассмотрим следующие предварительные требования.

- Вторичный управляемый экземпляр должен быть пустым.
- Диапазон подсети для вторичной виртуальной сети не должен пересекаться с диапазоном подсети основной виртуальной сети. 
- Параметры сортировки и часовой пояс вторичного экземпляра должны соответствовать параметрам первичного экземпляра. 
- При подключении двух шлюзов **общий ключ** должен быть одинаковым для обоих подключений. 

### <a name="create-primary-virtual-network-gateway"></a>Создание основного шлюза виртуальной сети 

Создайте основной шлюз виртуальной сети с помощью портал Azure или PowerShell. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Создайте основной шлюз виртуальной сети с помощью портал Azure. 

1. В [портал Azure](https://portal.azure.com)перейдите к группе ресурсов и выберите ресурс **виртуальной сети** для основного управляемого экземпляра. 
1. Выберите **подсети** в разделе **Параметры** , а затем выберите, чтобы добавить новую **подсеть шлюза**. Оставьте значения по умолчанию. 

   ![Добавление шлюза для основного управляемого экземпляра](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. После создания шлюза подсети выберите **создать ресурс** в левой области навигации, а затем введите `Virtual network gateway` в поле поиска. Выберите ресурс **шлюза виртуальной сети** , опубликованный **корпорацией Майкрософт**. 

   ![Создание нового шлюза виртуальной сети](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Заполните обязательные поля, чтобы настроить шлюз для основного управляемого экземпляра. 

   В следующей таблице приведены значения, необходимые для шлюза для основного управляемого экземпляра.
 
    | **Поле** | Значение |
    | --- | --- |
    | **подписка** |  Подписка, в которой находится основной управляемый экземпляр. |
    | **Name** | Имя шлюза виртуальной сети. | 
    | **Регион** | Регион, где находится ваш вторичный управляемый экземпляр. |
    | **Тип шлюза** | Выберите **VPN**. |
    | **Тип VPN** | Выбор **на основе маршрута** |
    | **SKU**| Оставьте значение по `VpnGw1`умолчанию для. |
    | **Location**| Расположение, где находится дополнительный управляемый экземпляр и вторичная виртуальная сеть.   |
    | **Виртуальная сеть**| Выберите виртуальную сеть для вторичного управляемого экземпляра. |
    | **Общедоступный IP-адрес**| Выберите **Создать**. |
    | **Имя общедоступного IP-адреса**| Введите имя для своего IP-адреса. |
    | &nbsp; | &nbsp; |

1. Оставьте остальные значения по умолчанию, а затем выберите **проверить и создать** , чтобы проверить параметры шлюза виртуальной сети.

   ![Параметры основного шлюза](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Выберите **создать** , чтобы создать новый шлюз виртуальной сети. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте основной шлюз виртуальной сети с помощью PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Создание дополнительного шлюза виртуальной сети

Создайте дополнительный шлюз виртуальной сети с помощью портал Azure или PowerShell. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)
Повторите действия, описанные в предыдущем разделе, чтобы создать подсеть и шлюз виртуальной сети для вторичного управляемого экземпляра. Заполните обязательные поля, чтобы настроить шлюз для вторичного управляемого экземпляра. 

   В следующей таблице приведены значения, необходимые для шлюза для вторичного управляемого экземпляра.

   | **Поле** | Значение |
   | --- | --- |
   | **подписка** |  Подписка, в которой находится экземпляр вторичного управляемого экземпляра. |
   | **Name** | Имя шлюза виртуальной сети, например `secondary-mi-gateway`. | 
   | **Регион** | Регион, где находится ваш вторичный управляемый экземпляр. |
   | **Тип шлюза** | Выберите **VPN**. |
   | **Тип VPN** | Выбор **на основе маршрута** |
   | **SKU**| Оставьте значение по `VpnGw1`умолчанию для. |
   | **Location**| Расположение, где находится дополнительный управляемый экземпляр и вторичная виртуальная сеть.   |
   | **Виртуальная сеть**| Выберите виртуальную сеть, созданную в разделе 2, например `vnet-sql-mi-secondary`. |
   | **Общедоступный IP-адрес**| Выберите **Создать**. |
   | **Имя общедоступного IP-адреса**| Введите имя IP-адреса, например `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Параметры дополнительного шлюза](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте дополнительный шлюз виртуальной сети с помощью PowerShell. 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>Подключение шлюзов 
Создайте подключения между двумя шлюзами с помощью портал Azure или PowerShell. 

Необходимо создать два подключения — подключение из основного шлюза к дополнительному шлюзу, а затем подключение из дополнительного шлюза к основному шлюзу. 

Общий ключ, используемый для обоих подключений, должен быть одинаковым для каждого подключения. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)
Создайте подключения между двумя шлюзами с помощью портал Azure. 

1. Перейдите к группе ресурсов в [портал Azure](https://portal.azure.com) и выберите основной шлюз, созданный на шаге 4. 
1. Выберите **подключения** в разделе **Параметры** , а затем нажмите кнопку **добавить** , чтобы создать новое соединение. 

   ![Добавление подключения к основному шлюзу](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. Введите имя для подключения и введите значение для **общего ключа**. 
1. Выберите **второй шлюз виртуальной сети** , а затем выберите шлюз для вторичного управляемого экземпляра. 

   ![Создание первичного и вторичного подключения](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. Нажмите кнопку **ОК** , чтобы добавить новое подключение к шлюзу "основной-к-дополнительно".
1. Повторите эти действия, чтобы создать подключение из шлюза вторичного управляемого экземпляра к шлюзу основного управляемого экземпляра. 

   ![Создать вторичное подключение к первичному соединению](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте подключения между двумя шлюзами с помощью PowerShell. 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Создание группы отработки отказа 
Создайте группу отработки отказа для управляемых экземпляров с помощью портал Azure или PowerShell. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Создайте группу отработки отказа для управляемых экземпляров с помощью портал Azure. 

1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите основной управляемый экземпляр, который необходимо добавить в группу отработки отказа.  
1. В разделе **Параметры**перейдите к **экземпляру группы отработки отказа** и выберите **Добавить группу** , чтобы открыть страницу **группы отработки отказа экземпляра** . 

   ![Добавление группы отработки отказа](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. На странице **Группа отработки отказа** введите имя группы отработки отказа, а затем выберите вторичный управляемый экземпляр из раскрывающегося списка. Выберите **создать** , чтобы создать группу отработки отказа. 

   ![Создание группы отработки отказа](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. После завершения развертывания группы отработки отказа будет выполнен переход на страницу **группы отработки отказа** . 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте группу отработки отказа для управляемых экземпляров с помощью PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>Тестовая отработка отказа

Тестовая отработка отказа группы отработки отказа с помощью портал Azure или PowerShell. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Тестовая отработка отказа группы отработки отказа с помощью портал Azure. 

1. Перейдите к управляемому экземпляру в [портал Azure](https://portal.azure.com) и выберите **группы отработки отказа экземпляра** в разделе Параметры. 
1. Проверьте, какой управляемый экземпляр является первичным и какой управляемый экземпляр является вторичным. 
1. Выберите **отработка отказа** и нажмите **кнопку Да** в предупреждении о том, что сеансы TDS отключены. 

   ![Переключение группы отработки отказа](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Проверьте, какой экземпляр управляемого экземпляра является основным, а какой — дополнительным. Если отработка отказа прошла успешно, два экземпляра должны иметь переключаемые роли. 

   ![Управляемые экземпляры с переключением ролей после отработки отказа](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Снова выберите **отработку отказа** , чтобы восстановить первичный экземпляр в первичную роль. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Тестовая отработка отказа группы отработки отказа с помощью PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>Определение конечной точки прослушивателя

После настройки группы отработки отказа обновите строку подключения приложения до конечной точки прослушивателя. При этом приложение будет подключено к прослушивателю группы отработки отказа, а не к базе данных источника, эластичному пулу или управляемому экземпляру. Таким образом, вам не придется вручную обновлять строку подключения каждый раз, когда происходит отработка отказа сущности базы данных SQL Azure, и трафик направляется в любую сущность, которая в настоящее время является первичной. 

Конечная точка прослушивателя имеет форму `fog-name.database.windows.net` и отображается в портал Azure при просмотре группы отработки отказа:

![Строка подключения группы отработки отказа](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Примечания

- Удаление группы отработки отказа для одной базы данных или в составе пула не приводит к ее удалению и не удаляет реплицированную базу данных. Необходимо вручную отключить георепликацию и удалить базу данных с сервера-получателя, если вы хотите добавить отдельную или объединенную базу данных в группу отработки отказа после ее удаления. Если не выполнить ни одно из этих действий, может возникнуть ошибка, аналогичная `The operation cannot be performed due to multiple errors` при попытке добавить базу данных в группу отработки отказа. 


## <a name="next-steps"></a>Следующие шаги

Подробные инструкции по настройке группы отработки отказа см. в следующих учебниках:
- [Добавление отдельной базы данных в группу отработки отказа](sql-database-single-database-failover-group-tutorial.md)
- [Добавление эластичного пула в группу отработки отказа](sql-database-elastic-pool-failover-group-tutorial.md)
- [Добавление управляемых экземпляров в группу отработки отказа](sql-database-managed-instance-failover-group-tutorial.md)
 
Общие сведения о вариантах высокой доступности базы данных SQL Azure см. в статье [Георепликация](sql-database-active-geo-replication.md) и [группы автоматической отработки отказа](sql-database-auto-failover-group.md). 
