---
title: Краткое руководство. Создание Load Balancer ценовой категории "Стандартный" с помощью Azure PowerShell
titleSuffix: Azure Load Balancer
description: Из этого краткого руководства вы узнаете, как с помощью Azure PowerShell создать Load Balancer уровня "Стандартный".
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: b387df5049fff2cb17e8d0758f1cf5fd8f0d0853
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049110"
---
# <a name="quickstart-create-a-standard-load-balancer-using-azure-powershell"></a>Краткое руководство. Создание Load Balancer ценовой категории "Стандартный" с помощью Azure PowerShell

Из этого краткого руководства вы узнаете, как с помощью Azure PowerShell создать Load Balancer уровня "Стандартный" Чтобы проверить работу подсистемы балансировки нагрузки, вы развернете три виртуальные машины Windows Server и распределите между ними нагрузку веб-приложения. См. дополнительные сведения о [Load Balancer уровня "Стандартный"](load-balancer-standard-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать PowerShell локально, для работы с этой статьей вам понадобится модуль Azure PowerShell 5.4.1 или более поздней версии. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Прежде чем создавать подсистему балансировки нагрузки, создайте группу ресурсов с помощью командлета [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). В следующем примере создается группа ресурсов с именем *myResourceGroupSLB* в расположении *EastUS*.

```azurepowershell
$rgName='MyResourceGroupSLB'
$location='eastus'
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса

Для доступа к приложению через Интернет требуется общедоступный IP-адрес для балансировщика нагрузки. Создайте общедоступный IP-адрес с помощью командлета [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). В следующем примере создается общедоступный IP-адрес с именем *myPublicIP* в группе ресурсов *myResourceGroupSLB*.

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard
```

## <a name="create-standard-load-balancer"></a>Создание Load Balancer уровня "Стандартный"

В рамках этого раздела вы настроите интерфейсный IP-адрес и серверный пул IP-адресов подсистемы балансировки нагрузки, а затем создадите Load Balancer (цен. категория "Стандартный").

### <a name="create-front-end-ip"></a>Создание интерфейсного IP-адреса

Создайте интерфейсный IP-адрес с помощью командлета [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). В следующем примере создается конфигурация с именем *myFrontEnd* для интерфейсного IP-адреса и к ней подключается адрес *myPublicIP*:

```azurepowershell
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Настройка серверного пула адресов

Создайте серверный пул адресов с помощью командлета [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Виртуальные машины присоединяются в этот серверный пул на следующих этапах. В следующем примере создается серверный пул адресов *myBackEndPool*.

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

### <a name="create-a-health-probe"></a>Создание пробы работоспособности
Чтобы балансировщик нагрузки мог следить за состоянием приложения, необходимо настроить пробу работоспособности. Проба работоспособности динамически добавляет или удаляет виртуальные машины из балансировщика нагрузки на основе их ответа на проверки работоспособности. По умолчанию виртуальная машина удаляется из числа машин, на которые балансировщик распределяет нагрузку, после двух последовательных сбоев с интервалом в 15 секунд. Пробу работоспособности можно создать на основе протокола или конкретной страницы проверки работоспособности приложения.

В следующем примере создается проба TCP. Вы также можете создать настраиваемую пробу HTTP для более детальных проверок. При использовании настраиваемой пробы HTTP нужно создать страницу проверки работоспособности, например *healthcheck.aspx*. Чтобы обеспечить работоспособность узла, проба должна возвращать ответ **HTTP 200 OK** для балансировщика нагрузки.

Чтобы создать пробу работоспособности TCP, выполните командлет [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). В следующем примере создается проба работоспособности *myHealthProbe*, которая отслеживает каждую виртуальную машину через *HTTP*-порт *80*:

```azurepowershell
$probe = New-AzLoadBalancerProbeConfig `
 -Name 'myHealthProbe' `
 -Protocol Http -Port 80 `
 -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

### <a name="create-a-load-balancer-rule"></a>Создание правила балансировщика нагрузки
Правило балансировщика нагрузки позволяет определить распределение трафика между виртуальными машинами. Вы определяете интерфейсную конфигурацию IP-адресов для входящего трафика и внутренний пул IP-адресов для приема трафика, а также требуемый порт источника и назначения. Чтобы обеспечить получение трафика только работоспособными виртуальными машинами, можно также определить используемую пробу работоспособности.

Создайте правило подсистемы балансировки нагрузки с помощью командлета [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). В следующем примере создается правило подсистемы балансировки нагрузки *myLoadBalancerRule* для балансировки трафика через *TCP*-порт *80*:

```azurepowershell
$rule = New-AzLoadBalancerRuleConfig `
  -Name 'myLoadBalancerRuleWeb' -Protocol Tcp `
  -Probe $probe -FrontendPort 80 -BackendPort 80 `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bePool
```

### <a name="create-the-nat-rules"></a>Создание правил NAT

Создайте правила NAT с помощью командлета [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig). В следующем примере создаются правила NAT с именами *myLoadBalancerRDP1* и *myLoadBalancerRDP2*, разрешающие RDP-подключения к внутренним серверам по портам 4221 и 4222:

```azurepowershell
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP1' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp -FrontendPort 4221 `
  -BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP2' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4222 `
  -BackendPort 3389

$natrule3 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP3' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4223 `
  -BackendPort 3389
```

### <a name="create-load-balancer"></a>Создание подсистемы балансировки нагрузки

Создайте Load Balancer (цен. категория "Стандартный") с помощью командлета [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). В приведенном ниже примере создается общедоступный экземпляр службы Load Balancer (цен. категория "Стандартный") с именем myLoadBalancer. Для него применяется конфигурация интерфейсных IP-адресов, серверный пул, проба работоспособности, правило балансировки нагрузки и правила NAT, созданные на предыдущих этапах.

```azurepowershell
$lb = New-AzLoadBalancer `
  -ResourceGroupName $rgName `
  -Name 'MyLoadBalancer' `
  -SKU Standard `
  -Location $location `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bepool `
  -Probe $probe `
  -LoadBalancingRule $rule `
  -InboundNatRule $natrule1,$natrule2,$natrule3
```

## <a name="create-network-resources"></a>Создание сетевых ресурсов
Прежде чем развертывать виртуальные машины и тестировать подсистему балансировки нагрузки, создайте вспомогательные ресурсы, то есть виртуальную сеть и виртуальные сетевые адаптеры. 

### <a name="create-a-virtual-network"></a>Создание виртуальной сети
Создайте виртуальную сеть с помощью командлета [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). В следующем примере создаются виртуальная сеть *myVnet* и подсеть *mySubnet*.

```azurepowershell
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupSLB" `
  -Location $location `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-addresses-for-the-vms"></a>Создание общедоступных IP-адресов для виртуальных машин

Для доступа к виртуальным машинам с помощью подключения к удаленному рабочему столу требуется, чтобы у каждой из них был общедоступный IP-адрес. Так как в этом сценарии используется Load Balancer (цен. категория "Стандартный"), необходимо создать общедоступные IP-адреса (цен. категория "Стандартный") для виртуальных машин, выполнив командлет [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static
 

$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static


$RdpPublicIP_3 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_3" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static

```
### <a name="create-network-security-group"></a>Создание группы безопасности сети
Создайте группу безопасности сети для определения входящих подключений к виртуальной сети.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Создание правила группы безопасности сети для порта 3389
Создайте правило для группы безопасности сети, разрешающее RDP-подключения через порт 3389, с помощью командлета [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell

$rule1 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleRDP' -Description 'Allow RDP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 1000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Создание правила группы безопасности сети для порта 80
Создайте правило для группы безопасности сети, разрешающее входящие подключения через порт 80, с помощью командлета [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleHTTP' -Description 'Allow HTTP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 2000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Создайте группу безопасности сети с помощью командлета [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RgName -Location $location `
-Name 'myNetworkSecurityGroup' -SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Создание сетевых адаптеров
Создайте виртуальные сетевые адаптеры и свяжите их с общедоступным IP-адресом и группами безопасности сети, созданными на предыдущих шагах, с помощью командлета [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). В следующем примере создаются три виртуальных сетевых адаптера (по одной виртуальной сетевой карте для каждой виртуальной машины, используемой приложением). Вы можете в любое время создать дополнительные виртуальные сетевые карты и виртуальные машины и добавить их в балансировщик нагрузки:

```azurepowershell
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic1' -PublicIpAddress $RdpPublicIP_1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule1 -Subnet $vnet.Subnets[0]

$nicVM2 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic2' -PublicIpAddress $RdpPublicIP_2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule2 -Subnet $vnet.Subnets[0]

$nicVM3 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic3' -PublicIpAddress $RdpPublicIP_3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule3 -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Укажите имя и пароль администратора для виртуальной машины с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = Get-Credential
```

Теперь вы можете создать виртуальные машины с помощью командлета [New-AzVM](/powershell/module/az.compute/new-azvm). В приведенном ниже примере создаются две виртуальные машины и обязательные компоненты виртуальной сети, если их еще нет: В этом примере сетевые адаптеры (*MyNic1*, *MyNic2* и *MyNic3*), созданные на предыдущем шаге, назначаются виртуальным машинам *myVM1*, *myVM2* и *VM3*. Кроме того, так как сетевые адаптеры связаны с внутренним пулом подсистемы балансировки нагрузки, виртуальные машины автоматически добавляются во внутренний пул.

```azurepowershell

# ############## VM1 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM1' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM1' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM1.Id

# Create a virtual machine
$vm1 = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig

# ############## VM2 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM2' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM2' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM2.Id

# Create a virtual machine
$vm2 = New-AzVM -ResourceGroupName $rgName -Zone 2 -Location $location -VM $vmConfig

# ############## VM3 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM3' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM3' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
| Add-AzVMNetworkInterface -Id $nicVM3.Id

# Create a virtual machine
$vm3 = New-AzVM -ResourceGroupName $rgName -Zone 3 -Location $location -VM $vmConfig
```

Создание и настройка трех виртуальных машин занимает несколько минут.

### <a name="install-iis-with-a-custom-web-page"></a>Установка IIS с пользовательской веб-страницей

Установите IIS с пользовательской веб-страницей на обеих виртуальных машинах серверной части. Для этого сделайте следующее:

1. Получите общедоступные IP-адреса трех виртуальных машин с помощью `Get-AzPublicIPAddress`.

   ```azurepowershell
     $vm1_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_1").IpAddress
     $vm2_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_2").IpAddress
     $vm3_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_3").IpAddress
    ```
2. Создайте подключения к удаленному рабочему столу *myVM1*, *myVM2* и *myVM3* с помощью общедоступных IP-адресов этих виртуальных машин следующим образом. 

   ```azurepowershell    
     mstsc /v:$vm1_rdp_ip
     mstsc /v:$vm2_rdp_ip
     mstsc /v:$vm3_rdp_ip
   
    ```

3. Введите учетные данные для каждой виртуальной машины, чтобы начать сеанс RDP.
4. На каждой виртуальной машине запустите Windows PowerShell и выполните в нем приведенные ниже команды, чтобы установить сервер IIS и изменить стандартный HTM-файл.

    ```azurepowershell
    # Install IIS
     Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from host " + $env:computername)
    ```

5. Закройте подключения RDP к *myVM1*, *myVM2* и *myVM3*.


## <a name="test-load-balancer"></a>Проверка балансировщика нагрузки
Получите общедоступный IP-адрес подсистемы балансировки нагрузки с помощью командлета [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). Следующий пример позволяет получить IP-адрес для созданного ранее *myPublicIP*.

```azurepowershell
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupSLB" `
  -Name "myPublicIP" | select IpAddress
```

После этого можно ввести общедоступный IP-адрес в веб-браузер. Отображается веб-сайт, а также имя узла виртуальной машины, на которую балансировщик нагрузки направил трафик, как показано в следующем примере:

![Проверка балансировщика нагрузки](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Чтобы увидеть, как балансировщик нагрузки распределяет трафик между тремя виртуальными машинами, на которых выполняется приложение, принудительно обновите веб-браузер. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroupSLB
```

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как создать подсистему балансировки нагрузки в службе Azure Load Balancer (цен. категории "Стандартный"), подключить к ней виртуальные машины, настроить правило трафика подсистемы балансировки нагрузки, зонд работоспособности, а также протестировать подсистему балансировки нагрузки. Чтобы узнать больше об Azure Load Balancer, ознакомьтесь с другими руководствами по этой службе.

> [!div class="nextstepaction"]
> [Руководства по Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
