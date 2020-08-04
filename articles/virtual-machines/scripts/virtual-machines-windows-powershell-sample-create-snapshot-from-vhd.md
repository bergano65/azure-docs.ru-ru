---
title: Создание нескольких идентичных управляемых дисков (Windows) на основе моментального снимка VHD с помощью PowerShell
description: Пример сценария Azure PowerShell для создания моментального снимка из VHD для быстрого создания нескольких идентичных управляемых дисков.
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
ms.openlocfilehash: 0625c968a3c60d38ca2bbe2f13318ccd85d61a61
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082381"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell-windows"></a>Создание моментального снимка из VHD для быстрого создания нескольких идентичных управляемых дисков с помощью PowerShell (Windows)

Этот сценарий создает моментальный снимок из VHD-файла в учетной записи хранения в той же или другой подписке. Этот сценарий можно использовать, чтобы импортировать специализированный (не универсальный и не подготовленный командой Sysprep) VHD в моментальный снимок, затем с помощью этого моментального снимка создать несколько идентичных управляемых дисков за короткий промежуток времени. Кроме того, с его помощью можно импортировать VHD данных в моментальный снимок, а затем использовать этот моментальный снимок для быстрого создания нескольких управляемых дисков. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="next-steps"></a>Дальнейшие действия

[Создание управляемого диска из моментального снимка](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Создание виртуальной машины путем подключения управляемого диска как диска ОС](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
