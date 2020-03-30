---
title: Создание VM со статичным личным IP-адресом - Azure PowerShell
description: Узнайте, как создать виртуальную машину с личным IP-адресом с помощью PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1745ca176fac18b4903686cb556670531ee40a1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244762"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Создание виртуальной машины со статичным личным IP-адресом с помощью PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Можно создать виртуальную машину (VM) со статичным личным IP-адресом. Назначайте статический частный IP-адрес, а не динамический адрес, если вы хотите выбрать, какой адрес из подсети назначен VM. Узнайте больше о [статических частных IP-адресах.](virtual-network-ip-addresses-overview-arm.md#allocation-method) Чтобы изменить личный IP-адрес, присвоенный существующему VM, от динамического к статическому или работать с общедоступными IP-адресами, [см.](virtual-network-network-interface-addresses.md)

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Указанные ниже действия можно выполнить с помощью локального компьютера или Azure Cloud Shell. Чтобы использовать локальный компьютер, на нем должна быть установлена служба [Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Чтобы использовать Azure Cloud Shell, выберите функцию **Попробовать** в правом верхнем углу любого окна со следующими командами. Через Cloud Shell будет выполнен вход в Azure.

1. При использовании Cloud Shell перейдите к шагу 2. Откройте сеанс командной строки и войдите в Azure с помощью команды `Connect-AzAccount`.
2. Создайте группу ресурсов с помощью команды [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). В следующем примере создается группа ресурсов в регионе Azure "Восточная часть США".

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Создайте конфигурацию подсети и виртуальную сеть с новыми [командами AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) и [New-AzVirtualNetwork:](/powershell/module/az.network/new-azvirtualnetwork)

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Создайте сетевой интерфейс в виртуальной сети и присвоите частный IP-адрес из подсети в сетевом интерфейсе с командами [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) и [New-AzNetworkInterface:](/powershell/module/az.network/new-aznetworkinterface)

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. Создайте конфигурацию VM с [Помощью New-AzVMConfig,](/powershell/module/Az.Compute/New-AzVMConfig)а затем создайте VM с [помощью New-AzVM.](/powershell/module/az.Compute/New-azVM) По запросу укажите имя пользователя и пароль, которые будут использоваться в качестве знака в учетных данных для VM:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Хотя вы можете добавить в операционную систему настройки приватных IP-адресов, мы рекомендуем не делать этого до тех пор, пока не прочитайте [частный IP-адрес в операционную систему.](virtual-network-network-interface-addresses.md#private)
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> Чтобы получить доступ к VM из Интернета, вы должны назначить общественный IP-адрес для VM. Можно также изменить динамическое назначение частного IP-адреса на статическое назначение. Для получения подробной информации можно [ознакомиться с ip-адресами.](virtual-network-network-interface-addresses.md) Кроме того, рекомендуется ограничить сетевой трафик вашим VM, связав группу сетевой безопасности с сетевым интерфейсом, подсетью, в которую вы создали сетевой интерфейс, или и тем, и другим. Для получения подробной информации [см.](manage-network-security-group.md)

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы с помощью командлета [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [частных IP-адресах](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) и назначении [статического частного IP-адреса](virtual-network-network-interface-addresses.md#add-ip-addresses) виртуальной машине Azure.
- Узнайте больше о создании виртуальных машин [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) и [Windows.](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
