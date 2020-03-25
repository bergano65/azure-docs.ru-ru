---
title: Создание и проверка виртуальной машины в лаборатории с помощью Azure CLI
description: Этот скрипт Azure CLI предназначен для создания и проверки доступности виртуальной машины в лаборатории.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2020
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 767d1f3a504e91783e37d8ff1c1b97f62816af3b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76169264"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Создание и проверка доступности виртуальной машины в лаборатории в Azure DevTest Labs с помощью Azure CLI

Этот скрипт Azure CLI предназначен для создания виртуальной машины (VM) в лаборатории. Виртуальная машина создается на основе образа Marketplace с использованием аутентификации SSH. Затем с помощью этого скрипта проверяется доступность VM для использования. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды:

| Get-Help | Примечания |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az lab vm create](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-create) | Создание виртуальной машины в лаборатории. |
| [az lab vm show](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-show) | Отображение сведений о состоянии VM в лаборатории. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

См. [дополнительные примеры скриптов CLI для Служб лабораторий Azure](../samples-cli.md).
