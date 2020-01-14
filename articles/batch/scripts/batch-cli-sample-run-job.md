---
title: Пример сценария Azure CLI. Выполнение пакетного задания
description: Этот сценарий создает задание пакетной службы и добавляет в него ряд задач. Он также демонстрирует, как отслеживать задание и его задачи.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: tysonn
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2019
ms.author: lahugh
ms.openlocfilehash: 3fff556f91558f30389ac3548d699e3788719d59
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75390035"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Пример использования CLI. Выполнение задания и задач с использованием пакетной службы Azure

Этот сценарий создает задание пакетной службы и добавляет в него ряд задач. Он также демонстрирует, как отслеживать задание и его задачи. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.20 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>Очистка развертывания

Выполните следующую команду, чтобы удалить группу ресурсов и все связанные с ней ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Создает учетную запись пакетной службы. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Выполняет проверку подлинности с помощью указанной учетной записи пакетной службы для дальнейшего взаимодействия с интерфейсом командной строки.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-create) | Создает пул вычислительных узлов.  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#az-batch-job-create) | Создает задание пакетной службы.  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#az-batch-task-create) | Добавляет задачу в указанное задание пакетной службы.  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#az-batch-job-set) | Обновляет свойства задания пакетной службы.  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#az-batch-job-show) | Получает сведения об указанном задании пакетной службы.  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#az-batch-task-show) | Получает сведения о задаче из указанного задания пакетной службы.  |
| [az group delete](/cli/azure/group#az-group-delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).