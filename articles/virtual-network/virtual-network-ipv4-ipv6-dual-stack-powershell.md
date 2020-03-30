---
title: Развертывание iPv6 двойное приложение стек - Базовая нагрузка балансер - PowerShell
titlesuffix: Azure Virtual Network
description: В этой статье показано, как развертывать приложение с двойным стеком IPv6 в виртуальной сети Azure с помощью Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/17/2019
ms.author: kumud
ms.openlocfilehash: 003d677dcdead5792f932ecfe6350df63184cee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75368341"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---powershell-preview"></a>Развертывание приложения с двойным стеком IPv6 с помощью базового баланса нагрузки - PowerShell (Предварительный просмотр)

В этой статье показано, как развернуть двойной стек (IPv4 - IPv6) приложение с Basic Load Balancer с помощью Azure PowerShell, который включает в себя двойной стек виртуальной сети и подсети, балансер базовой нагрузки с двойной (IPv4 iPv6) передние конфигурации, VMs с NICs, которые имеют двойную конфигурацию IP, группу сетевой безопасности и общедоступные IP-адреса.

Чтобы развернуть двойное приложение стек (IPV4 - IPv6) с помощью Standard Load Balancer, [см. Развертывание двойного стека IPv6 с помощью стандартного балансера нагрузки с помощью Azure PowerShell.](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)

> [!Important]
> Поддержка IPv6 для виртуальной сети Azure в настоящее время находится в открытом доступе. Предварительная версия предоставляется без соглашения об уровне обслуживания. Не рекомендуем использовать ее в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

Прежде чем создать виртуальную сеть с двойным стеком, необходимо создать группу ресурсов с [Помощью New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Следующий пример создает группу ресурсов под названием *myRGDualStack* *на востоке нас* местоположение:

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Создание общедоступных IP-адресов IPv4 и IPv6
Чтобы получить доступ к вашим виртуальным машинам из Интернета, вам нужно IPv4 и IPv6 общедоступные IP-адреса для балансиватель нагрузки. Создание общедоступных IP-адресов с [помощью New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Следующий пример создает ip-адрес IPv4 и IPv6 под названием *dsPublicIP_v4* и *dsPublicIP_v6* в группе ресурсов *dsRG1:*

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
Чтобы получить доступ к вашим виртуальным машинам с помощью RDP-соединения, создайте общедоступные IP-адреса IPV4 для виртуальных машин с [помощью New-AzPublicIpAddress.](/powershell/module/az.network/new-azpublicipaddress)

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>Создание подсистемы балансировки нагрузки уровня "Базовый"

В этом разделе вы настраиваете двойной IP-адрес (IPv4 и IPv6) и пул адресов бэк-энда для балансопарка нагрузки, а затем создаете балансер базовой нагрузки.

### <a name="create-front-end-ip"></a>Создание интерфейсного IP-адреса

Создайте интерфейсный IP-адрес с помощью командлета [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Следующий пример создает конфигурации IP-адресов IPv4 и IPv6 под названием *dsLbFrontEnd_v4* и *dsLbFrontEnd_v6:*

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Настройка серверного пула адресов

Создайте пул адресов бэк-энда с [помощью New-AzLoadBalancerBackendAddressPoolConfig.](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) Виртуальные машины присоединяются в этот серверный пул на следующих этапах. Следующий пример создает пулы адресов бэк-энда, названные *dsLbBackEndPool_v4* и *dsLbBackEndPool_v6* включать в себя виртуальные вымсвязи с конфигурациями IPV4 и IPv6 NIC:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```
### <a name="create-a-health-probe"></a>Создание пробы работоспособности
Используйте [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) для создания зонда работоспособности для мониторинга работоспособности ВМ.
```azurepowershell
$probe = New-AzLoadBalancerProbeConfig -Name MyProbe -Protocol tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
```
### <a name="create-a-load-balancer-rule"></a>Создание правила балансировщика нагрузки

Правило балансировщика нагрузки позволяет определить распределение трафика между виртуальными машинами. Вы определяете конфигурацию внешнего IP-адреса для входящего трафика и пул внутренних IP-адресов для приема трафика, а также требуемый порт источника и назначения. Чтобы убедиться, что только здоровые ВМ получают трафик, можно дополнительно определить зонд работоспособности. Базовый балансоровостой нагрузки использует зонд IPv4 для оценки работоспособности как для конечных точек IPv4, так и для IPv6 на VMs. Стандартный балансоровостом нагрузки включает в себя поддержку явно зондов здоровья IPv6.

Создайте правило подсистемы балансировки нагрузки с помощью командлета [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Следующий пример создает правила балансоподъемности *нагрузки,* названные dsLBrule_v4 и *dsLBrule_v6* и уравновешивает трафик на *tCP* port *80* с конфигурациями IP-класса IPv4 и IPv6:

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe
```

### <a name="create-load-balancer"></a>Создание подсистемы балансировки нагрузки

Теперь создайте Load Balancer ценовой категории "Базовый" с помощью командлета [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Следующий пример создает общедоступный балансер basic Load balancer под названием *myLoadBalancer* с использованием конфигураций IP-адресов IPv4 и IPv6, резервных пулов и правил балансировки нагрузки, созданных на предыдущих этапах:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Создание сетевых ресурсов
Перед развертыванием некоторых виртуальных технологий и проверки балансового средства необходимо создать вспомогательные сетевые ресурсы - набор доступности, группу сетевой безопасности, виртуальную сеть и виртуальные NICs. 
### <a name="create-an-availability-set"></a>"Создать группу доступности"
Чтобы улучшить высокую доступность приложения, поместите виртуальные машины в группу доступности.

Создайте группу доступности с помощью командлета [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). В следующем примере создается группа доступности *myAvailabilitySet*.

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Создание группы безопасности сети

Создайте группу сетевой безопасности для правил, которые будут регулировать входящие и исходящие коммуникации в вашем VNET.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Создание правила группы безопасности сети для порта 3389

Создайте правило для группы безопасности сети, разрешающее RDP-подключения через порт 3389, с помощью командлета [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
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

```azurepowershell-interactive
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

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Создание виртуальной сети

Создайте виртуальную сеть с помощью командлета [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). В следующем примере создаются виртуальная сеть *myVnet* и подсеть *mySubnet*.

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>Создание сетевых адаптеров

Создавайте виртуальные NICs с [помощью New-AzNetworkInterface.](/powershell/module/az.network/new-aznetworkinterface) Следующий пример создает два виртуальных NICs как с конфигурациями IPv4 и IPv6. (по одной виртуальной сетевой карте для каждой виртуальной машины, используемой приложением).

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
    
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Укажите имя и пароль администратора для виртуальной машины с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

Теперь вы можете создать виртуальные машины с помощью командлета [New-AzVM](/powershell/module/az.compute/new-azvm). В приведенном ниже примере создаются две виртуальные машины и обязательные компоненты виртуальной сети, если их еще нет: 

```azurepowershell-interactive
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

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Определение IP-адресов конечных точек IPv4 и IPv6
Получите все объекты сетевого интерфейса в группе ресурсов, чтобы обобщить IP, используемый в этом развертывании с `get-AzNetworkInterface`помощью. Кроме того, получить фронтенд адреса груза балансера iPv4 и `get-AzpublicIpAddress`IPv6 конечных точек с .

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
На следующем рисунке показан апробный вывод, в который перечислены частные адреса IPv4 и IPv6 двух ВМ, а также IP-адреса Ip-адресов Грузопромбанка IPv4 и IPv6.

![Резюме IP двойного стека (IPv4/IPv6) развертывания приложений в Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Просмотр виртуальной сети IPv6 с двойным стеком на портале Azure
Виртуальную сеть IPv6 с двойным стеком можно просмотреть следующим образом:
1. В панели поиска портала введите *dsVnet*.
2. Когда в результатах поиска появится пункт **myVirtualNetwork**, выберите его. Это запускает **Обзор** страницы двойного стека виртуальной сети под названием *dsVnet*. Виртуальная сеть двойного стека показывает два NICs с конфигурациями IPv4 и IPv6, расположенными в подсетевом сдвойном стеке под названием *dsSubnet.*

  ![Виртуальная сеть IPv6 с двойным стеком в Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Виртуальная сеть IPv6 для Azure доступна на портале Azure только для этого предварительного просмотра.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы создали балансер Basic Load balancer с двойной конфигурацией IP-адреса (IPv4 и IPv6). Вы также создали два виртуальных машины, которые включали NICs с двойными конфигурациями IP (IPV4 и IPv6), которые были добавлены в пул задней части баланса нагрузки. Подробнее о поддержке IPv6 в виртуальных сетях Azure читайте в пример [IPv6 для виртуальной сети Azure?](ipv6-overview.md)
