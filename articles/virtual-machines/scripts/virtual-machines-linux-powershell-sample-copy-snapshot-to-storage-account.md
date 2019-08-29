---
title: Пример сценария Azure PowerShell. Экспорт или копирование моментального снимка в виде VHD в учетную запись хранения в другом регионе | Документация Майкрософт
description: Пример сценария Azure PowerShell для экспорта или копирования моментального снимка в виде VHD в учетную запись хранения в другом регионе.
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: e2245e2f7c457195605755d31d0f0d4f1d0e72e5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091157"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Экспорт или копирование управляемых моментальных снимков в виде VHD в учетную запись хранения в другом регионе с помощью PowerShell

Этот сценарий экспортирует управляемый моментальный снимок в учетную запись хранения в другом регионе. Сначала он создает URI SAS для моментального снимка, а затем использует его для копирования в учетную запись хранения в другом регионе. Этот сценарий можно использовать для обеспечения резервной копии управляемых дисков в другом регионе в целях аварийного восстановления.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий выполняет приведенные ниже команды для создания универсального кода ресурса (URI) SAS для управляемого моментального снимка и копирует моментальный снимок в учетную запись хранения, используя созданный универсальный код ресурса (URI) SAS. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [Grant-AzSnapshotAccess](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Создает универсальный код ресурса (URI) SAS для моментального снимка, который используется для копирования моментального снимка в учетную запись хранения. |
| [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/New-AzureStorageContext) | Создает контекст учетной записи хранения с помощью имени учетной записи и ключа. Этот контекст можно использовать для выполнения операций чтения и записи с учетной записью хранения. |
| [Start-AzureStorageBlobCopy](https://docs.microsoft.com/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Копирует базовый VHD моментального снимка в учетную запись хранения. |

## <a name="next-steps"></a>Дополнительная информация

[Создание управляемого диска на основе VHD](virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Создание виртуальной машины на основе управляемого диска](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Linux](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).