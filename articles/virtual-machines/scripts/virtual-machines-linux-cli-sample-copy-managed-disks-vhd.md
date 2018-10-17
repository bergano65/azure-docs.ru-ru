---
title: Пример скрипта Azure CLI — экспорт и копирование базового VHD управляемых дисков в учетную запись хранения | Документация Майкрософт
description: Пример скрипта Azure CLI — экспорт и копирование базового VHD управляемых дисков в учетную запись хранения
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: c5f06a8c8fb707a2bf0451f8e9ed391ac0c5bad9
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48045260"
---
# <a name="exportcopy-the-underlying-vhd-of-a-managed-disk-to-a-storage-account-with-cli"></a>Экспорт и копирование базового VHD управляемого диска в учетную запись хранения с помощью CLI

Этот скрипт позволяет экспортировать базовый VHD управляемого диска в учетную запись хранения в том же или другом регионе. Сначала для управляемого диска создается URI SAS. Затем с его помощью VHD копируется в учетную запись хранения. Этот скрипт также позволяет копировать управляемые диски в различных регионах. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.sh "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует приведенные ниже команды для создания универсального кода ресурса (URI) SAS управляемого диска. Затем базовый VHD копируется в учетную запись хранения с помощью созданного URI SAS. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az disk grant-access](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-grant-access) | Создает SAS только для чтения, который используется для копирования базового VHD-файла в учетную запись хранения или загрузки в локальную среду  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy#az_storage_blob_copy_start) | Асинхронно копирует большой двоичный объект из одной учетной записи хранения в другую. |

## <a name="next-steps"></a>Дополнительная информация

[Создание управляемого диска на основе VHD](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Создание виртуальной машины на основе управляемого диска](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры сценариев интерфейса командной строки для виртуальных машин и управляемых дисков см. в [документации по виртуальным машинам Azure под управлением Linux](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
