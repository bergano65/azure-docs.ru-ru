---
title: Примеры для Azure CLI. Подключение и использование дисков данных
description: При помощи этого скрипта создается масштабируемый набор виртуальных машин, а также присоединяются и подготавливаются диски данных с Azure CLI.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6243899183bfbdf07aa9417d654e62b4b6cb548a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277670"
---
# <a name="attach-and-use-data-disks-with-a-virtual-machine-scale-set-with-the-azure-cli"></a>Подключение дисков данных к масштабируемому набору виртуальных машин и их использование с помощью Azure CLI
При помощи этого скрипта создается масштабируемый набор виртуальных машин, а также присоединяются и подготавливаются диски данных.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/use-data-disks/use-data-disks.sh "Create a virtual machine scale set with data disks")]

## <a name="clean-up-deployment"></a>Очистка развертывания
Выполните следующую команду, чтобы удалить группу ресурсов, масштабируемый набор и все связанные с ними ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта
Для создания группы ресурсов, масштабируемого набора виртуальных машин и всех связанных ресурсов в этом скрипте используются приведенные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az group create](/cli/azure/ad/group) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az vmss create](/cli/azure/vmss) | Создает масштабируемый набор виртуальных машин и подключает его к виртуальной сети, подсети и группе безопасности сети. Чтобы распределить трафик между несколькими экземплярами виртуальных машин, создается еще и подсистема балансировки нагрузки. Эта команда также указывает образ виртуальной машины, который будет использоваться, и учетные данные администратора.  |
| [az vmss disk attach](/cli/azure/vmss/disk) | Создание дисков данных и их присоединение к масштабируемому набору виртуальных машин. |
| [az vmss extension set](/cli/azure/vmss/extension) | Устанавливает расширение пользовательских скриптов Azure для выполнения сценария, который позволяет подготовить диски данных для каждого экземпляра виртуальной машины. |
| [az group delete](/cli/azure/ad/group) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Изучите дополнительные примеры сценариев Azure CLI в [документации по масштабируемым наборам виртуальных машин Azure](../cli-samples.md).
