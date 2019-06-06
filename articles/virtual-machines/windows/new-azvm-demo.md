---
title: Создание виртуальной машины Windows с помощью упрощенного командлета New-AzVM в Azure Cloud Shell | Документация Майкрософт
description: Научитесь быстро создавать виртуальные машины Windows с помощью упрощенного командлета New-AzVM в Azure Cloud Shell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: cdf7385b3686c5c3f91e66c67ab5f0758935b39f
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730128"
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azvm-cmdlet-in-cloud-shell"></a>Создание виртуальной машины Windows с помощью упрощенного командлета New-AzVM в Cloud Shell 

В командлет [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) добавлен упрощенный набор параметров для создания виртуальной машины с помощью PowerShell. В этом разделе описано, как использовать PowerShell в Azure Cloud Shell с последней версией предварительно установленного командлета New-AzureVM для создания виртуальной машины. Мы будем использовать упрощенный набор параметров, с помощью которого можно автоматически создать все необходимые ресурсы, используя стандартные интеллектуальные значения. 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]


## <a name="create-the-vm"></a>Создание виртуальной машины

Можно использовать командлет [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) для создания виртуальной машины с интеллектуальными значениями по умолчанию, включая использование образа Windows Server 2016 Datacenter из Azure Marketplace. Вы можете использовать командлет New-AzVM только с параметром **-Name**, и это значение будет использоваться для всех названий ресурсов. В этом примере в качестве параметра **-Name** задается значение *myVM*. 

Убедитесь, что **PowerShell** выбран в Cloud Shell, и введите:

```azurepowershell-interactive
New-AzVm -Name myVM
```

Вам будет предложено создать имя пользователя и пароль для виртуальной машины, которые будут использоваться при подключении к виртуальной машине далее в этой статье. Пароль должен содержать от 12 до 123 символов и включать по меньшей мере три из следующих символов: одна строчная буква, одна прописная буква, одна цифра и один специальный символ.

Создание виртуальной машины и связанных с ней ресурсов занимает минуту. По завершении вы увидите все ресурсы, которые были созданы с помощью командлета [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource).

```azurepowershell-interactive
Get-AzResource `
    -ResourceGroupName myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Подключение к виртуальной машине

После завершения развертывания создайте подключение к удаленному рабочему столу виртуальной машины.

Верните общедоступный IP-адрес виртуальной машины, выполнив команду [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). Запишите этот IP-адрес.

```azurepowershell-interactive
Get-AzPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

На локальном компьютере откройте командную строку и введите команду **mstsc** для запуска сеанса удаленного рабочего стола для новой виртуальной машины. Замените &lt;publicIPAddress&gt; IP-адресом виртуальной машины. При появлении запроса введите имя пользователя и пароль, предоставленные вашей виртуальной машине при создании.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Указание разных названий ресурсов

Вы можете также указать понятные имена для ресурсов. Они по-прежнему будут созданы автоматически. Ниже приведен пример присвоения имен нескольким ресурсам новой виртуальной машины, включая новую группу ресурсов.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myVM
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

При работе с этой статьей вы развернули простую виртуальную машину с помощью командлета New-AzVM, а затем подключились к ней с помощью протокола удаленного рабочего стола. Дополнительные сведения о виртуальных машинах Azure см. в руководстве по работе с виртуальными машинами Windows.

> [!div class="nextstepaction"]
> [Создание виртуальных машин Windows и управление ими с помощью модуля Azure PowerShell](./tutorial-manage-vm.md)
