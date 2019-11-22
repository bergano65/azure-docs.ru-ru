---
title: Пример Azure CLI. Копирование моментального снимка в учетную запись хранения в другом регионе
description: Пример сценария Azure CLI. Экспорт или копирование моментального снимка в виде VHD в учетную запись хранения в тот же или другой регион.
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
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: e9390d6854ecfa43542d1713bae7d7e8fd8439ee
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038259"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>Экспорт или копирование моментальных снимков в учетную запись хранения в другой регион с помощью интерфейса командной строки

Этот сценарий экспортирует управляемый моментальный снимок в учетную запись хранения в другом регионе. Сначала он создает URI SAS для моментального снимка, а затем использует его для копирования в учетную запись хранения в другом регионе. Этот сценарий можно использовать для обеспечения резервной копии управляемых дисков в другом регионе в целях аварийного восстановления. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий выполняет приведенные ниже команды для создания универсального кода ресурса (URI) SAS для управляемого моментального снимка и копирует моментальный снимок в учетную запись хранения, используя созданный универсальный код ресурса (URI) SAS. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot) | Создает SAS только для чтения, который используется для копирования базового VHD-файла в учетную запись хранения или загрузки в локальную среду.  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | Асинхронно копирует большой двоичный объект из одной учетной записи хранения в другую. |

## <a name="next-steps"></a>Дополнительная информация

[Создание управляемого диска на основе VHD](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Создание виртуальной машины на основе управляемого диска](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры сценариев интерфейса командной строки для виртуальных машин и управляемых дисков см. в [документации по виртуальным машинам Azure под управлением Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
