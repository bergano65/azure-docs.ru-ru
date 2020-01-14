---
title: Пример для PowerShell. Создание виртуальной машины на основе моментального снимка
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
ms.openlocfilehash: fb10f6c2d8109d240840faf5fa864176c89f24e1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75368329"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell"></a>Создание виртуальной машины из моментального снимка с помощью PowerShell

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
| [Get-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/get-azsnapshot) | Возвращает моментальный снимок на основе его имени. |
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig) | Создает конфигурацию диска. Эта конфигурация используется при создании диска. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk) | Создает управляемый диск. |
| [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) | Создает конфигурацию виртуальной машины. Эта конфигурация включает в себя такие сведения, как имя виртуальной машины, операционную систему и учетные данные администратора. Данная конфигурации используется при создании виртуальной машины. |
| [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) | Присоединяет управляемый диск в качестве диска ОС к виртуальной машине. |
| [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) | Создает общедоступный IP-адрес. |
| [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) | Создает сетевой интерфейс. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Создает виртуальную машину. |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).