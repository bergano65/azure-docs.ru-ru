---
title: Пример для PowerShell. Создание управляемого диска на основе моментального снимка (Windows)
description: Пример сценария Azure PowerShell для создания управляемого диска из моментального снимка.
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: c3b429e9aab26313ace9dbc3672b08d03ec78c15
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088620"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell-windows"></a>Создание управляемого диска из моментального снимка с помощью PowerShell (Windows)

Этот сценарий создает управляемый диск на основе моментального снимка. Его можно использовать для восстановления виртуальной машины из моментальных снимков ОС и дисков данных. Можно создать управляемые диски данных и ОС на основе соответствующих моментальных снимков, а затем создать виртуальную машину, подключив эти управляемые диски. Кроме того, вы можете восстановить диски данных существующей виртуальной машины, подключив диски данных, созданные из моментальных снимков.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания управляемого диска на основе моментального снимка. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Возвращает свойства моментального снимка.  |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Создает конфигурацию диска, которая используется для создания диска. Она содержит идентификатор ресурса родительского моментального снимка, расположение, которое совпадает с расположением родительского моментального снимка, и тип хранилища.  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Создает диск с помощью конфигурации диска, имени диска и имени группы ресурсов, которые передаются в качестве параметров. |


## <a name="next-steps"></a>Дальнейшие действия

[Создание виртуальной машины на основе управляемого диска](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
