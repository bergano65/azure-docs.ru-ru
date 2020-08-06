---
title: Пример сценария Azure CLI. Перезапуск виртуальных машин
description: Пример сценария Azure CLI. Перезапуск виртуальных машин с использованием тега и идентификатора
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 03a1e44ee3bdaa168fb3eb17078bfecb1f45c443
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479495"
---
# <a name="restart-vms"></a>Перезапуск виртуальных машин

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

В этом примере показано несколько способов получить несколько виртуальных машин и перезапустить их.

Первый пример перезапускает все виртуальные машины в группе ресурсов.

```azurecli
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

Второй возвращает виртуальные машины с тегами с помощью команды `az resource list`, применяет фильтр, оставляя только те ресурсы, которые являются виртуальными машинами, и перезапускает их.

```azurecli
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

Этот пример работает в оболочке Bash. Сведения о вариантах выполнения скриптов Azure CLI в клиенте Windows см. в статье [Установка Azure CLI 2.0 в Windows](/cli/azure/install-azure-cli-windows).


## <a name="sample-script"></a>Пример скрипта

Пример состоит из трех сценариев.
Первый подготавливает виртуальные машины.
В нем используется параметр no-wait, поэтому команда возвращается без ожидания подготовки каждой виртуальной машины.
Второй ожидает, пока виртуальные машины не будут полностью подготовлены.
Третий сценарий перезапускает все виртуальные машины, которые были подготовлены, и затем — только виртуальные машины с тегами.

### <a name="provision-the-vms"></a>Подготовка виртуальных машин

Этот сценарий создает группу ресурсов и создает три виртуальные машины, которые следует перезапустить.
Две из них имеют теги.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Provision the VMs")]

### <a name="wait"></a>Ожидание

Этот сценарий проверяет состояние подготовки каждые 20 секунд, пока все три виртуальные машины не будут подготовлены или подготовка одной из них не завершится сбоем.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Wait for the VMs to be provisioned")]

### <a name="restart-the-vms"></a>Перезапуск виртуальных машин

Этот сценарий перезапускает все виртуальные машины в группе ресурсов, а затем перезапускает только виртуальные машины с тегами.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Restart VMs by tag")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

После выполнения примера сценария можно удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив следующую команду:

```azurecli-interactive
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины, группы доступности, балансировщика нагрузки и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az group create](/cli/azure/group) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az vm create](/cli/azure/vm/availability-set) | Создает виртуальные машины.  |
| [az vm list](/cli/azure/vm) | Используется с `--query`, чтобы обеспечить подготовку виртуальных машин, прежде чем они будут перезапущены, а затем — чтобы получить идентификаторы виртуальных машин для их перезапуска. |
| [az resource list](/cli/azure/vm) | Используется с `--query` для получения идентификаторов виртуальных машин по тегу. |
| [az vm restart](/cli/azure/vm) | Перезагружает виртуальные машины. |
| [az group delete](/cli/azure/vm/extension) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
