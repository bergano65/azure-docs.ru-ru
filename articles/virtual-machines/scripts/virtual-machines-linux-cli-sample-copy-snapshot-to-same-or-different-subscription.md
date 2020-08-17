---
title: Пример для CLI. Копирование моментального снимка управляемых дисков в подписку на виртуальной машине Linux
description: Пример сценария Azure CLI. Копирование (или перемещение) моментального снимка управляемого диска в ту же или другую подписку с помощью CLI на виртуальной машине Linux
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
ms.openlocfilehash: 637c8c3a2f6ba90a7a16fa375d99a7463be71270
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056097"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli-on-a-linux-vm"></a>Копирование моментального снимка управляемого диска в ту же или другую подписку с помощью интерфейса командной строки на виртуальную машину Linux

Этот сценарий копирует моментальный снимок управляемого диска в ту же или другую подписку. Используйте этот скрипт в следующих сценариях:

1. чтобы перенести моментальный снимок из хранилища класса Premium (Premium_LRS) в стандартное хранилище (Standard_LRS или Standard_ZRS) для сокращения расходов;
1. чтобы перенести моментальный снимок из локально избыточного хранилища (Premium_LRS, Standard_LRS) в хранилище, избыточное между зонами (Standard_ZRS), для повышения его надежности;
1. чтобы переместить моментальный снимок в другую подписку в том же регионе на долгосрочное хранение.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания моментального снимка в целевой подписке с помощью идентификатора исходного моментального снимка. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | Получает все свойства моментального снимка, используя имя и свойства группы ресурсов моментального снимка. Свойство идентификатора используется для копирования моментального снимка в другую подписку.  |
| [az snapshot create](/cli/azure/snapshot) | Копирует моментальный снимок путем создания моментального снимка в другой подписке с помощью идентификатора и имени родительского снимка.  |

## <a name="next-steps"></a>Дальнейшие действия

[Создание виртуальной машины на основе моментального снимка](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры сценариев интерфейса командной строки для виртуальных машин и управляемых дисков см. в [документации по виртуальным машинам Azure под управлением Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
