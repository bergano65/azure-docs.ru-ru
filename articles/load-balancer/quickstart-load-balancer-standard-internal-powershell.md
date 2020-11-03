---
title: Краткое руководство. Создание внутренней подсистемы балансировки нагрузки — Azure PowerShell
titleSuffix: Azure Load Balancer
description: В этом кратком руководстве показано, как создать внутреннюю подсистему балансировки нагрузки с помощью Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 12190a50579bf5b87685fc4b19ec7b2907e5ee9c
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547050"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Краткое руководство. Создание внутренней подсистемы балансировки нагрузки с помощью Azure PowerShell для распределения нагрузки между виртуальными машинами

Начните работу с Azure Load Balancer, создав с помощью Azure PowerShell внутреннюю подсистему балансировки нагрузки и две виртуальные машины.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
- Локальная установка Azure PowerShell или Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать PowerShell локально, для работы с этой статьей вам понадобится модуль Azure PowerShell 5.4.1 или более поздней версии. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). При использовании PowerShell на локальном компьютере также нужно запустить `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью командлета [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

* с именем **CreateIntLBQS-rg** ;
* в расположении **eastus**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**SKU "Стандартный"**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Для производственных рабочих нагрузок рекомендуется использовать подсистему балансировки нагрузки ценовой категории "Стандартный". Дополнительные сведения о доступных ценовых категориях см. в статье **[Номера SKU для Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Настройка виртуальной сети

Прежде чем развертывать виртуальные машины и тестировать подсистему балансировки нагрузки, создайте вспомогательные ресурсы виртуальной сети.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Создание виртуальной сети и узла Бастиона Azure

Создайте виртуальную сеть с помощью командлета [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* с именем **myVNet** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* с именем подсети **myBackendSubnet** ;
* в виртуальной сети **10.0.0.0/16** ;
* в подсети **10.0.0.0/24**.
* в подсети с именем **AzureBastionSubnet** ;
* в подсети — **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Создание общедоступного IP-адреса для узла Бастиона Azure

Чтобы создать общедоступный IP-адрес узла-бастиона, используйте командлет [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

* с именем **myPublicIPBastion** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* в расположении **eastus**.
* со **статическим** методом выделения;
* с номером SKU **Стандартный** ;

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Создание узла-бастиона Azure

Создайте узел-бастион с помощью командлета [New-AzBastion](/powershell/module/az.network/new-azbastion):

* с именем **myBastion** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* виртуальная сеть **myVNet** ;
* связанный с общедоступным IP-адресом **myPublicIPBastion**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Развертывание узла-бастиона может занять несколько минут.

### <a name="create-network-security-group"></a>Создание группы безопасности сети
Создайте группу безопасности сети для определения входящих подключений к виртуальной сети.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Создание правила группы безопасности сети для порта 80
Создайте правило группы безопасности сети с помощью командлета [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* с именем **myNSGRuleHTTP** ;
* с описанием **Allow HTTP** ;
* с доступом **Allow** ;
* с протоколом **(*)** ;
* с направлением **Inbound** ;
* с приоритетом **2000** ;
* с источником **Internet** ;
* с диапазоном исходных портов **(*)** ;
* с префиксом адреса назначения **(*)** ;
* с конечным портом  **80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Создайте группу безопасности сети с помощью командлета [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* с именем **myNSG** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* в расположении **eastus** ;
* с созданными на предыдущих шагах правилами безопасности, сохраненными в переменной.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-standard-load-balancer"></a>Создание подсистемы балансировки нагрузки (цен. категория "Стандартный")

В этом разделе описано, как создать и настроить следующие компоненты подсистемы балансировки нагрузки:

  * интерфейсный пул IP-адресов, который получает входящий трафик в подсистеме балансировки нагрузки;
  * внутренний пул IP-адресов, на который интерфейсный пул отправляет трафик с балансировкой нагрузки;
  * проверка работоспособности, определяющая работоспособность внутренних экземпляров виртуальной машины;
  * правило подсистемы балансировки нагрузки, определяющее порядок распределения трафика между виртуальными машинами.

### <a name="create-frontend-ip"></a>Создание интерфейсного IP-адреса

Создайте интерфейсный IP-адрес с помощью командлета [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* с именем **myFrontEnd** ;
* с частным IP-адресом **10.0.0.4**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Настройка серверного пула адресов

Создайте серверный пул адресов с помощью командлета [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* с именем **myBackEndPool**.
* Виртуальные машины присоединяются в этот серверный пул на следующих этапах.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Создание зонда работоспособности

При пробе работоспособности выполняется проверка всех экземпляров виртуальной машины, чтобы убедиться, что они могут отправлять сетевой трафик. 

Виртуальная машина с неудачной пробой удаляется из подсистемы балансировки нагрузки и снова добавляется в нее после устранения сбоя.

Создайте пробу работоспособности с помощью командлета [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig), которая:

* отслеживает работоспособность виртуальных машин;
* имеет имя **myHealthProbe** ;
* использует протокол **TCP** ;
* отслеживает **порт 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Создание правила подсистемы балансировки нагрузки

Правило подсистемы балансировки нагрузки определяет:

* конфигурацию интерфейсных IP-адресов для входящего трафика;
* серверный пул IP-адресов для приема трафика;
* требуемые порты источника и назначения. 

Создайте правило подсистемы балансировки нагрузки с помощью командлета [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig), которое: 

* имеет имя **myHTTPRule** ;
* ожидает передачи данных от **порта 80** , используемого интерфейсным пулом **myFrontEnd** ;
* перенаправляет трафик, для которого настроена балансировка нагрузки, ко внутреннему пулу адресов **myBackEndPool** , который использует **порт 80** ; 
* использует пробу работоспособности **myHealthProbe** ;
* использует протокол **TCP** ;
* с временем ожидания перед переходом в режим простоя **15 минут** ;
* и включенной функцией сброса подключений TCP.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT -IdleTimeoutInMinutes $idl -EnableTcpReset
```
>[!NOTE]
>Виртуальные машины во внутреннем пуле не смогут создавать исходящие подключения к Интернету с такой конфигурацией. </br> Дополнительные сведения о включении исходящих подключений см. в следующих статьях: </br> **[Исходящие подключения в Azure](load-balancer-outbound-connections.md)**</br> Варианты обеспечения подключений: </br> **[Конфигурация подсистемы балансировки нагрузки только для исходящего трафика](egress-only.md)** </br> **[Что такое NAT виртуальной сети?](https://docs.microsoft.com/azure/virtual-network/nat-overview)**


### <a name="create-load-balancer-resource"></a>Создание ресурса подсистемы балансировки нагрузки

Создайте внутреннюю подсистему балансировки нагрузки с помощью командлета [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* с именем **myLoadBalancer** ;
* в **eastus** ;
* в группе ресурсов **CreateIntLBQS-rg** ;

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Создание сетевых интерфейсов

Создайте три сетевых интерфейса с помощью командлета [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>Виртуальная машина 1

* с именем **myNicVM1** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* в расположении **eastus** ;
* в виртуальной сети **myVNet** ;
* подсеть **myBackendSubnet** ;
* группа безопасности сети **myNSG** ;
* с подключением к подсистеме балансировки нагрузки **myLoadBalancer** в **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>Виртуальная машина 2

* с именем **myNicVM2** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* в расположении **eastus** ;
* в виртуальной сети **myVNet** ;
* подсеть **myBackendSubnet** ;
* группа безопасности сети **myNSG** ;
* с подключением к подсистеме балансировки нагрузки **myLoadBalancer** в **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Укажите имя и пароль администратора для виртуальной машины с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = Get-Credential
```

Создайте виртуальные машины с помощью следующих командлетов:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* имя **myVM1** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* с подключением к сетевому интерфейсу **myNicVM1** ;
* с подключением к подсистеме балансировки нагрузки **myLoadBalancer** ;
* в **зоне 1** ;
* в расположении **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* имя **myVM2** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* с подключением к сетевому интерфейсу **myNicVM2** ;
* с подключением к подсистеме балансировки нагрузки **myLoadBalancer** ;
* в **зоне 2** ;
* в расположении **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

# <a name="basic-sku"></a>[**SKU "Базовый"**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Для производственных рабочих нагрузок рекомендуется использовать подсистему балансировки нагрузки ценовой категории "Стандартный". Дополнительные сведения о доступных ценовых категориях см. в статье **[Номера SKU для Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Настройка виртуальной сети

Прежде чем развертывать виртуальные машины и тестировать подсистему балансировки нагрузки, создайте вспомогательные ресурсы виртуальной сети.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Создание виртуальной сети и узла Бастиона Azure

Создайте виртуальную сеть с помощью командлета [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* с именем **myVNet** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* с именем подсети **myBackendSubnet** ;
* в виртуальной сети **10.0.0.0/16** ;
* в подсети **10.0.0.0/24**.
* в подсети с именем **AzureBastionSubnet** ;
* в подсети — **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Создание общедоступного IP-адреса для узла Бастиона Azure

Чтобы создать общедоступный IP-адрес узла-бастиона, используйте командлет [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

* с именем **myPublicIPBastion** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* в расположении **eastus**.
* со **статическим** методом выделения;
* с номером SKU **Стандартный** ;

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Создание узла-бастиона Azure

Создайте узел-бастион с помощью командлета [New-AzBastion](/powershell/module/az.network/new-azbastion):

* с именем **myBastion** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* виртуальная сеть **myVNet** ;
* связанный с общедоступным IP-адресом **myPublicIPBastion**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Развертывание узла-бастиона может занять несколько минут.


### <a name="create-network-security-group"></a>Создание группы безопасности сети
Создайте группу безопасности сети для определения входящих подключений к виртуальной сети.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Создание правила группы безопасности сети для порта 80
Создайте правило группы безопасности сети с помощью командлета [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* с именем **myNSGRuleHTTP** ;
* с описанием **Allow HTTP** ;
* с доступом **Allow** ;
* с протоколом **(*)** ;
* с направлением **Inbound** ;
* с приоритетом **2000** ;
* с источником **Internet** ;
* с диапазоном исходных портов **(*)** ;
* с префиксом адреса назначения **(*)** ;
* с конечным портом  **80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Создайте группу безопасности сети с помощью командлета [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* с именем **myNSG** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* в расположении **eastus** ;
* с созданными на предыдущих шагах правилами безопасности, сохраненными в переменной.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-basic-load-balancer"></a>Создание подсистемы балансировки нагрузки уровня "Базовый"

В этом разделе описано, как создать и настроить следующие компоненты подсистемы балансировки нагрузки:

  * интерфейсный пул IP-адресов, который получает входящий трафик в подсистеме балансировки нагрузки;
  * внутренний пул IP-адресов, на который интерфейсный пул отправляет трафик с балансировкой нагрузки;
  * проверка работоспособности, определяющая работоспособность внутренних экземпляров виртуальной машины;
  * правило подсистемы балансировки нагрузки, определяющее порядок распределения трафика между виртуальными машинами.

### <a name="create-frontend-ip"></a>Создание интерфейсного IP-адреса

Создайте интерфейсный IP-адрес с помощью командлета [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* с именем **myFrontEnd** ;
* с частным IP-адресом **10.0.0.4**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Настройка серверного пула адресов

Создайте серверный пул адресов с помощью командлета [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* с именем **myBackEndPool**.
* Виртуальные машины присоединяются в этот серверный пул на следующих этапах.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Создание зонда работоспособности

При пробе работоспособности выполняется проверка всех экземпляров виртуальной машины, чтобы убедиться, что они могут отправлять сетевой трафик. 

Виртуальная машина с неудачной пробой удаляется из подсистемы балансировки нагрузки и снова добавляется в нее после устранения сбоя.

Создайте пробу работоспособности с помощью командлета [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig), которая:

* отслеживает работоспособность виртуальных машин;
* имеет имя **myHealthProbe** ;
* использует протокол **TCP** ;
* отслеживает **порт 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Создание правила подсистемы балансировки нагрузки

Правило подсистемы балансировки нагрузки определяет:

* конфигурацию интерфейсных IP-адресов для входящего трафика;
* серверный пул IP-адресов для приема трафика;
* требуемые порты источника и назначения. 

Создайте правило подсистемы балансировки нагрузки с помощью командлета [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig), которое: 

* имеет имя **myHTTPRule** ;
* ожидает передачи данных от **порта 80** , используемого интерфейсным пулом **myFrontEnd** ;
* перенаправляет трафик, для которого настроена балансировка нагрузки, ко внутреннему пулу адресов **myBackEndPool** , который использует **порт 80** ; 
* использует пробу работоспособности **myHealthProbe** ;
* использует протокол **TCP** ;
* с временем ожидания перед переходом в режим простоя **15 минут** ;

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -IdleTimeoutInMinutes $idl
```

### <a name="create-load-balancer-resource"></a>Создание ресурса подсистемы балансировки нагрузки

Теперь создайте общедоступную подсистему балансировки нагрузки с помощью командлета [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* с именем **myLoadBalancer** ;
* в **eastus** ;
* в группе ресурсов **CreateIntLBQS-rg** ;

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Создание сетевых интерфейсов

Создайте три сетевых интерфейса с помощью командлета [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>Виртуальная машина 1

* с именем **myNicVM1** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* в расположении **eastus** ;
* в виртуальной сети **myVNet** ;
* подсеть **myBackendSubnet** ;
* группа безопасности сети **myNSG** ;
* с подключением к подсистеме балансировки нагрузки **myLoadBalancer** в **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>Виртуальная машина 2

* с именем **myNicVM2** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* в расположении **eastus** ;
* в виртуальной сети **myVNet** ;
* подсеть **myBackendSubnet** ;
* группа безопасности сети **myNSG** ;
* с подключением к подсистеме балансировки нагрузки **myLoadBalancer** в **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>Создание группы доступности для виртуальных машин

Создайте группу доступности для виртуальных машин с помощью командлета [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm):

* с именем **myAvSet** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* в расположении **eastus**.

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'CreateIntLBQS-rg'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Укажите имя и пароль администратора для виртуальной машины с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = Get-Credential
```

Создайте виртуальные машины с помощью следующих командлетов:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* имя **myVM1** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* с подключением к сетевому интерфейсу **myNicVM1** ;
* с подключением к подсистеме балансировки нагрузки **myLoadBalancer** ;
* в расположении **eastus** ;
* в группе доступности **myAvSet**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* имя **myVM2** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* с подключением к сетевому интерфейсу **myNicVM2** ;
* с подключением к подсистеме балансировки нагрузки **myLoadBalancer** ;
* в расположении **eastus** ;
* в группе доступности **myAvSet**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

Создание и настройка трех виртуальных машин занимает несколько минут.

---

## <a name="install-iis"></a>Установка служб IIS

Воспользуйтесь командлетом [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension?view=latest), чтобы установить расширение пользовательских скриптов. 

Это расширение запускает команду PowerShell Add-WindowsFeature Web-Server для установки веб-сервера IIS, а затем обновляет страницу Default.htm для отображения имени узла виртуальной машины.

### <a name="vm1"></a>VM1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>VM2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>Тестирование подсистемы балансировки нагрузки

### <a name="create-network-interface"></a>Создание сетевого интерфейса

Создайте сетевой интерфейс с помощью командлета [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="mytestvm"></a>myTestVM

* с именем **myNicTestVM** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* в расположении **eastus** ;
* в виртуальной сети **myVNet** ;
* подсеть **myBackendSubnet** ;
* группа безопасности сети **myNSG** ;

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicTestVM'
$vnt = 'myVNet'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for myTestVM ##
$nicTestVM = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machine"></a>Создание виртуальной машины

Укажите имя и пароль администратора для виртуальной машины с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = Get-Credential
```

Создайте виртуальную машину с помощью следующих командлетов:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="mytestvm"></a>myTestVM

* с именем **myTestVM** ;
* в группе ресурсов **CreateIntLBQS-rg** ;
* с подключением к сетевому интерфейсу **myNicTestVM** ;
* в расположении **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myTestVM'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'


## Create a virtual machine configuration. $cred and $nicTestVM are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicTestVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

### <a name="test"></a>Тест

1. [Войдите](https://portal.azure.com) на портал Azure.

1. Найдите частный IP-адрес для подсистемы балансировки нагрузки на экране **обзора**. В меню слева щелкните **Все службы** , выберите **Все ресурсы** , а затем — **myLoadBalancer**.

2. Запишите или скопируйте адрес рядом с **частным IP-адресом** на экране **обзора** **myLoadBalancer**.

3. В меню слева щелкните **Все службы** , выберите **Все ресурсы** , а затем в списке ресурсов выберите виртуальную машину **myTestVM** , расположенную в группе ресурсов **CreateIntLBQS-rg**.

4. На странице **Обзор** выберите **Подключиться** и **Бастион**.

6. Введите имя пользователя и пароль, введенные в процессе создания виртуальной машины.

7. На виртуальной машине **myTestVM** откройте браузер **Internet Explorer**.

8. Введите IP-адрес с предыдущего шага в адресную строку браузера. В браузере отобразится страница по умолчанию веб-сервера IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Создание внутренней подсистемы балансировки нагрузки уровня Стандартный" border="true":::
   
Чтобы подсистема балансировки нагрузки распределяла трафик между всеми тремя виртуальными машинами, вы можете настроить стандартную страницу веб-сервера IIS каждой из них, а затем принудительно обновить браузер с клиентского компьютера.

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить группу ресурсов, подсистему балансировки нагрузки и все остальные ресурсы, можно воспользоваться командлетом [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
## Variable for command. ##
$rg = 'CreateIntLBQS-rg'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как:

* создать внутреннюю подсистему балансировки нагрузки уровня "Стандартный" или "Базовый";
* подключить к ней виртуальные машины; 
* настроить правило трафика подсистемы балансировки нагрузки и пробу работоспособности;
* тестировать подсистему балансировки нагрузки.

Чтобы узнать больше об Azure Load Balancer, ознакомьтесь со следующей статьей:
> [!div class="nextstepaction"]
> [Что такое Azure Load Balancer](load-balancer-overview.md)


