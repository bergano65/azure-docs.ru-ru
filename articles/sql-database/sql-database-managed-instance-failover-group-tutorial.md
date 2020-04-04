---
title: 'Учебник: Добавление управляемого экземпляра в группу неудач'
description: Научитесь настраивать группу сбоев для управляемого экземпляра базы данных Azure S'L.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: 0f1a56fa6ea38acd8061180407eb47fe416b61e9
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631699"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Учебник: Добавьте управляемый экземпляр базы данных S'L в группу неудач

Добавление управляемого экземпляра базы данных SQL в группу отработки отказа. В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> - Создание первичного управляемого экземпляра
> - Создайте вторичный управляемый экземпляр как часть [группы failover.](sql-database-auto-failover-group.md) 
> - Тестовая отработка отказа

  > [!NOTE]
  > - При прохождении этого учебника убедитесь, что вы настраиваете свои ресурсы с [предпосылками для создания групп failover для управляемого экземпляра.](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets) 
  > - Создание управляемого экземпляра может занять значительное время. В результате, этот учебник может занять несколько часов. Для получения дополнительной информации о времени подготовки [см.](sql-database-managed-instance.md#managed-instance-management-operations) 
  > - Управляемые экземпляры, участвующие в группе failover, требуют либо [ExpressRoute,](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) либо двух подключенных VPN шлюзов. В этом уроке представлены шаги по созданию и подключению VPN шлюзов. Пропустите эти шаги, если у вас уже есть ExpressRoute настроен. 


## <a name="prerequisites"></a>Предварительные требования

# <a name="portal"></a>[Портал](#tab/azure-portal)
В рамках этого руководства вам потребуются: 

- Подписка Azure. [Создайте бесплатную учетную запись,](https://azure.microsoft.com/free/) если у вас ее еще нет.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Чтобы завершить обучение, убедитесь, что у вас есть следующие элементы:

- Подписка Azure. [Создайте бесплатную учетную запись,](https://azure.microsoft.com/free/) если у вас ее еще нет.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1 - Создание группы ресурсов и первичного управляемого экземпляра
На этом этапе вы создадите группу ресурсов и основной управляемый экземпляр для группы неудач с помощью портала Azure или PowerShell. 


# <a name="portal"></a>[Портал](#tab/azure-portal) 

Создайте группу ресурсов и основной управляемый экземпляр с помощью портала Azure. 

1. В левом меню портала Azure выберите **Azure S'L.** Если **Azure SQL** отсутствует в списке, выберите **Все службы** и в поле поиска введите Azure SQL. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Щелкните **+ Добавить**, чтобы открыть страницу **Выбор варианта развертывания SQL**. Чтобы просмотреть дополнительные сведения о различных базах данных, выберите Показать сведения на плитке Базы данных.
1. Выберите **Создать** на **плитке управляемых экземпляров S'L.** 

    ![Выберите управляемый экземпляр](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. На странице **«Создание управляемых экземпляров базы данных Azure»** на вкладке **«Основы»**
    1. В соответствии с **деталями проекта,** выберите **подписку** из выпадающих, а затем выберите **создать новую** группу ресурсов. Введите имя для группы `myResourceGroup`ресурсов, например. 
    1. В **данных управляемых экземпляров**укажите имя вашего управляемого экземпляра и область, в которой вы хотите развернуть управляемый экземпляр. Оставьте **хранилище вычислений и вычислений** по умолчанию. 
    1. В **учетной записи администратора**укажите логин администратора, `azureuser`например, и сложный пароль администратора. 

    ![Создание первичного MI](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Оставьте остальные параметры на значениях по умолчанию и выберите **«Обзор» и создайте** для просмотра параметров управляемых экземпляров. 
1. Выберите **Создать** для создания основного управляемого экземпляра. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте группу ресурсов и основной управляемый экземпляр с помощью PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your managed instance
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
   
   
   
   # Set the managed instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your managed instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the managed instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary managed instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary managed instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary managed instance name is" $primaryInstance
   Write-host "Secondary managed instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure primary virtual network
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
   
   
   # Configure primary MI subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure route table management service
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
   
   
   # Create primary managed instance
   
   Write-host "Creating primary managed instance..."
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
   Write-host "Primary managed instance created successfully."
   ```

Эта часть учебника использует следующие PowerShell cmdlets:

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
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Создает управляемый экземпляр Базы данных SQL Azure.  |

---

## <a name="2---create-secondary-virtual-network"></a>2 - Создание вторичной виртуальной сети
Если вы используете портал Azure для создания управляемого экземпляра, необходимо создать виртуальную сеть отдельно, поскольку существует требование о том, что подсеть первичного и вторичного управляемого экземпляра не имеет перекрывающихся диапазонов. Если вы используете PowerShell для настройки управляемого экземпляра, перейдите вперед к шагу 3. 

# <a name="portal"></a>[Портал](#tab/azure-portal) 
Чтобы проверить диапазон подсетей вашей основной виртуальной сети, выполните следующие действия:
1. На [портале Azure](https://portal.azure.com)перейдите к группе ресурсов и выберите виртуальную сеть для основного экземпляра. 
1. Выберите **подсети** в **настройках** и обратите внимание на **диапазон Адресов.** Диапазон адресов подсети виртуальной сети для вторичного управляемого экземпляра не может перекрывать это. 


   ![Первичная подсеть](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Чтобы создать виртуальную сеть, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com)выберите Создать ресурс и найти **виртуальную** *сеть.* 
1. Выберите опцию **Virtual Network,** опубликованную корпорацией Майкрософт, а затем выберите **«Создать»** на следующей странице. 
1. Заполните необходимые поля для настройки виртуальной сети для вторичного управляемого экземпляра, а затем выберите **Create.** 

   В следующей таблице показаны значения, необходимые для вторичной виртуальной сети:

    | **Поле** | Значение |
    | --- | --- |
    | **имя**; |  Название виртуальной сети, используемой вторичным управляемым `vnet-sql-mi-secondary`экземпляром, например. |
    | **Адресное пространство** | Адресное пространство для виртуальной `10.128.0.0/16`сети, например. | 
    | **Подписка** | Подписка, в которой проживает основная управляемая группа и группа ресурсов. |
    | **Регион** | Место, где вы развернете вторичную управляемую экземпляр. |
    | **Подсети** | Название вашей подсети. `default`предоставляется вам по умолчанию. |
    | **Диапазон адресов**| Диапазон адресов для вашей подсети. Это должно отличаться от диапазона адресов подсети, используемого виртуальной `10.128.0.0/24`сетью вашего основного управляемого экземпляра, например.  |
    | &nbsp; | &nbsp; |

    ![Вторичные значения виртуальной сети](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Этот шаг необходим только в том случае, если вы используете портал Azure для развертывания управляемого экземпляра. Перейти вперед, чтобы шаг 3, если вы используете PowerShell. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3 - Создание вторичного управляемого экземпляра
На этом этапе на портале Azure будет создан вторичный управляемый экземпляр, который также настраивает сеть между двумя управляемыми экземплярами. 

Второй управляемый экземпляр должен:
- Будьпуст. 
- Иметь другой диапазон подсети и IP, чем первичный управляемый экземпляр. 

# <a name="portal"></a>[Портал](#tab/azure-portal) 

Создайте вторичный управляемый экземпляр с помощью портала Azure. 

1. В левом меню портала Azure выберите **Azure S'L.** Если **Azure SQL** отсутствует в списке, выберите **Все службы** и в поле поиска введите Azure SQL. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Щелкните **+ Добавить**, чтобы открыть страницу **Выбор варианта развертывания SQL**. Чтобы просмотреть дополнительные сведения о различных базах данных, выберите Показать сведения на плитке Базы данных.
1. Выберите **Создать** на **плитке управляемых экземпляров S'L.** 

    ![Выберите управляемый экземпляр](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. На **вкладке «Основы»** страницы **«Создание Azure S'L Database Managed Instance»** заполните необходимые поля для настройки вторичного управляемого экземпляра. 

   В следующей таблице показаны значения, необходимые для вторичного управляемого экземпляра:
 
    | **Поле** | Значение |
    | --- | --- |
    | **Подписка** |  Подписка, в которой находится основной управляемый экземпляр. |
    | **Группа ресурсов**| Группа ресурсов, в которой находится основной управляемый экземпляр. |
    | **Имя управляемого экземпляра** | Имя вашего нового вторичного управляемого экземпляра, например`sql-mi-secondary`  | 
    | **Регион**| Местоположение для вашего вторичного управляемого экземпляра.  |
    | **Имя для входа администратора управляемого экземпляра** | Логин, который вы хотите использовать для нового `azureuser`вторичного управляемого экземпляра, например. |
    | **Пароль** | Сложный пароль, который будет использоваться для входа в систему admin для нового вторичного управляемого экземпляра.  |
    | &nbsp; | &nbsp; |

1. Под вкладкой **Networking** для **виртуальной сети**выберите виртуальную сеть, созданную для вторичного управляемого экземпляра, из выпадения вниз.

   ![Вторичные сети MI](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Под **вкладкой Дополнительные настройки** для **Geo-Replication**выберите **«Да»** для использования в _качестве вторичного сбоя._ Выберите основной управляемый экземпляр из выпадения. 
    1. Убедитесь, что коллаж и часовой пояс совпадает с тем, что в первичном управляемом экземпляре. Основной управляемый экземпляр, созданный `SQL_Latin1_General_CP1_CI_AS` в этом `(UTC) Coordinated Universal Time` учебнике, использовал по умолчанию коллажа и часовой пояс. 

   ![Вторичные сети MI](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Выберите **Обзор и создайте** для просмотра параметров для вторичного управляемого экземпляра. 
1. Выберите **Создать** для создания вторичного управляемого экземпляра. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте вторичный управляемый экземпляр с помощью PowerShell. 

   ```powershell-interactive
   # Configure secondary virtual network
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
   
   # Configure secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure secondary route table MI management service
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
   
   # Create secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary managed instance..."
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
   Write-host "Secondary managed instance created successfully."
   ```

Эта часть учебника использует следующие PowerShell cmdlets:

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
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Создает управляемый экземпляр Базы данных SQL Azure.  |

---

## <a name="4---create-primary-gateway"></a>4 - Создание первичного шлюза 
Для двух управляемых экземпляров для участия в группе сбоев должен быть либо ExpressRoute, либо шлюз, настроенный между виртуальными сетями двух управляемых экземпляров, чтобы обеспечить сетевую связь. Если вы решите настроить [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) вместо подключения двух VPN шлюзов, перейдите вперед к [шагу 7.](#7---create-a-failover-group)  

В этой статье представлены шаги по созданию двух VPN шлюзов и их подключению, но вы можете перейти к созданию группы неудач, если вы настроили ExpressRoute вместо этого. 


# <a name="portal"></a>[Портал](#tab/azure-portal)

Создайте шлюз для виртуальной сети основного управляемого экземпляра с помощью портала Azure. 


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
    | **имя**; | Название для вашего виртуального `primary-mi-gateway`сетевого шлюза, например. | 
    | **Регион** | Регион, в котором находится ваш основной управляемый экземпляр. |
    | **Тип шлюза** | Выберите **VPN**. |
    | **Тип VPN** | Выберите **маршрут на основе** |
    | **SKU**| Оставьте `VpnGw1`по умолчанию . |
    | **Расположение**| Место, где находится ваш основной управляемый экземпляр и основная виртуальная сеть.   |
    | **Виртуальная сеть**| Выберите виртуальную сеть, которая была `vnet-sql-mi-primary`создана в разделе 2, например. |
    | **Общественный IP-адрес**| Выберите **Создать**. |
    | **Имя общедоступного IP-адреса**| Введите имя для вашего `primary-gateway-IP`IP-адреса, например. |
    | &nbsp; | &nbsp; |

1. Оставьте другие значения по умолчанию, а затем выберите **Обзор и создайте** для просмотра настроек для вашего виртуального сетевого шлюза.

   ![Настройки первичного шлюза](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Выберите **Создать** для создания нового виртуального сетевого шлюза. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте шлюз для виртуальной сети вашего основного управляемого экземпляра с помощью PowerShell. 

   ```powershell-interactive
   # Create primary gateway
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

Эта часть учебника использует следующие PowerShell cmdlets:

| Get-Help | Примечания |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Получает виртуальную сеть в группе ресурсов. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Добавляет конфигурацию подсети в виртуальную сеть. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Обновляет виртуальную сеть.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Получает подсеть в виртуальной сети. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Создает IP-конфигурацию для шлюза виртуальной сети. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Создание шлюза виртуальной сети |


---


## <a name="5---create-secondary-gateway"></a>5 - Создание вторичного шлюза 
На этом этапе создайте шлюз для виртуальной сети вашего вторичного управляемого экземпляра с помощью портала Azure, 


# <a name="portal"></a>[Портал](#tab/azure-portal)

Используя портал Azure, повторите шаги в предыдущем разделе, чтобы создать виртуальную сетевую подсеть и шлюз для вторичного управляемого экземпляра. Заполните необходимые поля для настройки шлюза для вторичного управляемого экземпляра. 

   В следующей таблице показаны значения, необходимые для шлюза для вторичного управляемого экземпляра:

   | **Поле** | Значение |
   | --- | --- |
   | **Подписка** |  Подписка, в которой находится ваш вторичный управляемый экземпляр. |
   | **имя**; | Название для вашего виртуального `secondary-mi-gateway`сетевого шлюза, например. | 
   | **Регион** | Регион, в котором находится ваш вторичный управляемый экземпляр. |
   | **Тип шлюза** | Выберите **VPN**. |
   | **Тип VPN** | Выберите **маршрут на основе** |
   | **SKU**| Оставьте `VpnGw1`по умолчанию . |
   | **Расположение**| Место, где находится ваш вторичный управляемый экземпляр и второстепенная виртуальная сеть.   |
   | **Виртуальная сеть**| Выберите виртуальную сеть, которая была `vnet-sql-mi-secondary`создана в разделе 2, например. |
   | **Общественный IP-адрес**| Выберите **Создать**. |
   | **Имя общедоступного IP-адреса**| Введите имя для вашего `secondary-gateway-IP`IP-адреса, например. |
   | &nbsp; | &nbsp; |

   ![Вторичные настройки шлюза](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)


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

Эта часть учебника использует следующие PowerShell cmdlets:

| Get-Help | Примечания |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Получает виртуальную сеть в группе ресурсов. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Добавляет конфигурацию подсети в виртуальную сеть. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Обновляет виртуальную сеть.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Получает подсеть в виртуальной сети. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Создает IP-конфигурацию для шлюза виртуальной сети. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Создание шлюза виртуальной сети |

---


## <a name="6---connect-the-gateways"></a>6 - Подключите шлюзы
На этом этапе создайте двунаправленное соединение между двумя шлюзами двух виртуальных сетей. 


# <a name="portal"></a>[Портал](#tab/azure-portal)

Подключите два шлюза с помощью портала Azure. 


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

Эта часть учебника использует следующие PowerShell cmdlet:

| Get-Help | Примечания |
|---|---|
| [New-AzVirtualNetworkGatewayConnection;](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Создает подключение между двумя шлюзами виртуальной сети.   |

---


## <a name="7---create-a-failover-group"></a>7 - Создание группы неудач
На этом этапе вы создадите группу failover и добавите в нее оба управляемых экземпляра. 


# <a name="portal"></a>[Портал](#tab/azure-portal)
Создайте группу неудач с помощью портала Azure. 


1. На [портале Azure](https://portal.azure.com) в меню слева выберите **Azure SQL**. Если **Azure SQL** отсутствует в списке, выберите **Все службы** и в поле поиска введите Azure SQL. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите основной управляемый экземпляр, созданный `sql-mi-primary`в первом разделе, например. 
1. В **настройках**перейдите в **группы Instance Failover,** а затем выберите **группу добавления,** чтобы открыть страницу **группы Instance Failover.** 

   ![Добавление группы неудач](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. На странице **Instance Failover Group** введите имя группы `failovergrouptutorial` неудач, например, а `sql-mi-secondary` затем выберите вторичный управляемый экземпляр, например, из выпадения. Выберите **Создать** для создания группы неудач. 

   ![Создание группы отработки отказа](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. После завершения развертывания группы failover вы будете возвращены на страницу **группы Failover.** 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Создайте группу неудач с помощью PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

Эта часть учебника использует следующие PowerShell cmdlet:

| Get-Help | Примечания |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Создает новую группу отработки отказа управляемого экземпляра Базы данных SQL Azure.  |


---


## <a name="8---test-failover"></a>8 - Тест неудачи
На этом этапе вы сведет е-группу неудачи на вторичный сервер, а затем сбой сможете вернуться с помощью портала Azure. 


# <a name="portal"></a>[Портал](#tab/azure-portal)
Тест-неудача с помощью портала Azure. 


1. Перейдите к _второму_ управляемому экземпляру в [рамках портала Azure](https://portal.azure.com) и выберите **группы Failover instance** в настройках. 
1. Обзор, который управлял экземпляром является основным, и который управляемый экземпляр является вторичным. 
1. Выберите **Failover,** а затем выберите **Yes** на предупреждение о отключениях сеансов TDS. 

   ![Fail over the failover group](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Просмотрите, какой экземпляр является основным, а какой экземпляр - вторичным. Если неудача закончилась, эти два экземпляра должны были поменяться ролями. 

   ![Управляемые экземпляры поменялись ролями после сбоя](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Перейдите к новому _вторичному_ управляемому экземпляру и выберите **Failover** еще раз, чтобы не вернуть основной экземпляр к основной роли. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Тест неудача с помощью PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Восстановите группу отработки отказа на основной сервер:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

Эта часть учебника использует следующие PowerShell cmdlets:

| Get-Help | Примечания |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Возвращает или перечисляет группы отработки отказа управляемого экземпляра.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Выполняет отработку отказа для группы отработки отказа управляемого экземпляра. | 

---



## <a name="clean-up-resources"></a>Очистка ресурсов
Очистка ресурсов путем сначала удаляния управляемого экземпляра, затем виртуального кластера, затем любых оставшихся ресурсов и, наконец, группы ресурсов. 

# <a name="portal"></a>[Портал](#tab/azure-portal)
1. На [портале Azure](https://portal.azure.com) перейдите к используемой группе ресурсов. 
1. Выберите один или несколько управляемых экземпляров и щелкните **Удалить**. Введите `yes` в текстовое поле, чтобы подтвердить удаление ресурса, а затем щелкните **Удалить**. Этот процесс может занять некоторое время в фоновом режиме, и пока это не будет сделано, вы не сможете удалить *виртуальный кластер* или любые другие зависимые ресурсы. Вы можете проверить состояние процесса удаления на вкладке "Действие", чтобы убедиться в удалении управляемого экземпляра. 
1. После удаления управляемого экземпляра удалите *виртуальный кластер*, выбрав его в группе ресурсов и щелкнув **Удалить**. Введите `yes` в текстовое поле, чтобы подтвердить удаление ресурса, а затем щелкните **Удалить**. 
1. Удалите все остальные ресурсы. Введите `yes` в текстовое поле, чтобы подтвердить удаление ресурса, а затем щелкните **Удалить**. 
1. Удалите группу ресурсов, выбрав **Удалить группу ресурсов**, введя в текстовом поле имя группы ресурсов `myResourceGroup` и щелкнув **Удалить**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Группу ресурсов необходимо будет удалить дважды. При первом удалении группы ресурсов будет удален управляемый экземпляр и виртуальные кластеры, но после этого будет выдаваться сообщение об ошибке `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`. Выполните команду Remove-AzResourceGroup еще раз, чтобы удалить все остаточные ресурсы, а также группу ресурсов.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

Эта часть учебника использует следующие PowerShell cmdlet:

| Get-Help | Примечания |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. |

---

## <a name="full-script"></a>Полный сценарий

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

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
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Создает управляемый экземпляр Базы данных SQL Azure.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Возвращает сведения об Управляемом экземпляре Базы данных SQL Azure |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Создает IP-конфигурацию для шлюза виртуальной сети. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Создание шлюза виртуальной сети |
| [New-AzVirtualNetworkGatewayConnection;](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Создает подключение между двумя шлюзами виртуальной сети.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Создает новую группу отработки отказа управляемого экземпляра Базы данных SQL Azure.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Возвращает или перечисляет группы отработки отказа управляемого экземпляра.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Выполняет отработку отказа для группы отработки отказа управляемого экземпляра. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. | 

# <a name="portal"></a>[Портал](#tab/azure-portal) 

Для портала Azure нет скриптов.

---

## <a name="next-steps"></a>Дальнейшие действия

В этом уроке вы настроили группу failover между двумя управляемыми экземплярами. Вы ознакомились с выполнением следующих задач:

> [!div class="checklist"]
> - Создание первичного управляемого экземпляра
> - Создайте вторичный управляемый экземпляр как часть [группы failover.](sql-database-auto-failover-group.md) 
> - Тестовая отработка отказа

Перейти к следующему быстрому запуску о том, как подключиться к управляемому экземпляру и как восстановить базу данных в управляемом экземпляре: 

> [!div class="nextstepaction"]
> [Подключение к управляемому экземпляру](sql-database-managed-instance-configure-vm.md)
> [Восстановление базы данных в управляемый экземпляр](sql-database-managed-instance-get-started-restore.md)


