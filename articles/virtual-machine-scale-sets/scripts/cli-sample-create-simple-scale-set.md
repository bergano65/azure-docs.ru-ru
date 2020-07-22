---
title: Примеры Azure CLI. Создание масштабируемого набора виртуальных машин
description: При помощи этого скрипта создается масштабируемый набор виртуальных машин Azure с операционной системой Ubuntu, а также связанные сетевые ресурсы, включая подсистему балансировки нагрузки.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 1456301f1cc1da33835206e10dc1550ed8fcbbf2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86503070"
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Создание масштабируемого набора виртуальных машин с помощью Azure CLI
При помощи этого скрипта создается масштабируемый набор виртуальных машин Azure с операционной системой Ubuntu, а также связанные сетевые ресурсы, включая подсистему балансировки нагрузки. После выполнения сценария можно получить доступ к экземплярам виртуальных машин по протоколу SSH.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/simple-scale-set/simple-scale-set.sh "Create a simple virtual machine scale set")]

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
| [az group delete](/cli/azure/ad/group) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure/overview).
