---
title: Создание виртуальной машины путем подключения управляемого диска в качестве диска ОС с помощью PowerShell
description: Пример скрипта Azure PowerShell. Создание виртуальной машины путем подключения управляемого диска в качестве диска ОС
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 038ec433a4f27eec5ace537ff9b08e5ff6c2f6a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89322786"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>Создание виртуальной машины с помощью существующего управляемого диска ОС с использованием PowerShell 

Этот скрипт создает виртуальную машину путем подключения существующего управляемого диска как диска ОС. Используйте этот скрипт в таких сценариях:
* создание виртуальной машины из существующего управляемого диска ОС, скопированного из управляемого диска в другой подписке;
* создание виртуальной машины из существующего управляемого диска, который был создан из специального файла VHD; 
* создание виртуальной машины из существующего управляемого диска ОС, который был создан из моментального снимка. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды для получения свойств управляемого диска, его подключения к новой виртуальной машине и создания виртуальной машины. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Примечания |
|---|---|
| [Get-AzDisk](/powershell/module/az.compute/get-azdisk) | Возвращает объект диска на основе имени и группы ресурсов диска. Свойство идентификатора возвращенного объекта диска используется для подключения диска к новой виртуальной машине. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Создает конфигурацию виртуальной машины. Эта конфигурация включает в себя такие сведения, как имя виртуальной машины, операционную систему и учетные данные администратора. Данная конфигурации используется при создании виртуальной машины. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | Подключает управляемый диск, используя свойство идентификатора диска как диск ОС для новой виртуальной машины. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Создает сетевой интерфейс. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Создайте виртуальную машину. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

Для образов из marketplace используйте [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan), чтобы задать сведения о плане.

```powershell
Set-AzVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
