---
title: Руководство по созданию и проверке шлюза NAT — Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: В этом учебнике объясняется, как создать шлюз NAT с помощью Azure PowerShell и проверить услугу NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 61cda5e61d14c4eeaf2d88483603707598b1c911
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202244"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Руководство по созданию шлюза NAT с помощью Azure PowerShell и проверке NAT

Из этого учебника вы узнаете, как создать шлюз NAT для обеспечения исходящего подключения в виртуальных машинах в Azure. Чтобы протестировать шлюз NAT, необходимо развернуть виртуальные машины источника и назначения. Чтобы проверить шлюз NAT, установите исходящие подключения по общедоступному IP-адресу. Из исходной виртуальной машины будут выполняться подключения к виртуальной машине назначения. В этом учебнике развертываются виртуальные машины источника и назначения в двух разных виртуальных сетях, но для простоты — в одной и той же группе ресурсов.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Инструкции из этого учебника можно выполнить, используя Azure Cloud Shell или запустив соответствующие команды локально.  Если вы никогда не использовали Azure Cloud Shell, необходимо [выполнить вход](https://shell.azure.com) в систему сейчас.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем **myResourceGroupNAT** в расположении **eastus2**:


```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Создание шлюза NAT

### <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса

Для доступа к Интернету требуется один или несколько общедоступных IP-адресов для шлюза NAT. Выполните командлет [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest), чтобы создать ресурс общедоступного IP-адреса с именем **myPublicIPsource** в группе **myResourceGroupNAT**. Результат этой команды будет храниться в переменной с именем **$publicIPsource** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pips = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pips -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Создание префикса общедоступного IP-адреса

 Выполните командлет [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest), чтобы создать ресурс префикса общедоступного IP-адреса с именем **myPublicIPprefixsource** в группе **myResourceGroupNAT**.  Результат этой команды будет храниться в переменной с именем **$publicIPPrefixsource** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$prips = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prips -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Создание ресурса шлюза NAT

В этом разделе подробно описано, как создать и настроить следующие компоненты NAT с использованием ресурса Шлюза NAT:
  - пул общедоступных IP-адресов и префикс общедоступного IP-адреса, которые будут использоваться для исходящих потоков, преобразованных ресурсом шлюза NAT;
  - изменение значения времени ожидания простоя с 4 минут по умолчанию на 10 минут.

Создайте глобальный шлюз Azure NAT с помощью командлета [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). В результате ее выполнения будет создан ресурс шлюза с именем **myNATgateway**, использующий общедоступный IP-адрес **myPublicIPsource** и префикс общедоступного IP-адреса **myPublicIPprefixsource**. Установите время ожидания простоя на 10 минут.  Результат этой команды будет храниться в переменной с именем **$natGateway** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$nnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $nnm -ResourceGroupName $rsg -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Location $loc -Sku $sku -IdleTimeoutInMinutes 10      
  ```

На этом этапе шлюз NAT функционирует, и все, что осталось сделать — это настроить, в каких подсетях виртуальной сети он должен использоваться.

## <a name="prepare-the-source-for-outbound-traffic"></a>Подготовка источника для исходящего трафика

Мы расскажем вам, как полностью настроить тестовую среду. Вы настроите тест, который будет использовать средства с открытым кодом, чтобы проверить шлюз NAT. Мы начнем с источника, который будет использовать шлюз NAT, созданный ранее.

### <a name="configure-virtual-network-for-source"></a>Настройка виртуальной сети для источника

Создайте виртуальную сеть и свяжите подсеть со шлюзом.

Создайте виртуальную сеть с именем **myVnetsource** и подсетью **mySubnetsource** с помощью командлета [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) в группе **myResourceGroupNAT**, используя командлет [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Диапазон IP-адресов для виртуальной сети — **192.168.0.0/16**. Подсеть в виртуальной сети — **192.168.0.0/24**.  Результаты команд будут храниться в переменных с именами **$subnetsource** и **$vnetsource** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$sbn = 'mySubnetsource'
$spfx = '192.168.0.0/24'
$vnm = 'myVnetsource'
$vpfx = '192.168.0.0/16'
$loc = 'eastus2'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $sbn -AddressPrefix $spfx -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnet
```

NAT теперь используется для всего исходящего трафика для назначений в Интернете.  Нет необходимости настраивать UDR.

Чтобы можно было протестировать шлюз NAT, необходимо создать исходную виртуальную машину.  Ресурс общедоступного IP-адреса будет назначен как общедоступный IP-адрес уровня экземпляра для доступа к этой виртуальной машине извне. Этот адрес используется, только чтобы получить доступ для теста.  Мы покажем, что настройки NAT будут приоритетнее, чем другие параметры исходящего трафика.

В качестве упражнения можно создать эту виртуальную машину без общедоступного IP-адреса, а также другую виртуальную машину, чтобы использовать ее как инсталяционный сервер без общедоступного IP-адреса.

### <a name="create-public-ip-for-source-vm"></a>Создание общедоступного IP-адреса для исходной виртуальной машины

Мы создадим общедоступный IP-адрес для доступа к виртуальной машине.  Выполните командлет [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest), чтобы создать ресурс общедоступного IP-адреса с именем **myPublicIPVM** в группе **myResourceGroupNAT**.  Результат этой команды будет храниться в переменной с именем **$publicIpsourceVM** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pipvm = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvm -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Создание группы безопасности сети (NSG) и предоставление конечной точки SSH для виртуальной машины

Так как стандартные общедоступные IP-адреса безопасны по умолчанию, мы создадим NSG, чтобы разрешить входящий доступ по протоколу SSH. В NAT учитывается направление потоков. Эта NSG не будет использоваться для исходящего трафика, если шлюз NAT настроен в той же подсети. Выполните командлет [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest), чтобы создать ресурс NSG с именем **myNSGsource**. Выполните командлет [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest), чтобы создать правило NSG доступа по протоколу SSH с именем **ssh** в группе **myResourceGroupNAT**. Результат этой команды будет храниться в переменной с именем **$nsgsource** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-source-vm"></a>Создание сетевой карты для исходной виртуальной машины

С помощью командлета [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) создайте сетевой интерфейс с именем **myNicsource**. Эта команда свяжет общедоступный IP-адрес и группу безопасности сети. Результат этого командлета будет храниться в переменной с именем **$nicsource** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $loc
```

### <a name="create-a-source-vm"></a>Создание исходной виртуальной машины

#### <a name="create-ssh-key-pair"></a>Создание пары ключей SSH

Для работы с этим кратким руководством вам понадобится пара ключей SSH. Если у вас уже есть эта пара, можно перейти к следующему шагу.

Выполните команду ssh-keygen, чтобы создать пару ключей SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Подробные сведения о создании пар ключей SSH, в том числе с помощью PuTTy, вы найдете в статье [Как использовать ключи SSH с Windows в Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

Если вы создадите пару ключей SSH с помощью Cloud Shell, она будет храниться в образе контейнера. [Автоматически будет создана эта учетная запись хранения](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Не удаляйте эту учетную запись хранения или общую папку в ней, пока не получите ключи.

#### <a name="create-vm-configuration"></a>Создание конфигурации виртуальной машины

Чтобы создать виртуальную машину в PowerShell, создайте конфигурацию, у которой будут такие параметры, как используемый образ, размер и параметры проверки подлинности. Затем эта конфигурация используется для создания виртуальной машины.

Задайте учетные данные для SSH, сведения об ОС и размер виртуальной машины. В этом примере ключ SSH хранится в файле ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Объедините определения конфигурации, чтобы создать виртуальную машину с именем **myVMsource** с помощью командлета [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) в группе **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigsource
```

Хотя команда и вернется немедленно, развертывание виртуальной машины может занять несколько минут.

## <a name="prepare-destination-for-outbound-traffic"></a>Подготовка назначения для исходящего трафика

Теперь мы создадим виртуальную машину назначения для исходящего трафика, преобразовываемого с помощью NAT, чтобы протестировать эту машину.

### <a name="configure-virtual-network-for-destination"></a>Настройка виртуальной сети для назначения

Необходимо создать виртуальную сеть, в которой будет виртуальная машина назначения.  Здесь выполняются те же шаги с командами, что и для исходной виртуальной машины. Добавлены небольшие изменения, чтобы предоставить конечную точку назначения.

Создайте виртуальную сеть с именем **myVnetdestination** с подсетью **mySubnetdestination** с помощью командлета [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) в группе **myResourceGroupNAT**, используя командлет [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Диапазон IP-адресов для виртуальной сети — **192.168.0.0/16**. Подсеть в виртуальной сети — **192.168.0.0/24**.  Результат команд будет храниться в переменных с именами **$subnetdestination** и **$vnetdestination** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnetdestination
```

### <a name="create-public-ip-for-destination-vm"></a>Создание общедоступного IP-адреса для виртуальной машины назначения

Мы создадим общедоступный IP-адрес для доступа к целевой виртуальной машине.  Выполните командлет [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest), чтобы создать ресурс общедоступного IP-адреса с именем **myPublicIPdestinationVM** в группе **myResourceGroupNAT**.  Результат этой команды будет храниться в переменной с именем **$publicIpdestinationVM** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rsg -AllocationMethod $all -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Создание NSG и предоставление конечной точки SSH и HTTP для виртуальной машины

Стандартные общедоступные IP-адреса безопасны по умолчанию, и мы можем создать NSG, чтобы разрешить входящий доступ по протоколу SSH. Выполните командлет [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest), чтобы создать ресурс NSG с именем **myNSGdestination**. Используйте командлет [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest), чтобы создать правило NSG доступа по протоколу SSH с именем **ssh**.  Используйте командлет [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest), чтобы создать правило NSG доступа по протоколу HTTP с именем **http**. Оба правила будут созданы в группе **myResourceGroupNAT**. Результат этой команды будет храниться в переменной с именем **$nsgdestination** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule,$httprule
```

### <a name="create-nic-for-destination-vm"></a>Создание сетевой карты для виртуальной машины назначения

С помощью командлета [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) создайте сетевой интерфейс с именем **myNicdestination**. Эта команда свяжет общедоступный IP-адрес и группу безопасности сети. Результат этой команды будет храниться в переменной с именем **$nicdestination** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $loc
```

### <a name="create-a-destination-vm"></a>Создание виртуальной машины назначения

#### <a name="create-vm-configuration"></a>Создание конфигурации виртуальной машины

Чтобы создать виртуальную машину в PowerShell, создайте конфигурацию, у которой будут такие параметры, как используемый образ, размер и параметры проверки подлинности. Затем эта конфигурация используется для создания виртуальной машины.

Задайте учетные данные для SSH, сведения об ОС и размер виртуальной машины. В этом примере ключ SSH хранится в файле ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$vmd = 'myVMdestination'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Объедините определения конфигурации, чтобы создать виртуальную машину с именем **myVMdestination** с помощью командлета [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) в группе **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigdestination
```

Хотя команда и вернется немедленно, развертывание виртуальной машины может занять несколько минут.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Подготовка веб-сервера и тестовых полезных данных для виртуальной машины назначения

Сначала необходимо найти IP-адрес виртуальной машины назначения.  Чтобы получить его, выполните командлет [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPdestinationVM'
  
Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Скопируйте общедоступный IP-адрес и вставьте его в блокнот, чтобы его можно было использовать в дальнейшем. Укажите, что это виртуальная машина назначения.

### <a name="sign-in-to-destination-vm"></a>Вход в виртуальную машину назначения

Учетные данные SSH должны сохраниться в Cloud Shell после предыдущей операции.  Откройте [Azure Cloud Shell](https://shell.azure.com) в браузере. Используйте IP-адрес, полученный на предыдущем шаге, для подключения к виртуальной машине по протоколу SSH. 

```bash
ssh azureuser@<ip-address-destination>
```

После входа скопируйте и вставьте следующие команды.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Эти команды обновят виртуальную машину, установят nginx и создадут файл размером 100 КБ. Этот файл будет извлечен из виртуальной машины источника с помощью NAT.

Закройте сеанс SSH с виртуальной машиной назначения.

## <a name="prepare-test-on-source-vm"></a>Подготовка теста для виртуальной машины источника

Сначала необходимо найти IP-адрес виртуальной машины источника.  Чтобы получить его, выполните командлет [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPsourceVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Скопируйте общедоступный IP-адрес и вставьте его в блокнот, чтобы его можно было использовать в дальнейшем. Укажите, что это виртуальная машина-источник.

### <a name="log-into-source-vm"></a>Вход в виртуальную машину-источник

Учетные данные SSH по прежнему будут храниться в Cloud Shell. Откройте новую вкладку для [Azure Cloud Shell](https://shell.azure.com) в браузере.  Используйте IP-адрес, полученный на предыдущем шаге, для подключения к виртуальной машине по протоколу SSH. 

```bash
ssh azureuser@<ip-address-source>
```

Скопируйте и вставьте следующие команды, чтобы подготовиться к тестированию NAT.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Эти команды обновят виртуальную машину и среду оболочки, установят компоненты Go и [hey](https://github.com/rakyll/hey) из GitHub.

Теперь вы готовы к тестированию NAT.

## <a name="validate-nat-service"></a>Проверка NAT

Войдите в исходную виртуальную машину и используйте **curl** и **hey** для создания запросов по IP-адресу назначения.

Используйте curl для извлечения файла размером 100 КБ.  В примере ниже замените **\<ip-address-destination>** IP-адресом назначения, скопированным ранее.  Параметр **--output** указывает, что полученный файл будет удален.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Можно также создать серию запросов с помощью **hey**. Точно так же замените **\<ip-address-destination>** IP-адресом назначения, скопированным ранее.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Эта команда будет создавать по 100 запросов, 10 из них — параллельно, со временем ожидания 30 секунд. TCP-подключение не будет использоваться повторно.  Каждый запрос будет получать файл размером 100 КБ.  В конце выполнения **hey** сообщит некоторые статистические данные о качестве работы NAT.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить группу ресурсов и все ресурсы в ней, если они больше не нужны, выполнив команду [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest).

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Дальнейшие действия
В этом учебнике вы создали шлюз NAT, виртуальные машины источника и назначения, а затем проверили шлюз NAT.

Просмотрите метрики в Azure Monitor, чтобы проверить работу NAT. Выполните диагностику проблем, таких как нехватка ресурсов доступных портов SNAT.  Эта проблема легко устраняется путем добавления ресурсов общедоступного IP-адреса, ресурсов префикса общедоступного IP-адреса, или их сочетания.

- Дополнительные сведения о [NAT виртуальной сети](./nat-overview.md).
- Дополнительные сведения о [ресурсе шлюза NAT](./nat-gateway-resource.md).
- Краткое руководство по развертыванию [ресурса Шлюза NAT с помощью Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Краткое руководство по развертыванию [ресурса Шлюза NAT с помощью Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Краткое руководство по развертыванию [ресурса Шлюза NAT с помощью портала Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

