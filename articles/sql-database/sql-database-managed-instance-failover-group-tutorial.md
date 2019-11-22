---
title: Учебник. Добавление управляемого экземпляра в группу отработки отказа
description: Узнайте, как настроить группу отработки отказа для управляемого экземпляра базы данных SQL Azure.
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
ms.openlocfilehash: 939606412c55ddad29801776c2385b406dc93a33
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286762"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Учебник. Добавление управляемого экземпляра базы данных SQL в группу отработки отказа

Добавьте управляемый экземпляр базы данных SQL в группу отработки отказа. В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> - Создание основного управляемого экземпляра
> - Создайте вторичный управляемый экземпляр в составе [группы отработки отказа](sql-database-auto-failover-group.md). 
> - Тестовая отработка отказа

  > [!NOTE]
  > - При работе с этим руководством убедитесь, что вы настраиваете ресурсы с [предварительными требованиями для настройки групп отработки отказа для управляемого экземпляра](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Создание управляемого экземпляра может занять значительное время. В результате для выполнения этого руководства может потребоваться несколько часов. Дополнительные сведения о времени подготовки см. в разделе [операции управления управляемыми экземплярами](sql-database-managed-instance.md#managed-instance-management-operations). 


## <a name="prerequisites"></a>предварительным требованиям

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)
В рамках этого руководства вам потребуются: 

- Подписка Azure. [Создайте бесплатную учетную запись](https://azure.microsoft.com/free/) , если она еще не создана.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Чтобы завершить работу с этим руководством, убедитесь, что у вас есть следующие элементы:

- Подписка Azure. [Создайте бесплатную учетную запись](https://azure.microsoft.com/free/) , если она еще не создана.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1\. Создание группы ресурсов и основного управляемого экземпляра
На этом шаге вы создадите группу ресурсов и основной управляемый экземпляр для группы отработки отказа с помощью портал Azure или PowerShell. 


# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal) 

Создайте группу ресурсов и основной управляемый экземпляр с помощью портал Azure. 

1. На портале Azure в меню слева выберите **Azure SQL**. Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Щелкните **+ Добавить**, чтобы открыть страницу **Выбор варианта развертывания SQL**. Чтобы просмотреть дополнительные сведения о различных базах данных, выберите Показать сведения в плитке базы данных.
1. Выберите **создать** на плитке **управляемые экземпляры SQL** . 

    ![Выбор управляемого экземпляра](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. На странице " **создание управляемый экземпляр базы данных SQL Azure** " на вкладке " **основы** "
    1. В разделе **сведения о проекте**выберите **подписку** из раскрывающегося списка, а затем выберите **создать новую** группу ресурсов. Введите имя группы ресурсов, например `myResourceGroup`. 
    1. В разделе **управляемый экземпляр сведения**укажите имя управляемого экземпляра и регион, в котором вы хотите развернуть управляемый экземпляр. Оставьте значения по умолчанию для **вычислений + Storage** . 
    1. В разделе **учетная запись администратора**укажите имя входа администратора, например `azureuser`и сложный пароль администратора. 

    ![Создать основной MI](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Оставьте остальные параметры со значениями по умолчанию и выберите проверить и **создать** , чтобы проверить параметры управляемого экземпляра. 
1. Выберите **создать** , чтобы создать основной управляемый экземпляр. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

В этой части руководства используются следующие командлеты PowerShell:

| Команда | Примечания |
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

## <a name="2---create-secondary-virtual-network"></a>2\. Создание вторичной виртуальной сети
Если вы используете портал Azure для создания управляемого экземпляра, необходимо отдельно создать виртуальную сеть, так как в подсети первичного и вторичного управляемых экземпляров нет перекрывающихся диапазонов. Если вы используете PowerShell для настройки управляемого экземпляра, перейдите к шагу 3. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal) 
Чтобы проверить диапазон подсети основной виртуальной сети, выполните следующие действия.
1. В [портал Azure](https://portal.azure.com)перейдите к группе ресурсов и выберите виртуальную сеть для основного экземпляра. 
1. Выберите **подсети** в разделе **Параметры** и запишите **диапазон адресов**. Диапазон адресов подсети виртуальной сети для вторичного управляемого экземпляра не может перекрывать эту область. 


   ![Основная подсеть](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Чтобы создать виртуальную сеть, выполните следующие действия.

1. В [портал Azure](https://portal.azure.com)выберите **создать ресурс** и выполните поиск по запросу *Виртуальная сеть*. 
1. Выберите параметр **виртуальной сети** , опубликованный корпорацией Майкрософт, а затем нажмите кнопку **создать** на следующей странице. 
1. Заполните обязательные поля, чтобы настроить виртуальную сеть для вторичного управляемого экземпляра, а затем нажмите кнопку **создать**. 

   В следующей таблице приведены значения, необходимые для вторичной виртуальной сети.

    | **Поле** | Значение |
    | --- | --- |
    | **имя** |  Имя виртуальной сети, используемой дополнительным управляемым экземпляром, например `vnet-sql-mi-secondary`. |
    | **Пространство адресов** | Адресное пространство виртуальной сети, например `10.128.0.0/16`. | 
    | **Подписка** | Подписка, в которой находятся основные управляемые экземпляры и группы ресурсов. |
    | **Регион** | Расположение, в которое будет развертываться вторичный управляемый экземпляр. |
    | **Подсеть** | Имя вашей подсети. `default` предоставляется по умолчанию. |
    | **Диапазон адресов**| Диапазон адресов для вашей подсети. Это значение должно отличаться от диапазона адресов подсети, используемого виртуальной сетью основного управляемого экземпляра, например `10.128.0.0/24`.  |
    | &nbsp; | &nbsp; |

    ![Значения вторичной виртуальной сети](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Этот шаг необходим только в том случае, если вы используете портал Azure для развертывания управляемого экземпляра. Если вы используете PowerShell, перейдите к шагу 3. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3\. Создание вторичного управляемого экземпляра
На этом шаге вы создадите вторичный управляемый экземпляр на портал Azure, который также будет настраивать сетевые подключения между двумя управляемыми экземплярами. 

Второй управляемый экземпляр должен:
- Быть пустым. 
- Имеют разные подсети и диапазоны IP-адресов, чем у основного управляемого экземпляра. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal) 

Создайте вторичный управляемый экземпляр с помощью портал Azure. 

1. На портале Azure в меню слева выберите **Azure SQL**. Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Щелкните **+ Добавить**, чтобы открыть страницу **Выбор варианта развертывания SQL**. Чтобы просмотреть дополнительные сведения о различных базах данных, выберите Показать сведения в плитке базы данных.
1. Выберите **создать** на плитке **управляемые экземпляры SQL** . 

    ![Выбор управляемого экземпляра](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. На вкладке **Основные сведения** на странице **Создание управляемый экземпляр базы данных SQL Azure** введите необходимые поля для настройки вторичного управляемого экземпляра. 

   В следующей таблице приведены значения, необходимые для вторичного управляемого экземпляра.
 
    | **Поле** | Значение |
    | --- | --- |
    | **Подписка** |  Подписка, в которой находится основной управляемый экземпляр. |
    | **Группа ресурсов**| Группа ресурсов, в которой находится основной управляемый экземпляр. |
    | **Имя управляемого экземпляра** | Имя нового вторичного управляемого экземпляра, например `sql-mi-secondary`  | 
    | **Регион**| Расположение для вторичного управляемого экземпляра.  |
    | **Имя для входа администратора управляемого экземпляра** | Имя входа, которое будет использоваться для нового вторичного управляемого экземпляра, например `azureuser`. |
    | **Пароль** | Сложный пароль, который будет использоваться именем входа администратора для нового вторичного управляемого экземпляра.  |
    | &nbsp; | &nbsp; |

1. На вкладке **Сетевые подключения** для **виртуальной сети**выберите виртуальную сеть, созданную для дополнительного управляемого экземпляра, из раскрывающегося списка.

   ![Сеть дополнительных MI](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. На вкладке **Дополнительные параметры** для параметра **Георепликация**выберите **Да** для _использования в качестве вторичной отработки отказа_. Выберите основной управляемый экземпляр из раскрывающегося списка. 
    1. Убедитесь, что параметры сортировки и часовой пояс соответствуют основному управляемому экземпляру. В основном управляемом экземпляре, созданном в этом руководстве, использовалось значение по умолчанию параметров сортировки `SQL_Latin1_General_CP1_CI_AS` и `(UTC) Coordinated Universal Time` часового пояса. 

   ![Сеть дополнительных MI](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Выберите **проверить и создать** , чтобы проверить параметры для вторичного управляемого экземпляра. 
1. Выберите **создать** , чтобы создать вторичный управляемый экземпляр. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

В этой части руководства используются следующие командлеты PowerShell:

| Команда | Примечания |
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

## <a name="4---create-primary-gateway"></a>4\. Создание основного шлюза 
Чтобы два управляемых экземпляра участвовали в группе отработки отказа, необходимо настроить шлюз между виртуальными сетями двух управляемых экземпляров, чтобы разрешить сетевую связь. Шлюз для основного управляемого экземпляра можно создать с помощью портал Azure. 


# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Создайте шлюз для виртуальной сети основного управляемого экземпляра с помощью портал Azure. 


1. В [портал Azure](https://portal.azure.com)перейдите к группе ресурсов и выберите ресурс **виртуальной сети** для основного управляемого экземпляра. 
1. Выберите **подсети** в разделе **Параметры** , а затем выберите, чтобы добавить новую **подсеть шлюза**. Оставьте значения по умолчанию. 

   ![Добавление шлюза для основного управляемого экземпляра](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. После создания шлюза подсети выберите **создать ресурс** в левой области навигации, а затем введите `Virtual network gateway` в поле поиска. Выберите ресурс **шлюза виртуальной сети** , опубликованный **корпорацией Майкрософт**. 

   ![Создание нового шлюза виртуальной сети](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Заполните обязательные поля, чтобы настроить шлюз для основного управляемого экземпляра. 

   В следующей таблице приведены значения, необходимые для шлюза для основного управляемого экземпляра.
 
    | **Поле** | Значение |
    | --- | --- |
    | **Подписка** |  Подписка, в которой находится основной управляемый экземпляр. |
    | **имя** | Имя шлюза виртуальной сети, например `primary-mi-gateway`. | 
    | **Регион** | Регион, где находится ваш вторичный управляемый экземпляр. |
    | **Тип шлюза** | Выберите **VPN**. |
    | **Тип VPN** | Выбор **на основе маршрута** |
    | **SKU**| Оставьте значение по умолчанию `VpnGw1`. |
    | **Местоположение.**| Расположение, в котором находится основной управляемый экземпляр и первичная виртуальная сеть.   |
    | **Виртуальная сеть**| Выберите виртуальную сеть, созданную в разделе 2, например `vnet-sql-mi-primary`. |
    | **Общедоступный IP-адрес**| Выберите **Создать**. |
    | **Имя общедоступного IP-адреса**| Введите имя IP-адреса, например `primary-gateway-IP`. |
    | &nbsp; | &nbsp; |

1. Оставьте остальные значения по умолчанию, а затем выберите **проверить и создать** , чтобы проверить параметры шлюза виртуальной сети.

   ![Параметры основного шлюза](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Выберите **создать** , чтобы создать новый шлюз виртуальной сети. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Создайте шлюз для виртуальной сети основного управляемого экземпляра с помощью PowerShell. 

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

В этой части руководства используются следующие командлеты PowerShell:

| Команда | Примечания |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Получает виртуальную сеть в группе ресурсов. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Добавляет конфигурацию подсети в виртуальную сеть. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Обновляет виртуальную сеть.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Получает подсеть в виртуальной сети. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Создает IP-конфигурацию для шлюза виртуальной сети. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Создание шлюза виртуальной сети |


---


## <a name="5---create-secondary-gateway"></a>5\. Создание вторичного шлюза 
На этом шаге создайте шлюз для виртуальной сети вторичного управляемого экземпляра с помощью портал Azure 


# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

С помощью портал Azure повторите действия, описанные в предыдущем разделе, чтобы создать подсеть и шлюз виртуальной сети для вторичного управляемого экземпляра. Заполните обязательные поля, чтобы настроить шлюз для вторичного управляемого экземпляра. 

   В следующей таблице приведены значения, необходимые для шлюза для вторичного управляемого экземпляра.

   | **Поле** | Значение |
   | --- | --- |
   | **Подписка** |  Подписка, в которой находится экземпляр вторичного управляемого экземпляра. |
   | **имя** | Имя шлюза виртуальной сети, например `secondary-mi-gateway`. | 
   | **Регион** | Регион, где находится ваш вторичный управляемый экземпляр. |
   | **Тип шлюза** | Выберите **VPN**. |
   | **Тип VPN** | Выбор **на основе маршрута** |
   | **SKU**| Оставьте значение по умолчанию `VpnGw1`. |
   | **Местоположение.**| Расположение, где находится дополнительный управляемый экземпляр и вторичная виртуальная сеть.   |
   | **Виртуальная сеть**| Выберите виртуальную сеть, созданную в разделе 2, например `vnet-sql-mi-secondary`. |
   | **Общедоступный IP-адрес**| Выберите **Создать**. |
   | **Имя общедоступного IP-адреса**| Введите имя IP-адреса, например `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Параметры дополнительного шлюза](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

| Команда | Примечания |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Получает виртуальную сеть в группе ресурсов. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Добавляет конфигурацию подсети в виртуальную сеть. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Обновляет виртуальную сеть.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Получает подсеть в виртуальной сети. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Создает IP-конфигурацию для шлюза виртуальной сети. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Создание шлюза виртуальной сети |

---


## <a name="6---connect-the-gateways"></a>6\. Подключение шлюзов
На этом шаге создайте двунаправленное подключение между двумя шлюзами двух виртуальных сетей. 


# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Подключите два шлюза с помощью портал Azure. 


1. Выберите **создать ресурс** на [портал Azure](https://portal.azure.com).
1. Введите `connection` в поле поиска и нажмите клавишу ВВОД для поиска, который переводит вас на ресурс **подключения** , опубликованный корпорацией Майкрософт.
1. Выберите **создать** , чтобы создать подключение. 
1. На вкладке **Основные сведения** выберите следующие значения и нажмите кнопку **ОК**. 
    1. Выберите `VNet-to-VNet` для **типа соединения**. 
    1. Выберите подписку в раскрывающемся списке. 
    1. Выберите группу ресурсов для управляемого экземпляра в раскрывающемся списке. 
    1. Выберите расположение основного управляемого экземпляра из раскрывающегося списка. 
1. На вкладке **Параметры** выберите или введите следующие значения, а затем нажмите кнопку **ОК**.
    1. Выберите основной сетевой шлюз для **первого шлюза виртуальной сети**, например `Primary-Gateway`.  
    1. Выберите дополнительный сетевой шлюз для **второго шлюза виртуальной сети**, например `Secondary-Gateway`. 
    1. Установите флажок рядом с пунктом **установить двунаправленное подключение**. 
    1. Либо оставьте имя основного подключения по умолчанию, либо переименуйте его в нужное значение. 
    1. Укажите **общий ключ (PSK)** для подключения, например `mi1m2psk`. 

   ![Создание подключения к шлюзу](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. На вкладке **Сводка** проверьте параметры двунаправленного подключения и нажмите кнопку **ОК** , чтобы создать подключение. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

| Команда | Примечания |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Создает подключение между двумя шлюзами виртуальной сети.   |

---


## <a name="7---create-a-failover-group"></a>7\. Создание группы отработки отказа
На этом шаге вы создадите группу отработки отказа и добавите в нее оба управляемых экземпляра. 


# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)
Создайте группу отработки отказа с помощью портал Azure. 


1. На **портале Azure** в меню слева выберите [Azure SQL](https://portal.azure.com). Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите Azure SQL в поле поиска. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Выберите основной управляемый экземпляр, созданный в первом разделе, например `sql-mi-primary`. 
1. В разделе **Параметры**перейдите к **экземпляру группы отработки отказа** и выберите **Добавить группу** , чтобы открыть страницу **группы отработки отказа экземпляра** . 

   ![Добавление группы отработки отказа](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. На странице **Группа отработки отказа** введите имя группы отработки отказа, например `failovergrouptutorial`, а затем в раскрывающемся списке выберите дополнительный управляемый экземпляр, например `sql-mi-secondary`. Выберите **создать** , чтобы создать группу отработки отказа. 

   ![Создание группы отработки отказа](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. После завершения развертывания группы отработки отказа будет выполнен переход на страницу **группы отработки отказа** . 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
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

| Команда | Примечания |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Создает новую группу отработки отказа управляемого экземпляра Базы данных SQL Azure.  |


---


## <a name="8---test-failover"></a>8\. тестовая отработка отказа
На этом шаге группа отработки отказа будет передаваться на сервер-получатель, а затем восстановлена с помощью портал Azure. 


# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)
Тестовая отработка отказа с помощью портал Azure. 


1. Перейдите к управляемому экземпляру в [портал Azure](https://portal.azure.com) и выберите **группы отработки отказа экземпляра** в разделе Параметры. 
1. Проверьте, какой управляемый экземпляр является первичным и какой управляемый экземпляр является вторичным. 
1. Выберите **отработка отказа** и нажмите **кнопку Да** в предупреждении о том, что сеансы TDS отключены. 

   ![Переключение группы отработки отказа](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Проверьте, какой экземпляр управляемого экземпляра является основным, а какой — дополнительным. Если отработка отказа завершилась успешно, два экземпляра должны иметь переключаемые роли. 

   ![Управляемые экземпляры с переключением ролей после отработки отказа](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Снова выберите **отработку отказа** , чтобы восстановить первичный экземпляр в первичную роль. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Тестовая отработка отказа с помощью PowerShell. 

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

В этой части руководства используются следующие командлеты PowerShell:

| Команда | Примечания |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Возвращает или перечисляет группы отработки отказа управляемого экземпляра.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Выполняет отработку отказа для группы отработки отказа управляемого экземпляра. | 

---



## <a name="clean-up-resources"></a>Очистка ресурсов
Очистите ресурсы, сначала удалив управляемый экземпляр, затем виртуальный кластер, остальные ресурсы и, наконец, группу ресурсов. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)
1. Перейдите к группе ресурсов в [портал Azure](https://portal.azure.com). 
1. Выберите управляемые экземпляры, а затем щелкните **Удалить**. В текстовом поле введите `yes`, чтобы подтвердить удаление ресурса, а затем выберите **Удалить**. Этот процесс может занять некоторое время в фоновом режиме, и пока он не будет завершен, вы не сможете удалить *виртуальный кластер* или другие зависимые ресурсы. Проследите за удалением на вкладке действие, чтобы подтвердить удаление управляемого экземпляра. 
1. После удаления управляемого экземпляра удалите *виртуальный кластер* , выбрав его в группе ресурсов, а затем нажмите кнопку **Удалить**. В текстовом поле введите `yes`, чтобы подтвердить удаление ресурса, а затем выберите **Удалить**. 
1. Удалите все оставшиеся ресурсы. В текстовом поле введите `yes`, чтобы подтвердить удаление ресурса, а затем выберите **Удалить**. 
1. Удалите группу ресурсов, выбрав **Удалить группу ресурсов**, введя имя группы ресурсов `myResourceGroup`и выбрав **Удалить**. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Группу ресурсов необходимо будет удалить дважды. При первом удалении группы ресурсов будет удален управляемый экземпляр и виртуальные кластеры, но после этого будет выдаваться сообщение об ошибке `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`. Выполните команду Remove-AzResourceGroup еще раз, чтобы удалить все остаточные ресурсы, а также группу ресурсов.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

В этой части руководства используется следующий командлет PowerShell:

| Команда | Примечания |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. |

---

## <a name="full-script"></a>Полный сценарий

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
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
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Создает подключение между двумя шлюзами виртуальной сети.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Создает новую группу отработки отказа управляемого экземпляра Базы данных SQL Azure.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Возвращает или перечисляет группы отработки отказа управляемого экземпляра.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Выполняет отработку отказа для группы отработки отказа управляемого экземпляра. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов. | 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal) 

Для портал Azure нет доступных скриптов.

---

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы настроили группу отработки отказа между двумя управляемыми экземплярами. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> - Создание основного управляемого экземпляра
> - Создайте вторичный управляемый экземпляр в составе [группы отработки отказа](sql-database-auto-failover-group.md). 
> - Тестовая отработка отказа

Перейдите к следующему краткому руководству по подключению к управляемому экземпляру и восстановлению базы данных в управляемом экземпляре. 

> [!div class="nextstepaction"]
> [Подключение к управляемому экземпляру](sql-database-managed-instance-configure-vm.md)
> [Восстановление базы данных в управляемый экземпляр](sql-database-managed-instance-get-started-restore.md)


