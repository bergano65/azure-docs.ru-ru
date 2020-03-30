---
title: Настройка группы отработки отказа
description: Узнайте, как настроить группу автоматического отказа для единой базы данных, эластичного пула и управляемого экземпляра Azure с помощью портала Azure, Az CLI и PowerShell.
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
ms.openlocfilehash: 3b423a25b6b13ad543ef4a74bc0335ce19f5766d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461818"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Наверскакните группу сбоя для базы данных Azure S'L

Эта тема научит вас, как настроить [группу автоматического отказа](sql-database-auto-failover-group.md) для единой базы данных Лазурного формата данных, эластичного пула и управляемого экземпляра с помощью портала Azure или PowerShell. 

## <a name="single-database"></a>Отдельная база данных
Создайте группу failover и добавьте к ней единую базу данных с помощью портала Azure или PowerShell.

### <a name="prerequisites"></a>Предварительные требования

Рассмотрим следующие предпосылки:

- Настройки входа в сервер и брандмауэр для вторичного сервера должны соответствовать настройкам вашего основного сервера. 

### <a name="create-failover-group"></a>Создание группы отработки отказа

# <a name="portal"></a>[Портал](#tab/azure-portal)
Создайте свою группу неудач и добавьте к ней единую базу данных с помощью портала Azure.


1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **Azure SQL** отсутствует в списке, выберите **Все службы** и в поле поиска введите Azure SQL. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите единую базу данных, которая требуется добавить в группу failover. 
1. Выберите имя сервера под **именем сервера,** чтобы открыть настройки для сервера.

   ![Открытый сервер для одного db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Выберите **группы Failover** под панелью **настроек,** а затем выберите **группу добавления** для создания новой группы неудач. 

    ![Добавление новой группы неудач](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. На странице **Failover Group** введите или выберите требуемые значения, а затем выберите **«Создать».**

   - **Базы данных в группе:** Выберите базу данных, добавленную в группу неудач. Добавление базы данных в группу failover автоматически запустит процесс георепликации. 
        
    ![Добавьте S'L DB в группу неудач](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Создайте свою группу неудач и добавьте к ней единую базу данных с помощью PowerShell. 

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

Тест-тест группы неудачи с помощью портала Azure или PowerShell. 

# <a name="portal"></a>[Портал](#tab/azure-portal)

Тест-сбой вашей группы неудачи с помощью портала Azure. 

1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **Azure SQL** отсутствует в списке, выберите **Все службы** и в поле поиска введите Azure SQL. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите единую базу данных, которая требуется добавить в группу failover. 

   ![Открытый сервер для одного db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Выберите **группы Failover** под панелью **настроек,** а затем выберите только что созданную группу неудач. 
  
   ![Выберите группу неудач на портале](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Просмотрите, какой сервер является первичным, а какой — вторичным. 
1. Выберите **Failover** из панели задач, чтобы потерпеть неудачу над вашей группой failover, содержащей единую базу данных. 
1. Выберите **«Да»** на предупреждении, уведомивающее вас о том, что сеансы TDS будут отключены. 

   ![Fail над вашей группой failover содержащ вашей базой данных S'L](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Просмотрите, какой сервер теперь является основным, а какой — вторичным. Если неудача удалось, два сервера должны были поменяться ролями. 
1. Выберите **Failover** снова, чтобы выйти из строя серверов обратно в свои первоначально роли. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Тест неудача вашей группы failover с помощью PowerShell.  

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
Не сбой на вторичном сервере: 

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

> [!IMPORTANT]
> Если вам необходимо удалить вторичную базу данных, удалите ее из группы failover, прежде чем удалить ее. Удаление вторичной базы данных до ее удаления из группы failover может привести к непредсказуемому поведению. 

## <a name="elastic-pool"></a>Эластичный пул
Создайте группу failover и добавьте в нее эластичный пул с помощью портала Azure или PowerShell.  

### <a name="prerequisites"></a>Предварительные требования

Рассмотрим следующие предпосылки:

- Настройки входа в сервер и брандмауэр для вторичного сервера должны соответствовать настройкам вашего основного сервера. 

### <a name="create-the-failover-group"></a>Создание группы неудач 

Создайте группу failover для эластичного пула с помощью портала Azure или PowerShell. 

# <a name="portal"></a>[Портал](#tab/azure-portal)
Создайте свою группу неудач и добавьте свой эластичный пул с помощью портала Azure.

1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **Azure SQL** отсутствует в списке, выберите **Все службы** и в поле поиска введите Azure SQL. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите эластичный пул, который вы хотите добавить в группу failover. 
1. На панели **Обзора** выберите имя сервера под **именем сервера,** чтобы открыть настройки для сервера.
  
    ![Открытый сервер для эластичного пула](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Выберите **группы Failover** под панелью **настроек,** а затем выберите **группу добавления** для создания новой группы неудач. 

    ![Добавление новой группы неудач](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. На странице **Failover Group** введите или выберите требуемые значения, а затем выберите **«Создать».** Либо создать новый вторичный сервер, либо выбрать существующий вторичный сервер. 

1. Выберите **базы данных в группе,** а затем выберите эластичный пул, который вы хотите добавить в группу failover. Если на вторичном сервере уже нет эластичного пула, появляется предупреждение, побуждающее вас создать эластичный пул на вторичном сервере. Выберите предупреждение, а затем выберите **OK** для создания эластичного пула на вторичном сервере. 
        
    ![Добавление эластичного пула в группу failover](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Выберите **Select,** чтобы применить параметры эластичного пула к группе failover, а затем выберите **Создать** для создания группы failover. Добавление эластичного пула в группу failover автоматически запустит процесс георепликации. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте свою группу неудач и добавьте свой эластичный пул с помощью PowerShell. 

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

Тестируйте неудачу в эластичном пуле с помощью портала Azure или PowerShell. 

# <a name="portal"></a>[Портал](#tab/azure-portal)

Не сбой в группе сбоя на вторичном сервере, а затем сбой обратно с помощью портала Azure. 

1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **Azure SQL** отсутствует в списке, выберите **Все службы** и в поле поиска введите Azure SQL. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите эластичный пул, который вы хотите добавить в группу failover. 
1. На панели **Обзора** выберите имя сервера под **именем сервера,** чтобы открыть настройки для сервера.
  
    ![Открытый сервер для эластичного пула](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. Выберите **группы Failover** под панелью **настроек,** а затем выберите группу неудач, созданную в разделе 2. 
  
   ![Выберите группу неудач на портале](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Просмотрите, какой сервер является первичным, а какой — вторичным. 
1. Выберите **Failover** из панели задач, чтобы потерпеть неудачу над вашей группой неудач, содержащей упругий пул. 
1. Выберите **«Да»** на предупреждении, уведомивающее вас о том, что сеансы TDS будут отключены. 

   ![Fail над вашей группой failover содержащ вашей базой данных S'L](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Просмотрите, какой сервер является основным, какой сервер является вторичным. Если неудача удалось, два сервера должны были поменяться ролями. 
1. Выберите **Failover** снова, чтобы потерпеть неудачу группы обратно в исходные настройки. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Тест неудача вашей группы failover с помощью PowerShell.

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

Не сбой на вторичном сервере: 

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

> [!IMPORTANT]
> Если вам необходимо удалить вторичную базу данных, удалите ее из группы failover, прежде чем удалить ее. Удаление вторичной базы данных до ее удаления из группы failover может привести к непредсказуемому поведению. 

## <a name="managed-instance"></a>управляемый экземпляр

Создайте группу сбоя между двумя управляемыми экземплярами с помощью портала Azure или PowerShell. 

Вам нужно будет либо настроить [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) или создать шлюз для виртуальной сети каждого управляемого экземпляра, соединить два шлюза, а затем создать группу неудач. 

### <a name="prerequisites"></a>Предварительные требования
Рассмотрим следующие предпосылки:

- Вторичный управляемый экземпляр должен быть пустым.
- Диапазон поднетов для вторичной виртуальной сети не должен перекрывать диапазон подсетей первичной виртуальной сети. 
- Сопоставление и часовой пояс вторичного экземпляра должны совпадать с расписанием основного экземпляра. 
- При подключении двух шлюзов **общий ключ** должен быть одинаковым для обоих соединений. 

### <a name="create-primary-virtual-network-gateway"></a>Создание первичного виртуального сетевого шлюза 

Если вы еще не настроили [ExpressRoute,](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)можно создать основной виртуальный сетевой шлюз с портала Azure или PowerShell. 

# <a name="portal"></a>[Портал](#tab/azure-portal)

Создание первичного виртуального сетевого шлюза с помощью портала Azure. 

1. На [портале Azure](https://portal.azure.com)перейдите в группу ресурсов и выберите ресурс **виртуальной сети** для основного управляемого экземпляра. 
1. Выберите **подсети** под **настройками,** а затем выберите для добавления новой **подсети Gateway.** Оставьте значения по умолчанию. 

   ![Добавление шлюза для первичного управляемого экземпляра](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. После создания шлюза подсети выберите **Создать ресурс** из левой `Virtual network gateway` панели навигации, а затем введите в поле поиска. Выберите ресурс **виртуального сетевого шлюза,** опубликованный **корпорацией Майкрософт.** 

   ![Создание нового виртуального сетевого шлюза](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Заполните необходимые поля для настройки шлюза основного управляемого экземпляра. 

   В следующей таблице показаны значения, необходимые для шлюза для первичного управляемого экземпляра:
 
    | **Поле** | Значение |
    | --- | --- |
    | **Подписка** |  Подписка, в которой находится основной управляемый экземпляр. |
    | **Название** | Название вашего виртуального сетевого шлюза. | 
    | **Регионе** | Регион, в котором находится ваш вторичный управляемый экземпляр. |
    | **Тип шлюза** | Выберите **VPN**. |
    | **Тип VPN** | Выберите **маршрут на основе** |
    | **номер SKU**| Оставьте `VpnGw1`по умолчанию . |
    | **Расположение**| Место, где находится ваш вторичный управляемый экземпляр и второстепенная виртуальная сеть.   |
    | **Виртуальная сеть**| Выберите виртуальную сеть для вторичного управляемого экземпляра. |
    | **Общественный IP-адрес**| Выберите **Создать**. |
    | **Имя общедоступного IP-адреса**| Введите имя для вашего IP-адреса. |
    | &nbsp; | &nbsp; |

1. Оставьте другие значения по умолчанию, а затем выберите **Обзор и создайте** для просмотра настроек для вашего виртуального сетевого шлюза.

   ![Настройки первичного шлюза](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Выберите **Создать** для создания нового виртуального сетевого шлюза. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Создание первичного виртуального сетевого шлюза с помощью PowerShell. 

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

### <a name="create-secondary-virtual-network-gateway"></a>Создание вторичного виртуального сетевого шлюза

Создайте вторичный виртуальный сетевой шлюз с помощью портала Azure или PowerShell. 

# <a name="portal"></a>[Портал](#tab/azure-portal)
Повторите шаги в предыдущем разделе, чтобы создать виртуальную сетевую подсеть и шлюз для вторичного управляемого экземпляра. Заполните необходимые поля для настройки шлюза для вторичного управляемого экземпляра. 

   В следующей таблице показаны значения, необходимые для шлюза для вторичного управляемого экземпляра:

   | **Поле** | Значение |
   | --- | --- |
   | **Подписка** |  Подписка, в которой находится ваш вторичный управляемый экземпляр. |
   | **Название** | Название для вашего виртуального `secondary-mi-gateway`сетевого шлюза, например. | 
   | **Регионе** | Регион, в котором находится ваш вторичный управляемый экземпляр. |
   | **Тип шлюза** | Выберите **VPN**. |
   | **Тип VPN** | Выберите **маршрут на основе** |
   | **номер SKU**| Оставьте `VpnGw1`по умолчанию . |
   | **Расположение**| Место, где находится ваш вторичный управляемый экземпляр и второстепенная виртуальная сеть.   |
   | **Виртуальная сеть**| Выберите виртуальную сеть, которая была `vnet-sql-mi-secondary`создана в разделе 2, например. |
   | **Общественный IP-адрес**| Выберите **Создать**. |
   | **Имя общедоступного IP-адреса**| Введите имя для вашего `secondary-gateway-IP`IP-адреса, например. |
   | &nbsp; | &nbsp; |

   ![Вторичные настройки шлюза](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Создание вторичного виртуального сетевого шлюза с помощью PowerShell. 

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
Создавайте соединения между двумя шлюзами с помощью портала Azure или PowerShell. 

Необходимо создать два соединения - подключение от основного шлюза к вторичному шлюзу, а затем соединение от вторичного шлюза к основному шлюзу. 

Общий ключ, используемый для обоих соединений, должен быть одинаковым для каждого соединения. 

# <a name="portal"></a>[Портал](#tab/azure-portal)
Создавайте соединения между двумя шлюзами с помощью портала Azure. 

1. Выберите **Создать ресурс** с [портала Azure.](https://portal.azure.com)
1. Введите `connection` в поле поиска, а затем нажмите введите для поиска, который приведет вас к ресурсу **подключения,** опубликованном корпорацией Майкрософт.
1. Выберите **Создать** для создания соединения. 
1. На вкладке **«Основы»** выберите следующие значения, а затем выберите **OK.** 
    1. Выберите `VNet-to-VNet` **для типа подключения**. 
    1. Выберите подписку в раскрывающемся списке. 
    1. Выберите группу ресурсов для управляемого экземпляра в выпадении. 
    1. Выберите местоположение основного управляемого экземпляра из выпадения 
1. На вкладке **«Настройки»** выберите или введите следующие значения, а затем выберите **OK:**
    1. Выберите основной сетевой шлюз для `Primary-Gateway` **первого виртуального сетевого шлюза,** например.  
    1. Выберите вторичный сетевой шлюз для `Secondary-Gateway` **второго виртуального сетевого шлюза,** например. 
    1. Выберите флажок рядом с **Establish двунаправленной связи.** 
    1. Либо оставьте имя основного соединения по умолчанию, либо переименуйте его в значение по вашему выбору. 
    1. Предоставьте **общий ключ (PSK)** для `mi1m2psk`подключения, например. 

   ![Создание подключения шлюза](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. На вкладке **Резюме** просмотрите настройки для двунаправленного соединения, а затем выберите **OK** для создания соединения. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Создавайте соединения между двумя шлюзами с помощью PowerShell. 

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

### <a name="create-the-failover-group"></a>Создание группы неудач 
Создайте группу failover для управляемых экземпляров с помощью портала Azure или PowerShell. 

# <a name="portal"></a>[Портал](#tab/azure-portal)

Создайте группу failover для управляемых экземпляров с помощью портала Azure. 

1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **Azure SQL** отсутствует в списке, выберите **Все службы** и в поле поиска введите Azure SQL. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите основной управляемый экземпляр, который необходимо добавить в группу failover.  
1. В **настройках**перейдите в **группы Instance Failover,** а затем выберите **группу добавления,** чтобы открыть страницу **группы Instance Failover.** 

   ![Добавление группы неудач](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. На странице **Instance Failover Group** введите имя группы неудач, а затем выберите вторичный управляемый экземпляр из выпадения. Выберите **Создать** для создания группы неудач. 

   ![Создание группы отработки отказа](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. После завершения развертывания группы failover вы будете возвращены на страницу **группы Failover.** 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте группу failover для управляемых экземпляров с помощью PowerShell. 

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

Тест-тест группы неудачи с помощью портала Azure или PowerShell. 

# <a name="portal"></a>[Портал](#tab/azure-portal)

Тест-сбой вашей группы неудачи с помощью портала Azure. 

1. Перейдите к _второму_ управляемому экземпляру в [рамках портала Azure](https://portal.azure.com) и выберите **группы Failover instance** в настройках. 
1. Обзор, который управлял экземпляром является основным, и который управляемый экземпляр является вторичным. 
1. Выберите **Failover,** а затем выберите **Yes** на предупреждение о отключениях сеансов TDS. 

   ![Fail over the failover group](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Просмотрите, какой экземпляр является основным, а какой экземпляр - вторичным. Если неудача удалось, эти два экземпляра должны были поменяться ролями. 

   ![Управляемые экземпляры поменялись ролями после сбоя](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Перейдите к новому _вторичному_ управляемому экземпляру и выберите **Failover** еще раз, чтобы не вернуть основной экземпляр к основной роли. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Тест неудача вашей группы failover с помощью PowerShell. 

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

## <a name="locate-listener-endpoint"></a>Найдите конечную точку для слушателя

После настройки группы failover обновите связь строки соединения приложения до конечная точка слушателя. Это позволит сохранить ваше приложение подключенным к слушателю группы failover, а не к основной базе данных, эластичной пулу или управляемому экземпляру. Таким образом, вам не придется вручную обновлять строку соединения каждый раз, когда сущность базы данных Azure S'L завершается, и трафик направляется в ту сущность, которая в настоящее время является основной. 

Конечная точка слушателя находится `fog-name.database.windows.net`в виде и видна на портале Azure при просмотре группы неудач:

![Строка соединения группы Failover](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Remarks

- Удаление группы сбоя для одной или объединенной базы данных не останавливает репликацию и не удаляет реплицированную базу данных. Вам нужно будет вручную остановить георепликацию и удалить базу данных со вторичного сервера, если вы хотите добавить единую или объединенную базу данных обратно в группу неудачников после ее удаления. Невыполнение любой вещи может привести `The operation cannot be performed due to multiple errors` к ошибке, аналогичной при попытке добавить базу данных в группу failover. 


## <a name="next-steps"></a>Дальнейшие действия

Для получения подробных инструкций по настройке группы неудачи см.
- [Добавление единой базы данных в группу неудач](sql-database-single-database-failover-group-tutorial.md)
- [Добавление эластичного пула в группу отработки отказа](sql-database-elastic-pool-failover-group-tutorial.md)
- [Добавление управляемых экземпляров в группу failover](sql-database-managed-instance-failover-group-tutorial.md)
 
Для получения обзора параметров высокой доступности базы данных Azure S'L [см.](sql-database-active-geo-replication.md) [auto-failover groups](sql-database-auto-failover-group.md) 
