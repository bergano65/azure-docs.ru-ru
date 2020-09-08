---
title: Пример для PowerShell. Создание управляемого диска на основе VHD-файла в учетной записи хранения в той же подписке
description: Пример сценария Azure PowerShell для создания управляемого диска из VHD-файла в учетной записи хранения в той же или другой подписке.
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.topic: sample
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 1d743d840afc4505cdc39b258b21856b6c3e7aa9
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322756"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Создание управляемого диска из VHD-файла в учетной записи хранения в той же или другой подписке с помощью PowerShell

Этот сценарий создает управляемый диск на основе VHD-файла в учетной записи хранения в той же или другой подписке. Этот сценарий можно использовать, чтобы импортировать специализированный (не универсальный и не подготовленный командой Sysprep) VHD в управляемый диск ОС для создания виртуальной машины. Кроме того, с его помощью можно импортировать VHD данных в управляемый диск данных. 

Не создавайте несколько идентичных управляемых дисков из VHD-файла за небольшой промежуток времени. При создании управляемых дисков из VHD-файла создается моментальный снимок большого двоичного объекта VHD-файла, который затем используется для этой операции. За минуту может быть создан только один большой двоичный объект, что приводит к сбоям при создании дисков из-за регулирования. Во избежание этого регулирования создайте [управляемый моментальный снимок из VHD-файла](virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json), а затем используйте его для создания нескольких управляемых дисков за короткий промежуток времени. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания управляемого диска на основе VHD. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Создает конфигурацию диска, которая используется для создания диска. Она содержит тип хранилища, расположение, идентификатор ресурса учетной записи хранения, в которой хранится родительский VHD, и универсальный код ресурса (URI) родительского VHD. |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Создает диск с помощью конфигурации диска, имени диска и имени группы ресурсов, которые передаются в качестве параметров. |

## <a name="next-steps"></a>Дальнейшие действия

[Создание виртуальной машины путем подключения управляемого диска как диска ОС](virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md)

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
