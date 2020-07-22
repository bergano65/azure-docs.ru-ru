---
title: Пример для CLI. Копирование управляемых дисков в подписку
description: Пример сценария Azure CLI. Копирование (или перемещение) управляемых дисков в ту же или другую подписку
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: b665e1203976180688b69e5f3175c4be8b9c95ce
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501642"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Копирование управляемых дисков в ту же или другую подписку с помощью интерфейса командной строки

Этот сценарий копирует управляемый диск в ту же или другую подписку в одном регионе. Копирование работает только в том случае, если подписки входят в состав одного клиента AAD.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания управляемого диска в целевой подписке с помощью идентификатора исходного управляемого диска. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az disk show](/cli/azure/disk) | Получает все свойства управляемого диска, используя имя и свойства группы ресурсов управляемого диска. Свойство идентификатора используется для копирования управляемого диска в другую подписку.  |
| [az disk create](/cli/azure/disk) | Копирует управляемый диск путем создания нового управляемого диска в другой подписке, используя идентификатор и имя родительского управляемого диска.  |

## <a name="next-steps"></a>Дальнейшие действия

[Создание виртуальной машины на основе управляемого диска](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры сценариев интерфейса командной строки для виртуальных машин и управляемых дисков см. в [документации по виртуальным машинам Azure под управлением Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
