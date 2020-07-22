---
title: Учебник. Добавление Управляемый экземпляр SQL в группу отработки отказа
titleSuffix: Azure SQL Managed Instance
description: Из этого руководства вы узнаете, как создать группу отработки отказа между первичным и дополнительным Управляемый экземпляр Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: f1bf8eff4a6f518fc24c87c5fbd24984ef8f8b29
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84718892"
---
# <a name="tutorial-add-sql-managed-instance-to-a-failover-group"></a>Учебник. Добавление Управляемый экземпляр SQL в группу отработки отказа
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Добавление управляемых экземпляров Управляемый экземпляр Azure SQL в группу отработки отказа. В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> - Создайте основной управляемый экземпляр.
> - Создайте вторичный управляемый экземпляр в составе [группы отработки отказа](../database/auto-failover-group-overview.md). 
> - Тестовая отработка отказа.

  > [!NOTE]
  > - При работе с этим руководством убедитесь, что вы настраиваете ресурсы с [предварительными требованиями для настройки групп отработки отказа для SQL управляемый экземпляр](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Создание управляемого экземпляра может занять значительное время. В результате для выполнения этого руководства может потребоваться несколько часов. Дополнительные сведения о времени подготовки см. в разделе [SQL управляемый экземпляр Management Operations](sql-managed-instance-paas-overview.md#management-operations). 
  > - Для управляемых экземпляров, участвующих в группе отработки отказа, требуется [Azure ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) или два ПОДКЛЮЧЕННЫХ VPN-шлюза. В этом руководстве содержатся инструкции по созданию и подключению VPN-шлюзов. Если вы уже настроили ExpressRoute, пропустите эти шаги. 


## <a name="prerequisites"></a>Предварительные требования

# <a name="portal"></a>[Портал](#tab/azure-portal)
В рамках этого руководства вам потребуются: 

- Подписка Azure. [Создайте бесплатную учетную запись](https://azure.microsoft.com/free/) , если она еще не создана.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Чтобы завершить работу с этим руководством, убедитесь, что у вас есть следующие элементы:

- Подписка Azure. [Создайте бесплатную учетную запись](https://azure.microsoft.com/free/) , если она еще не создана.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-a-resource-group-and-primary-managed-instance"></a>1. Создание группы ресурсов и основного управляемого экземпляра

На этом шаге вы создадите группу ресурсов и основной управляемый экземпляр для группы отработки отказа с помощью портал Azure или PowerShell. 


# <a name="portal"></a>[Портал](#tab/azure-portal) 

Создайте группу ресурсов и основной управляемый экземпляр с помощью портал Azure. 

1. На портале Azure в меню слева выберите **Azure SQL**. Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите `Azure SQL` в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Щелкните **+ Добавить**, чтобы открыть страницу **Выбор варианта развертывания SQL**. Чтобы просмотреть дополнительные сведения о различных базах данных, выберите **Показать сведения** на плитке **Базы данных**.
1. Выберите **создать** на плитке **управляемые экземпляры SQL** . 

    ![Выбор SQL Управляемый экземпляр](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. На странице " **создание управляемый экземпляр Azure SQL** " на вкладке " **основы** " выполните следующие действия.
    1. В разделе **сведения о проекте**выберите **подписку** из раскрывающегося списка, а затем выберите **создать новую** группу ресурсов. Введите имя группы ресурсов, например `myResourceGroup` . 
    1. В разделе **сведения о управляемый экземпляр SQL**укажите имя управляемого экземпляра и регион, в котором вы хотите развернуть управляемый экземпляр. Оставьте значение " **вычислить и хранилище** " в значениях по умолчанию. 
    1. В разделе **учетная запись администратора**укажите имя входа администратора, например `azureuser` , и сложный пароль администратора. 

    ![Создание основного управляемого экземпляра](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. Оставьте остальные параметры со значениями по умолчанию и выберите Просмотр и **Создание** , чтобы проверить параметры SQL управляемый экземпляр. 
1. Выберите **создать** , чтобы создать основной управляемый экземпляр. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте группу ресурсов и основной управляемый экземпляр с помощью PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the SQL Managed Instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server license that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary SQL Managed Instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary SQL Managed Instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary SQL Managed Instance name is" $primaryInstance
   Write-host "Secondary SQL Managed Instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set the subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create the resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure the primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure the primary managed instance subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure the network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure the route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create the primary managed instance
   
   Write-host "Creating primary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary SQL Managed Instance created successfully."
   ```

В этой части руководства используются следующие командлеты PowerShell:

| Get-Help | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группы ресурсов Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Создает виртуальную сеть.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Добавляет конфигурацию подсети в виртуальную сеть. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Получает виртуальную сеть в группе ресурсов. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Получает подсеть в виртуальной сети. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Создает группу безопасности сети. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Создает таблицу маршрутизации. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Обновляет конфигурацию подсети для виртуальной сети.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Обновляет виртуальную сеть.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Возвращает группу безопасности сети. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Добавляет в группу безопасности сети конфигурацию правила безопасности сети. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Обновляет группу безопасности сети.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Добавляет маршрут в таблицу маршрутизации. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Обновляет таблицу маршрутизации.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Создает управляемый экземпляр  |

---

## <a name="2---create-secondary-virtual-network"></a>2. Создание вторичной виртуальной сети

Если вы используете портал Azure для создания управляемого экземпляра, необходимо отдельно создать виртуальную сеть, так как в подсети первичного и вторичного управляемых экземпляров нет перекрывающихся диапазонов. Если вы используете PowerShell для настройки управляемого экземпляра, перейдите к шагу 3. 

# <a name="portal"></a>[Портал](#tab/azure-portal) 

Чтобы проверить диапазон подсети основной виртуальной сети, выполните следующие действия.

1. В [портал Azure](https://portal.azure.com)перейдите к группе ресурсов и выберите виртуальную сеть для основного экземпляра.  
2. Выберите **подсети** в разделе **Параметры** и запишите **диапазон адресов**. Диапазон адресов подсети виртуальной сети для вторичного управляемого экземпляра не может перекрывать эту область. 


   ![Основная подсеть](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

Чтобы создать виртуальную сеть, выполните следующие действия.

1. В [портал Azure](https://portal.azure.com)выберите **создать ресурс** и выполните поиск по запросу *Виртуальная сеть*. 
1. Выберите параметр **виртуальной сети** , опубликованный корпорацией Майкрософт, а затем нажмите кнопку **создать** на следующей странице. 
1. Заполните обязательные поля, чтобы настроить виртуальную сеть для вторичного управляемого экземпляра, а затем нажмите кнопку **создать**. 

   В следующей таблице приведены значения, необходимые для вторичной виртуальной сети.

    | **Поле** | Значение |
    | --- | --- |
    | **имя**; |  Имя виртуальной сети, используемой дополнительным управляемым экземпляром, например `vnet-sql-mi-secondary` . |
    | **Пространство адресов** | Адресное пространство виртуальной сети, например `10.128.0.0/16` . | 
    | **Подписка** | Подписка, в которой находятся основные управляемые экземпляры и группы ресурсов. |
    | **Регион** | Расположение, в которое будет развертываться вторичный управляемый экземпляр. |
    | **Подсеть** | Имя вашей подсети. `default`по умолчанию предоставляется для вас. |
    | **Диапазон адресов**| Диапазон адресов для вашей подсети. Это значение должно отличаться от диапазона адресов подсети, используемого виртуальной сетью основного управляемого экземпляра, например `10.128.0.0/24` .  |
    | &nbsp; | &nbsp; |

    ![Значения вторичной виртуальной сети](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Этот шаг необходим только в том случае, если вы используете портал Azure для развертывания Управляемый экземпляр SQL. Если вы используете PowerShell, перейдите к шагу 3. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3. Создание вторичного управляемого экземпляра
На этом шаге вы создадите вторичный управляемый экземпляр на портал Azure, который также будет настраивать сетевые подключения между двумя управляемыми экземплярами. 

Второй управляемый экземпляр должен:
- Быть пустым. 
- Имеют разные подсети и диапазоны IP-адресов, чем у основного управляемого экземпляра. 

# <a name="portal"></a>[Портал](#tab/azure-portal) 

Создайте вторичный управляемый экземпляр с помощью портал Azure. 

1. На портале Azure в меню слева выберите **Azure SQL**. Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите `Azure SQL` в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Щелкните **+ Добавить**, чтобы открыть страницу **Выбор варианта развертывания SQL**. Чтобы просмотреть дополнительные сведения о различных базах данных, выберите **Показать сведения** на плитке **Базы данных**.
1. Выберите **создать** на плитке **управляемые экземпляры SQL** . 

    ![Выбор SQL Управляемый экземпляр](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. На вкладке **Основные сведения** страницы **Создание управляемый экземпляр Azure SQL** введите необходимые поля для настройки вторичного управляемого экземпляра. 

   В следующей таблице приведены значения, необходимые для вторичного управляемого экземпляра.
 
    | **Поле** | Значение |
    | --- | --- |
    | **Подписка** |  Подписка, в которой находится основной управляемый экземпляр. |
    | **Группа ресурсов**| Группа ресурсов, в которой находится основной управляемый экземпляр. |
    | **Имя Управляемый экземпляр SQL** | Имя нового вторичного управляемого экземпляра, например `sql-mi-secondary` .  | 
    | **Регион**| Расположение для вторичного управляемого экземпляра.  |
    | **Имя для входа администратора SQL Управляемый экземпляр** | Имя входа, которое будет использоваться для нового вторичного управляемого экземпляра, например `azureuser` . |
    | **Пароль** | Сложный пароль, который будет использоваться именем входа администратора для нового вторичного управляемого экземпляра.  |
    | &nbsp; | &nbsp; |

1. На вкладке **Сетевые подключения** для **виртуальной сети**выберите виртуальную сеть, созданную для дополнительного управляемого экземпляра, из раскрывающегося списка.

   ![Сеть дополнительных MI](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. На вкладке **Дополнительные параметры** для параметра **Георепликация**выберите **Да** для _использования в качестве вторичной отработки отказа_. Выберите основной управляемый экземпляр из раскрывающегося списка. 
    
   Убедитесь, что параметры сортировки и часовой пояс соответствуют основному управляемому экземпляру. В основном управляемом экземпляре, созданном в этом учебнике, использовалось по умолчанию `SQL_Latin1_General_CP1_CI_AS` Параметры сортировки и `(UTC) Coordinated Universal Time` Часовой пояс. 

   ![Сеть вторичного управляемого экземпляра](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. Выберите **проверить и создать** , чтобы проверить параметры для вторичного управляемого экземпляра. 
1. Выберите **создать** , чтобы создать вторичный управляемый экземпляр. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте вторичный управляемый экземпляр с помощью PowerShell. 

   ```powershell-interactive
   # Configure the secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure the secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure the secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure the secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create the secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary SQL Managed Instance created successfully."
   ```

В этой части руководства используются следующие командлеты PowerShell:

| Get-Help | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группы ресурсов Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Создает виртуальную сеть.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Добавляет конфигурацию подсети в виртуальную сеть. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Получает виртуальную сеть в группе ресурсов. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Получает подсеть в виртуальной сети. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Создает группу безопасности сети. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Создает таблицу маршрутизации. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Обновляет конфигурацию подсети для виртуальной сети.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Обновляет виртуальную сеть.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Возвращает группу безопасности сети. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Добавляет в группу безопасности сети конфигурацию правила безопасности сети. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Обновляет группу безопасности сети.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Добавляет маршрут в таблицу маршрутизации. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Обновляет таблицу маршрутизации.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Создает управляемый экземпляр  |

---

## <a name="4---create-a-primary-gateway"></a>4. Создание основного шлюза 

Чтобы два управляемых экземпляра участвовали в группе отработки отказа, необходимо настроить ExpressRoute или шлюз, настроенный между виртуальными сетями двух управляемых экземпляров, чтобы разрешить сетевое взаимодействие. Если вы решили настроить [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) вместо подключения двух VPN-шлюзов, перейдите к [шагу 7](#7---create-a-failover-group).  

В этой статье приводятся шаги по созданию двух VPN-шлюзов и их подключению, но вы можете сразу перейти к созданию группы отработки отказа, если вместо этого вы настроили ExpressRoute. 


# <a name="portal"></a>[Портал](#tab/azure-portal)

Создайте шлюз для виртуальной сети основного управляемого экземпляра с помощью портал Azure. 


1. В [портал Azure](https://portal.azure.com)перейдите к группе ресурсов и выберите ресурс **виртуальной сети** для основного управляемого экземпляра. 
1. Выберите **подсети** в разделе **Параметры** , а затем выберите, чтобы добавить новую **подсеть шлюза**. Оставьте значения по умолчанию. 

   ![Добавление шлюза для основного управляемого экземпляра](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. После создания шлюза подсети выберите **создать ресурс** в левой области навигации, а затем введите `Virtual network gateway` в поле поиска. Выберите ресурс **шлюза виртуальной сети** , опубликованный **корпорацией Майкрософт**. 

   ![Создание нового шлюза виртуальной сети](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. Заполните обязательные поля, чтобы настроить шлюз для основного управляемого экземпляра. 

   В следующей таблице приведены значения, необходимые для шлюза для основного управляемого экземпляра.
 
    | **Поле** | Значение |
    | --- | --- |
    | **Подписка** |  Подписка, в которой находится основной управляемый экземпляр. |
    | **Name** | Имя шлюза виртуальной сети, например `primary-mi-gateway` . | 
    | **Регион** | Регион, в котором находится основной управляемый экземпляр. |
    | **Тип шлюза** | Выберите **VPN**. |
    | **Тип VPN** | Выберите **На основе маршрута**. |
    | **SKU**| Оставьте значение по умолчанию для `VpnGw1` . |
    | **Расположение**| Расположение, в котором находится основной управляемый экземпляр и первичная виртуальная сеть.   |
    | **Виртуальная сеть**| Выберите виртуальную сеть, созданную в разделе 2, например `vnet-sql-mi-primary` . |
    | **Общедоступный IP-адрес**| Выберите **Создать**. |
    | **Имя общедоступного IP-адреса**| Введите имя IP-адреса, например `primary-gateway-IP` . |
    | &nbsp; | &nbsp; |

1. Оставьте остальные значения по умолчанию, а затем выберите **проверить и создать** , чтобы проверить параметры шлюза виртуальной сети.

   ![Параметры основного шлюза](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. Выберите **создать** , чтобы создать новый шлюз виртуальной сети. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте шлюз для виртуальной сети основного управляемого экземпляра с помощью PowerShell. 

   ```powershell-interactive
   # Create the primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

В этой части руководства используются следующие командлеты PowerShell:

| Get-Help | Примечания |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Получает виртуальную сеть в группе ресурсов. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Добавляет конфигурацию подсети в виртуальную сеть. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Обновляет виртуальную сеть.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Получает подсеть в виртуальной сети. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Создает IP-конфигурацию для шлюза виртуальной сети. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Создает шлюз виртуальной сети. |


---


## <a name="5---create-secondary-gateway"></a>5. Создание вторичного шлюза 
На этом шаге создайте шлюз для виртуальной сети вторичного управляемого экземпляра с помощью портал Azure. 


# <a name="portal"></a>[Портал](#tab/azure-portal)

С помощью портал Azure повторите действия, описанные в предыдущем разделе, чтобы создать подсеть и шлюз виртуальной сети для вторичного управляемого экземпляра. Заполните обязательные поля, чтобы настроить шлюз для вторичного управляемого экземпляра. 

   В следующей таблице приведены значения, необходимые для шлюза для вторичного управляемого экземпляра.

   | **Поле** | Значение |
   | --- | --- |
   | **Подписка** |  Подписка, в которой находится экземпляр вторичного управляемого экземпляра. |
   | **Name** | Имя шлюза виртуальной сети, например `secondary-mi-gateway` . | 
   | **Регион** | Регион, где находится ваш вторичный управляемый экземпляр. |
   | **Тип шлюза** | Выберите **VPN**. |
   | **Тип VPN** | Выберите **На основе маршрута**. |
   | **SKU**| Оставьте значение по умолчанию для `VpnGw1` . |
   | **Расположение**| Расположение, в котором находится дополнительный управляемый экземпляр и вторичная виртуальная сеть.   |
   | **Виртуальная сеть**| Выберите виртуальную сеть, созданную в разделе 2, например `vnet-sql-mi-secondary` . |
   | **Общедоступный IP-адрес**| Выберите **Создать**. |
   | **Имя общедоступного IP-адреса**| Введите имя IP-адреса, например `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Параметры дополнительного шлюза](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте шлюз для виртуальной сети вторичного управляемого экземпляра с помощью PowerShell. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

В этой части руководства используются следующие командлеты PowerShell:

| Get-Help | Примечания |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Получает виртуальную сеть в группе ресурсов. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Добавляет конфигурацию подсети в виртуальную сеть. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Обновляет виртуальную сеть.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Получает подсеть в виртуальной сети. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Создает IP-конфигурацию для шлюза виртуальной сети. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Создает шлюз виртуальной сети. |

---


## <a name="6---connect-the-gateways"></a>6. Подключение шлюзов
На этом шаге создайте двунаправленное подключение между двумя шлюзами двух виртуальных сетей. 


# <a name="portal"></a>[Портал](#tab/azure-portal)

Подключите два шлюза с помощью портал Azure. 


1. Выберите **создать ресурс** на [портал Azure](https://portal.azure.com).
1. Введите `connection` в поле поиска и нажмите клавишу ВВОД для поиска, который переводит вас на ресурс **подключения** , опубликованный корпорацией Майкрософт.
1. Выберите **создать** , чтобы создать подключение. 
1. На вкладке **Основные сведения** выберите следующие значения и нажмите кнопку **ОК**. 
    1. Выберите `VNet-to-VNet` для **типа соединения**. 
    1. Выберите подписку в раскрывающемся списке. 
    1. В раскрывающемся списке выберите группу ресурсов для Управляемый экземпляр SQL. 
    1. Выберите расположение основного управляемого экземпляра из раскрывающегося списка. 
1. На вкладке **Параметры** выберите или введите следующие значения, а затем нажмите кнопку **ОК**.
    1. Выберите основной сетевой шлюз для **первого шлюза виртуальной сети**, например `Primary-Gateway` .  
    1. Выберите дополнительный сетевой шлюз для **второго шлюза виртуальной сети**, например `Secondary-Gateway` . 
    1. Установите флажок рядом с пунктом **установить двунаправленное подключение**. 
    1. Либо оставьте имя основного подключения по умолчанию, либо переименуйте его в нужное значение. 
    1. Укажите **общий ключ (PSK)** для подключения, например `mi1m2psk` . 

   ![Создание подключения к шлюзу](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

1. На вкладке **Сводка** проверьте параметры двунаправленного подключения и нажмите кнопку **ОК** , чтобы создать подключение. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Подключите два шлюза с помощью PowerShell. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

В этой части руководства используется следующий командлет PowerShell:

| Get-Help | Примечания |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Создает подключение между двумя шлюзами виртуальной сети.   |

---


## <a name="7---create-a-failover-group"></a>7. Создание группы отработки отказа
На этом шаге вы создадите группу отработки отказа и добавите в нее оба управляемых экземпляра. 


# <a name="portal"></a>[Портал](#tab/azure-portal)
Создайте группу отработки отказа с помощью портал Azure. 


1. Выберите **Azure SQL** в меню слева [портал Azure](https://portal.azure.com). Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите `Azure SQL` в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите основной управляемый экземпляр, созданный в первом разделе, например `sql-mi-primary` . 
1. В разделе **Параметры**перейдите к **экземпляру группы отработки отказа** и нажмите кнопку **Добавить группу** , чтобы открыть страницу **группы отработки отказа экземпляра** . 

   ![Добавление группы отработки отказа](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. На странице **Группа отработки отказа** введите имя группы отработки отказа, например `failovergrouptutorial` . Затем в раскрывающемся списке выберите дополнительный управляемый экземпляр, например `sql-mi-secondary` . Выберите **создать** , чтобы создать группу отработки отказа. 

   ![Создание группы отработки отказа](./media/failover-group-add-instance-tutorial/create-failover-group.png)

1. После завершения развертывания группы отработки отказа будет выполнен переход на страницу **группы отработки отказа** . 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Создайте группу отработки отказа с помощью PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

В этой части руководства используется следующий командлет PowerShell:

| Get-Help | Примечания |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Создает новую группу отработки отказа Управляемого экземпляра SQL Azure.  |


---


## <a name="8---test-failover"></a>8. тестовая отработка отказа
На этом шаге группа отработки отказа будет передаваться на сервер-получатель, а затем восстановлена с помощью портал Azure. 


# <a name="portal"></a>[Портал](#tab/azure-portal)
Тестовая отработка отказа с помощью портал Azure. 


1. Перейдите к _дополнительному_ управляемому экземпляру в [портал Azure](https://portal.azure.com) и выберите **группы отработки отказа экземпляра** в разделе Параметры. 
1. Проверьте, какой управляемый экземпляр является первичным и какой управляемый экземпляр является вторичным. 
1. Выберите **отработка отказа** и нажмите **кнопку Да** в предупреждении о том, что сеансы TDS отключены. 

   ![Переключение группы отработки отказа](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. Проверьте, какой управляемый экземпляр является источником и управляемым экземпляром является вторичным. Если отработка отказа прошла успешно, два экземпляра должны иметь переключаемые роли. 

   ![Управляемые экземпляры с переключением ролей после отработки отказа](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. Перейдите к новому управляемому экземпляру- _получателю_ и снова выберите **отработку отказа** , чтобы восстановить первичный экземпляр в первичную роль. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Тестовая отработка отказа с помощью PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Fail over the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Верните группу отработки отказа обратно на сервер источника:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail the primary managed instance back to the primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

В этой части руководства используются следующие командлеты PowerShell:

| Get-Help | Примечания |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Возвращает или перечисляет группы отработки отказа Управляемого экземпляра SQL.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Выполняет отработку отказа для группы отработки отказа Управляемого экземпляра SQL. | 

---



## <a name="clean-up-resources"></a>Очистка ресурсов
Очистите ресурсы, сначала удалив управляемые экземпляры, затем виртуальный кластер, остальные ресурсы и, наконец, группу ресурсов. 

# <a name="portal"></a>[Портал](#tab/azure-portal)
1. На [портале Azure](https://portal.azure.com) перейдите к используемой группе ресурсов. 
1. Выберите один или несколько управляемых экземпляров и щелкните **Удалить**. Введите `yes` в текстовое поле, чтобы подтвердить удаление ресурса, а затем щелкните **Удалить**. Этот процесс некоторое время выполняется в фоновом режиме, и до его завершения вы не сможете удалить *виртуальный кластер* и другие зависимые ресурсы. Отслеживайте удаление на вкладке **действие** , чтобы подтвердить удаление управляемого экземпляра. 
1. После удаления управляемого экземпляра удалите *виртуальный кластер*, выбрав его в группе ресурсов и щелкнув **Удалить**. Введите `yes` в текстовое поле, чтобы подтвердить удаление ресурса, а затем щелкните **Удалить**. 
1. Удалите все остальные ресурсы. Введите `yes` в текстовое поле, чтобы подтвердить удаление ресурса, а затем щелкните **Удалить**. 
1. Удалите группу ресурсов, выбрав **Удалить группу ресурсов**, введя в текстовом поле имя группы ресурсов `myResourceGroup` и щелкнув **Удалить**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Группу ресурсов необходимо будет удалить дважды. При первом удалении группы ресурсов будут удалены и управляемые экземпляры, и виртуальные кластеры, но после этого будет выдаваться сообщение об ошибке `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` . Выполните команду Remove-AzResourceGroup еще раз, чтобы удалить все остаточные ресурсы, а также группу ресурсов.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resource group..."
```

В этой части руководства используется следующий командлет PowerShell:

| Get-Help | Примечания |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. |

---

## <a name="full-script"></a>Полный сценарий

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add SQL Managed Instance to a failover group")]

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группы ресурсов Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Создает виртуальную сеть.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Добавляет конфигурацию подсети в виртуальную сеть. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Получает виртуальную сеть в группе ресурсов. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Получает подсеть в виртуальной сети. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Создает группу безопасности сети. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Создает таблицу маршрутизации. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Обновляет конфигурацию подсети для виртуальной сети.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Обновляет виртуальную сеть.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Возвращает группу безопасности сети. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Добавляет в группу безопасности сети конфигурацию правила безопасности сети. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Обновляет группу безопасности сети.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Добавляет маршрут в таблицу маршрутизации. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Обновляет таблицу маршрутизации.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Создает управляемый экземпляр  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Возвращает информацию об Управляемом экземпляре SQL Azure. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Создает IP-конфигурацию для шлюза виртуальной сети. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Создает шлюз виртуальной сети. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Создает подключение между двумя шлюзами виртуальной сети.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Создает новую группу отработки отказа SQL Управляемый экземпляр.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Возвращает или перечисляет группы отработки отказа Управляемого экземпляра SQL.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Выполняет отработку отказа для группы отработки отказа Управляемого экземпляра SQL. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. | 

# <a name="portal"></a>[Портал](#tab/azure-portal) 

Для портал Azure нет доступных скриптов.

---

## <a name="next-steps"></a>Дальнейшие шаги

В этом руководстве вы настроили группу отработки отказа между двумя управляемыми экземплярами. Вы ознакомились с выполнением следующих задач:

> [!div class="checklist"]
> - Создайте основной управляемый экземпляр.
> - Создайте вторичный управляемый экземпляр в составе [группы отработки отказа](../database/auto-failover-group-overview.md). 
> - Тестовая отработка отказа.

Перейдите к следующему краткому руководству по подключению к SQL Управляемый экземпляр и восстановлению базы данных в SQL Управляемый экземпляр. 

> [!div class="nextstepaction"]
> [Подключение к SQL управляемый экземпляр](connect-vm-instance-configure.md) 
>  [Восстановление базы данных в SQL управляемый экземпляр](restore-sample-database-quickstart.md)


