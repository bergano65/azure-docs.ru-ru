---
title: Создание виртуальной Машины со статическим частным IP-адрес адресом — Azure PowerShell | Документация Майкрософт
description: Узнайте, как создать виртуальную машину с помощью частного IP-адреса, с помощью PowerShell.
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
ms.openlocfilehash: 9115386b0543e1ac840aec29fc7f57e7c98c03bb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685333"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Создание виртуальной машины статический частный IP-адрес, с помощью PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Статический частный IP-адрес можно создать виртуальную машину (ВМ). Назначьте статический частный IP-адрес, а не динамический адрес, если вы хотите выбрать, какой адрес из подсети, назначаемый виртуальной Машине. Дополнительные сведения о [статические частные IP-адреса](virtual-network-ip-addresses-overview-arm.md#allocation-method). Чтобы изменить частный IP-адрес, назначенный существующей виртуальной Машины с динамического на статический, или для работы с общедоступных IP-адресов, см. в разделе [Добавление, изменение и удаление IP-адреса](virtual-network-network-interface-addresses.md).

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Указанные ниже действия можно выполнить с помощью локального компьютера или Azure Cloud Shell. Чтобы использовать локальный компьютер, на нем должна быть установлена служба [Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Чтобы использовать Azure Cloud Shell, выберите функцию **Попробовать** в правом верхнем углу любого окна со следующими командами. Через Cloud Shell будет выполнен вход в Azure.

1. При использовании Cloud Shell перейдите к шагу 2. Откройте сеанс командной строки и войдите в Azure с помощью команды `Connect-AzAccount`.
2. Создайте группу ресурсов с помощью команды [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). В следующем примере создается группа ресурсов в регионе Azure "Восточная часть США".

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Создайте конфигурацию подсети и виртуальной сети с помощью [New AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) и [New AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) команды:

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

4. Создайте сетевой интерфейс в виртуальной сети и назначить частный IP-адрес из подсети сетевой интерфейс с [New AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) и [New AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) команды:

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

5. Создайте конфигурацию виртуальной Машины с [New AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig), а затем создайте виртуальную Машину с [командлета New-AzVM](/powershell/module/az.Compute/New-azVM). При запросе укажите имя пользователя и пароль для использования в качестве подписи в учетных данных для виртуальной Машины:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Если вы можете добавить параметры частного IP-адресов в операционную систему, мы рекомендуем не делать это до и после прочтения [добавить частный IP-адрес для операционной системы](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> Для доступа к виртуальной Машине из Интернета, необходимо назначить общедоступный IP-адрес к виртуальной Машине. Можно также изменить динамический частный назначение IP-адресов для назначения статического. Дополнительные сведения см. в разделе [Добавление или изменение IP-адреса](virtual-network-network-interface-addresses.md). Кроме того рекомендуется ограничить сетевой трафик к виртуальной Машине, связав группу безопасности сети сетевому интерфейсу или подсети, к которой вы создали в сетевом интерфейсе. Дополнительные сведения см. в разделе [Управление группами безопасности сети](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы с помощью командлета [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [частные IP-адреса](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) и назначение [статический частный IP-адрес](virtual-network-network-interface-addresses.md#add-ip-addresses) к виртуальной машине Azure.
- Дополнительные сведения о создании [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) и [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) виртуальных машин.
