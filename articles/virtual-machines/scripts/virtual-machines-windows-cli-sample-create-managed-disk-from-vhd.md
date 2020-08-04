---
title: Создание управляемого диска на основе VHD-файла в той же учетной записи (Windows). Пример для интерфейса командной строки
description: Пример сценария Azure CLI. Создание управляемого диска на основе VHD-файла в учетной записи хранения в той же подписке
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 34f05318840bcb4356de0aab8dd12f62ac79d345
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069257"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli-windows"></a>Создание управляемого диска на основе VHD-файла в учетной записи хранения в той же подписке с помощью интерфейса командной строки (Windows)

Этот сценарий создает управляемый диск на основе VHD-файла в учетной записи хранения в той же подписке. Этот сценарий можно использовать, чтобы импортировать специализированный (не универсальный и не подготовленный командой Sysprep) VHD в управляемый диск ОС для создания виртуальной машины. Кроме того, с его помощью можно импортировать виртуальный жесткий диск данных в управляемый диск данных.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды для создания управляемого диска на основе VHD-файла. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az disk create](/cli/azure/disk) | Создает управляемый диск на основе URI виртуального жесткого диска в учетной записи хранения в той же подписке. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры сценариев интерфейса командной строки для виртуальных машин и управляемых дисков см. в [документации по виртуальным машинам Azure под управлением Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
