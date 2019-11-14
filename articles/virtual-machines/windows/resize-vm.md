---
title: Изменение размера виртуальной машины Windows в Azure с помощью PowerShell
description: Изменение размера виртуальной машины Windows, созданной в модели развертывания с помощью Resource Manager, с использованием Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 4b30f2fd8e095b00898e083e33c23c7c9a915b99
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073372"
---
# <a name="resize-a-windows-vm"></a>Изменение размера виртуальной машины Windows

В этой статье показано, как изменить [размер](sizes.md) виртуальной машины с помощью Azure Powershell.

После того как вы создали виртуальную машину, ее можно масштабировать, изменяя размер. В некоторых случаях сначала необходимо освободить виртуальную машину. Это может случиться, если новый размер недоступен в кластере оборудования, в котором она сейчас размещена.

Если виртуальная машина использует хранилище класса Premium, выберите версию размера **s**, чтобы получить поддержку этого хранилища. Например, выберите Standard_E4**s**_v3 вместо Standard_E4_v3.

 

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Изменение размера виртуальной машины Windows, не входящей в группу доступности

Задайте несколько переменных. Подставьте собственные значения.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Выведите список размеров виртуальной машины, доступных в кластере оборудования, в котором она размещена. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Если в списке есть нужный размер, выполните следующие команды, чтобы изменить размер виртуальной машины. Если нужный размер отсутствует в списке, перейдите к шагу 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Если нужного размера нет в списке, выполните следующие команды, чтобы освободить виртуальную машину, изменить ее размер и перезагрузить. Замените **\<неввмсизе >** требуемым размером.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> При освобождении виртуальной машины освобождаются все назначенные ей динамические IP-адреса. Это не влияет на диски ОС и данных. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Изменение размера виртуальной машины Windows в группе доступности

Если новый размер виртуальной машины, входящей в группу доступности, недоступен в кластере оборудования, в котором она сейчас размещена, то для изменения размера этой виртуальной машины потребуется освободить все виртуальные машины в данной группе доступности. После изменения размера одной виртуальной машины может также потребоваться обновить размер остальных виртуальных машин в группе доступности. Чтобы изменить размер виртуальной машины в группе доступности, выполните следующие действия.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Выведите список размеров виртуальной машины, доступных в кластере оборудования, в котором она размещена. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Если в списке есть нужный размер, выполните следующие команды, чтобы изменить размер виртуальной машины. Если его нет в списке, перейдите к следующему разделу.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Если нужный размер отсутствует в списке, выполните приведенные ниже действия, чтобы освободить все виртуальные машины в группе доступности, изменить их размер, а затем перезапустить.

Остановите все виртуальные машины в группе доступности.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Измените размер виртуальных машин в группе доступности и перезапустите их.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Дополнительная информация

Для повышения масштабируемости запустите несколько экземпляров виртуальных машин и выполните горизонтальное масштабирование. Дополнительные сведения см. [в статье Автоматическое масштабирование компьютеров Windows в масштабируемом наборе виртуальных машин](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

