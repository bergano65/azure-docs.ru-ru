---
title: Создание шлюза NAT — Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: В этом кратком руководстве показано, как создать шлюз NAT с помощью Azure PowerShell
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 516f858c8f95ec3f7cfd8ffc1592358716986519
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223318"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Руководство по Создание шлюза NAT с помощью Azure PowerShell

В этом руководстве показано, как использовать услугу преобразования сетевых адресов (NAT) в виртуальной сети Azure. Вы узнаете, как создать шлюз NAT для обеспечения исходящего подключения в виртуальных машинах в Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Инструкции из этого учебника можно выполнить, используя Azure Cloud Shell или запустив команды локально.  Если вы никогда не использовали Azure Cloud Shell, [выполните вход](https://shell.azure.com) сейчас.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью командлета [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?view=latest). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем **myResourceGroupNAT** в расположении **eastus2**:

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>создание шлюза NAT

Параметры общедоступного IP-адреса для шлюза NAT:

* **Типы IP-адресов и методы распределения в Azure**
* **Префиксы общедоступных IP-адресов**

Оба варианта можно использовать со шлюзом NAT.

Для демонстрации в этот сценарий будут добавлены адрес и префикс общедоступного IP-адреса.

### <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса

Для доступа к Интернету требуется один или несколько общедоступных IP-адресов для шлюза NAT. Выполните командлет [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest), чтобы создать ресурс общедоступного IP-адреса с именем **myPublicIP** в группе **myResourceGroupNAT**. Результат этого командлета будет храниться в переменной **$publicIP** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Создание префикса общедоступного IP-адреса

Выполните командлет [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix?view=latest), чтобы создать ресурс префикса общедоступного IP-адреса с именем **myPublicIPprefix** в группе **myResourceGroupNAT**.  Результат этой команды будет храниться в переменной с именем **$publicIPPrefix** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Создание ресурса шлюза NAT

В этом разделе подробно описано, как создать и настроить следующие компоненты NAT с использованием ресурса Шлюза NAT:
  - пул общедоступных IP-адресов и префикс общедоступного IP-адреса, которые будут использоваться для исходящих потоков, преобразованных ресурсом шлюза NAT;
  - изменение значения времени ожидания простоя с 4 минут по умолчанию на 10 минут.

Создайте глобальный шлюз Azure NAT с помощью командлета [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). В результате ее выполнения будет создан ресурс шлюза с именем **myNATgateway**, использующий общедоступный IP-адрес **myPublicIP** и префикс общедоступного IP-адреса **myPublicIPprefix**. Установите время ожидания простоя на 10 минут.  Результат этой команды будет храниться в переменной с именем **$natGateway** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

На этом этапе шлюз NAT функционирует, и все, что осталось сделать — это настроить, в каких подсетях виртуальной сети он должен использоваться.

## <a name="configure-virtual-network"></a>Настройка виртуальной сети

Создайте виртуальную сеть и свяжите подсеть со шлюзом.

Создайте виртуальную сеть с именем **myVnet** и подсетью **mySubnet** с помощью командлета [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) в группе **myResourceGroup**, используя командлет [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork?view=latest). Диапазон IP-адресов для виртуальной сети — **192.168.0.0/16**. Подсеть в виртуальной сети — **192.168.0.0/24**.  Результаты команд будут храниться в переменных с именами **$subnet** и **$vnet** для последующего использования.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

NAT теперь используется для всего исходящего трафика для назначений в Интернете.  Нет необходимости настраивать UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Создание виртуальной машины для использования NAT

Теперь мы создадим виртуальную машину для использования NAT.  У этой виртуальной машины будет общедоступный IP-адрес для использования на уровне экземпляра, чтобы вы могли получать доступ к ней.  В NAT учитывается направление потоков и заменяется назначение в Интернете по умолчанию в подсети. Общедоступный IP-адрес виртуальной машины не будет использоваться для исходящих соединений.

### <a name="create-public-ip-for-source-vm"></a>Создание общедоступного IP-адреса для исходной виртуальной машины

Мы создадим общедоступный IP-адрес для доступа к виртуальной машине.  Выполните командлет [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest), чтобы создать ресурс общедоступного IP-адреса с именем **myPublicIPVM** в группе **myResourceGroupNAT**.  Результат этой команды будет храниться в переменной с именем **$publicIpVM** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Создание группы безопасности сети (NSG) и предоставление конечной точки SSH для виртуальной машины

Так как стандартные общедоступные IP-адреса безопасны по умолчанию, необходимо создать NSG, чтобы разрешить входящий доступ по протоколу SSH. Выполните командлет [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup?view=latest), чтобы создать ресурс NSG с именем **myNSG**. Выполните командлет [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest), чтобы создать правило NSG доступа по протоколу SSH с именем **ssh** в группе **myResourceGroupNAT**.  Результат этого командлета будет храниться в переменной с именем **$nsg** для последующего использования.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>Создание сетевой карты для виртуальной машины

С помощью командлета [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) создайте сетевой интерфейс с именем **myNic**. Эта команда свяжет общедоступный IP-адрес и группу безопасности сети. Результат этого командлета будет храниться в переменной с именем **$nic** для последующего использования.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>Создание виртуальной машины

#### <a name="create-ssh-key-pair"></a>Создание пары ключей SSH

Для работы с этим кратким руководством вам понадобится пара ключей SSH. Если у вас уже есть эта пара, можно перейти к следующему шагу.

Выполните команду ssh-keygen, чтобы создать пару ключей SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Подробные сведения о создании пар ключей SSH, в том числе с помощью PuTTy, вы найдете в статье [Как использовать ключи SSH с Windows в Azure](../virtual-machines/linux/ssh-from-windows.md).

Если вы создадите пару ключей SSH с помощью Cloud Shell, она будет храниться в образе контейнера. [Автоматически будет создана эта учетная запись хранения](../cloud-shell/persisting-shell-storage.md). Не удаляйте эту учетную запись хранения или общую папку в ней, пока не получите ключи.

#### <a name="create-vm-configuration"></a>Создание конфигурации виртуальной машины

Чтобы создать виртуальную машину в PowerShell, создайте конфигурацию, у которой будут такие параметры для используемого образа, размера и параметров проверки подлинности. Конфигурация используется для создания виртуальной машины.

Задайте учетные данные для SSH, сведения об ОС и размер виртуальной машины. В этом примере ключ SSH хранится в файле ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Объедините определения конфигурации, чтобы создать виртуальную машину с именем **myVM** с помощью командлета [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) в группе **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Дождитесь, пока виртуальная машина будет готова к развертыванию и продолжите выполнение остальных шагов.

## <a name="discover-the-ip-address-of-the-vm"></a>Обнаружение IP-адреса виртуальной машины

Сначала необходимо найти IP-адрес виртуальной машины, которую вы создали. Чтобы получить его, выполните командлет [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Скопируйте общедоступный IP-адрес и вставьте его в блокнот, чтобы этот адрес можно было использовать для доступа к виртуальной машине.

### <a name="sign-in-to-vm"></a>Вход в виртуальную машину

Учетные данные SSH должны сохраниться в Cloud Shell после предыдущей операции.  Откройте [Azure Cloud Shell](https://shell.azure.com) в браузере. Используйте IP-адрес, полученный на предыдущем шаге, для подключения к виртуальной машине по протоколу SSH.

```bash
ssh azureuser@<ip-address-destination>
```

Теперь можно использовать NAT.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить группу ресурсов и все ресурсы в ней, если они больше не нужны, выполнив команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=latest).

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы создали шлюз NAT и виртуальную машину для его использования. 

Просмотрите метрики в Azure Monitor, чтобы проверить работу NAT. Выполните диагностику проблем, таких как нехватка ресурсов доступных портов SNAT.  Эта проблема устраняется путем добавления ресурсов общедоступного IP-адреса, ресурсов префикса общедоступного IP-адреса, или их сочетания.


- Дополнительные сведения о [NAT виртуальной сети Azure](./nat-overview.md).
- Дополнительные сведения о [ресурсе шлюза NAT](./nat-gateway-resource.md).
- Краткое руководство по развертыванию [ресурса Шлюза NAT с помощью Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Краткое руководство по развертыванию [ресурса Шлюза NAT с помощью Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Краткое руководство по развертыванию [ресурса Шлюза NAT с помощью портала Azure](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]