---
title: Пример для PowerShell. Создание виртуальной машины на основе моментального снимка (Windows)
description: Создание виртуальной машины из моментального снимка с помощью сценария Azure PowerShell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 8ec7b8655a499057e91f138411529a5f271ea511
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082364"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell-windows"></a>Создание виртуальной машины из моментального снимка с помощью PowerShell (Windows)

Этот скрипт создает виртуальную машину из моментального снимка диска ОС. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from managed os disk")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для получения свойств моментального снимка, создания управляемого диска из моментального снимка и создания виртуальной машины этот сценарий использует указанные ниже команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Примечания |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Возвращает моментальный снимок на основе его имени. |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Создает конфигурацию диска. Эта конфигурация используется при создании диска. |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Создает управляемый диск. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Создает конфигурацию виртуальной машины. Эта конфигурация включает в себя такие сведения, как имя виртуальной машины, операционную систему и учетные данные администратора. Данная конфигурации используется при создании виртуальной машины. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | Присоединяет управляемый диск в качестве диска ОС к виртуальной машине. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Создает сетевой интерфейс. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Создает виртуальную машину. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
