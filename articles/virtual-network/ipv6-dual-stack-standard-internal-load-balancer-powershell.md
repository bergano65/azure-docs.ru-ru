---
title: Развертывание приложения с двойным стеком IPv6 с помощью стандартного баланса внутренней нагрузки в Azure - PowerShell
titlesuffix: Azure Virtual Network
description: В этой статье показано, как развернуть приложение с двойным стеком IPv6 со стандартным балансом внутренней нагрузки в виртуальной сети Azure с помощью Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: fdf726fd31e8b92a04a1c136eb5cd7110e0c6d5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72333369"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>Развертывание приложения с двойным стеком IPv6 с помощью стандартного баланса внутренней нагрузки в Azure - PowerShell (Предварительный просмотр)

В этой статье показано, как развернуть двойной стек (IPv4 - IPv6) приложение в Azure, которое включает в себя двойной стек виртуальной сети и подсети, Стандартный внутренний баланс нагрузки с двойной (IPv4 iPv6) передние конфигурации, VMs с NICs, которые имеют двойной IP конфигурации, группы сетевой безопасности и общедоступных ИП.

> [!Important]
> Поддержка IPv6 для виртуальной сети Azure в настоящее время находится в открытом доступе. Предварительная версия предоставляется без соглашения об уровне обслуживания. Не рекомендуем использовать ее в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Процедура создания IPv6-capable Internal Load Balancer почти идентична процессу создания интернет-баланса нагрузки IPv6, описанного [здесь.](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) Единственные отличия для создания внутреннего баланса нагрузки находятся в передней конфигурации, как показано на примере PowerShell ниже:

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "ace:cab:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

Изменения, которые делают выше внутренней нагрузки балансоровой передней конфигурации являются:
- Указан `PrivateIpAddressVersion` как "IPv6"
- Аргумент `-PublicIpAddress` был либо опущен или заменен `-PrivateIpAddress`. Обратите внимание, что частный адрес должен находиться в диапазоне IP-пространства Subnet, в котором будет развернут внутренний балансер нагрузки. Если статическая часть `-PrivateIpAddress` опущена, следующий бесплатный адрес IPv6 будет выбран из подсети, в которой развернут балансер внутренней нагрузки.
- Поднет двойного стека, в котором будет развернут баланселизатор `-Subnet` `-SubnetId` внутренней нагрузки, указан либо аргументом.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решите установить и использовать PowerShell локально, эта статья требует версии модуля Azure PowerShell 6.9.0 или позже. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="prerequisites"></a>Предварительные требования
Перед развертыванием приложения с двойным стеком в Azure необходимо настроить подписку на эту функцию предварительного просмотра с помощью следующего приложения Azure PowerShell:

Зарегистрируйтесь следующим образом:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Регистрация функции занимает до 30 минут. Вы можете проверить свой регистрационный статус, запустив следующую команду Azure PowerShell: Проверьте регистрацию следующим образом:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
После регистрации выполните следующую команду:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Прежде чем создать виртуальную сеть с двойным стеком, необходимо создать группу ресурсов с [Помощью New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Следующий пример создает группу ресурсов, названную *dsStd_ILB_RG* в восточном расположении *нас:*

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Создание общедоступных IP-адресов IPv4 и IPv6
Чтобы получить доступ к виртуальным машинам из Интернета, вам нужно IPv4 и IPv6 общедоступные IP-адреса для виртуальных технологий. Создание общедоступных IP-адресов с [помощью New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Следующий пример создает публичный IP-адрес IPv4 и IPv6 под названием *RdpPublicIP_1* и *RdpPublicIP_2* в *группе ресурсов dsStd_ILB_RG:*

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>Создание виртуальной сети и подсети

Создайте виртуальную сеть с использованием [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) с двойной конфигурацией подсети с помощью [New-AzVirtualNetworkSubnetConfig.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) Следующий пример создает виртуальную сеть под названием *dsVnet* с *dsSubnet*.

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>Создание Load Balancer уровня "Стандартный"

В этом разделе вы настраиваете двойной ip-адрес (IPv4 и IPv6) и пул адресов бэк-энда для балансопарка нагрузки, а затем создаете балансер стандартной нагрузки.

### <a name="create-front-end-ip"></a>Создание интерфейсного IP-адреса

Создайте интерфейсный IP-адрес с помощью командлета [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Следующий пример создает конфигурации IP-адресов IPv4 и IPv6 под названием *dsLbFrontEnd_v4* и *dsLbFrontEnd_v6:*

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "ace:cab:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>Настройка серверного пула адресов

Создайте пул адресов бэк-энда с [помощью New-AzLoadBalancerBackendAddressPoolConfig.](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) Виртуальные машины присоединяются в этот серверный пул на следующих этапах. Следующий пример создает пулы адресов бэк-энда, названные *dsLbBackEndPool_v4* и *dsLbBackEndPool_v6* включать в себя виртуальные вымсвязи с конфигурациями IPV4 и IPv6 NIC:

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Создание правила балансировщика нагрузки

Правило балансировщика нагрузки позволяет определить распределение трафика между виртуальными машинами. Вы определяете конфигурацию внешнего IP-адреса для входящего трафика и пул внутренних IP-адресов для приема трафика, а также требуемый порт источника и назначения. Чтобы убедиться, что только здоровые ВМ получают трафик, можно дополнительно определить зонд работоспособности. Базовый балансоровостой нагрузки использует зонд IPv4 для оценки работоспособности как для конечных точек IPv4, так и для IPv6 на VMs. Стандартный балансоровостом нагрузки включает в себя поддержку явно зондов здоровья IPv6.

Создайте правило подсистемы балансировки нагрузки с помощью командлета [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Следующий пример создает правила балансоподъемности *нагрузки,* названные dsLBrule_v4 и *dsLBrule_v6* и уравновешивает трафик на *tCP* port *80* с конфигурациями IP-класса IPv4 и IPv6:

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Создание подсистемы балансировки нагрузки

Создайте балансировку стандартной нагрузки с [помощью New-AzLoadBalancer.](/powershell/module/az.network/new-azloadbalancer) Следующий пример создает общедоступный балансер стандартной нагрузки под названием *myInternalLoadBalancer* с использованием конфигураций IP-адресов IPv4 и IPv6, бэкэнд-пулов и правил балансировки нагрузки, созданных на предыдущих этапах:

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Создание сетевых ресурсов
Перед развертыванием некоторых виртуальных технологий и проверки баланса необходимо создать вспомогательные сетевые ресурсы - набор доступности, группу сетевой безопасности и виртуальные NICs. 

### <a name="create-an-availability-set"></a>"Создать группу доступности"
Чтобы повысить доступность приложения, поместите в набор доступных вашего числа вневых внештатных данных.

Создайте группу доступности с помощью командлета [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). Следующий пример создает набор доступности под названием *dsAVset:*

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Создание группы безопасности сети

Создайте группу сетевой безопасности для правил, которые будут регулировать входящие и исходящие коммуникации в vNet.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Создание правила группы безопасности сети для порта 3389

Создайте правило для группы безопасности сети, разрешающее RDP-подключения через порт 3389, с помощью командлета [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Создание правила группы безопасности сети для порта 80

Создайте правило группы сетевой безопасности, позволяющее подключение к Интернету через порт 80 с [помощью New-AzNetworkSecurityRuleConfig.](/powershell/module/az.network/new-aznetworksecurityruleconfig)

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Создайте группу безопасности сети с помощью командлета [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>Создание сетевых адаптеров

Создавайте виртуальные NICs с [помощью New-AzNetworkInterface.](/powershell/module/az.network/new-aznetworkinterface) Следующий пример создает два виртуальных NICs как с конфигурациями IPv4 и IPv6. (по одной виртуальной сетевой карте для каждой виртуальной машины, используемой приложением).

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Укажите имя и пароль администратора для виртуальной машины с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

Теперь вы можете создать виртуальные машины с помощью командлета [New-AzVM](/powershell/module/az.compute/new-azvm). В приведенном ниже примере создаются две виртуальные машины и обязательные компоненты виртуальной сети, если их еще нет:

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Просмотр виртуальной сети IPv6 с двойным стеком на портале Azure
Виртуальную сеть IPv6 с двойным стеком можно просмотреть следующим образом:
1. В панели поиска портала введите *dsVnet*.
2. Когда **dsVnet** появляется в результатах поиска, выберите его. Это запускает **Обзор** страницы двойного стека виртуальной сети под названием *dsVnet*. Виртуальная сеть двойного стека показывает два NICs с конфигурациями IPv4 и IPv6, расположенными в подсетевом сдвойном стеке под названием *dsSubnet.*

![IPv6 Двойной стек Виртуальная сеть со стандартной внутренней нагрузки балансер](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> Виртуальная сеть IPv6 для Azure доступна на портале Azure только для этого предварительного просмотра.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы создали standard Load Balancer с двойной конфигурацией IP-адреса (IPv4 и IPv6). Вы также создали два виртуальных машины, которые включали NICs с двойными конфигурациями IP (IPV4 и IPv6), которые были добавлены в пул задней части баланса нагрузки. Подробнее о поддержке IPv6 в виртуальных сетях Azure читайте в пример [IPv6 для виртуальной сети Azure?](ipv6-overview.md)