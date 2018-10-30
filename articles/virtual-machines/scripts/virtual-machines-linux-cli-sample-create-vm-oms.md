---
title: Пример скрипта Azure CLI. Создание виртуальной машины Linux с использованием мониторинга Azure | Документация Майкрософт
description: Пример скрипта Azure CLI. Создание виртуальной машины Linux с использованием мониторинга Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: cedb8d9c2ef4acd9d7a896153bce6be790ed83a3
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49407974"
---
# <a name="monitor-a-vm-with-azure-monitoring"></a>Мониторинг виртуальной машины с помощью службы мониторинга Azure

Этот скрипт создает виртуальную машину Azure, устанавливает агент Log Analytics и регистрирует систему в рабочей области Log Analytics. После выполнения скрипта виртуальная машина отобразится в консоли.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-monitor-oms/create-vm-monitor-oms.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Очистка развертывания

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Создает виртуальную машину и подключает ее к сетевой карте, виртуальной сети, подсети и группе безопасности сети. Эта команда также указывает образ виртуальной машины, который будет использоваться, и учетные данные администратора.  |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Выполняет расширение виртуальной машины на виртуальной машине. В этом случае расширение для агента Log Analytics используется, чтобы установить агент Log Analytics и зарегистрировать виртуальную машину в рабочей области Log Analytics. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
