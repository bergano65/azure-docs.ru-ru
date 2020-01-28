---
title: Остановка работы и удаление виртуальной машины в лаборатории с помощью Azure CLI
description: Этот скрипт Azure CLI останавливает и удаляет виртуальную машину в лаборатории.
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
ms.openlocfilehash: 7465770eaeb35772012be2219858ccda512775d6
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166413"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Остановка и удаление виртуальной машины в лаборатории в Azure DevTest Labs с помощью Azure CLI

Этот скрипт Azure CLI останавливает и удаляет виртуальную машину в лаборатории. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды:

| Get-Help | Примечания |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | Остановка виртуальной машины в лаборатории. Эта операция может занять некоторое время. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | Удаление виртуальной машины в лаборатории. Эта операция может занять некоторое время. |


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

См. [дополнительные примеры скриптов CLI для Служб лабораторий Azure](../samples-cli.md).
