---
title: Пример сценария Azure CLI. Выполнение пакетного задания
description: Этот сценарий создает задание пакетной службы и добавляет в него ряд задач. Он также демонстрирует, как отслеживать задание и его задачи.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: d8efce1bc8dc32070b770769104b7fdc38e683cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500237"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Пример CLI. Выполнение задания и задач с использованием пакетной службы Azure

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
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Создает пул вычислительных узлов.  |
| [az batch job create](/cli/azure/batch/job#az-batch-job-create) | Создает задание пакетной службы.  |
| [az batch task create](/cli/azure/batch/task#az-batch-task-create) | Добавляет задачу в указанное задание пакетной службы.  |
| [az batch job set](/cli/azure/batch/job#az-batch-job-set) | Обновляет свойства задания пакетной службы.  |
| [az batch job show](/cli/azure/batch/job#az-batch-job-show) | Получает сведения об указанном задании пакетной службы.  |
| [az batch task show](/cli/azure/batch/task#az-batch-task-show) | Получает сведения о задаче из указанного задания пакетной службы.  |
| [az group delete](/cli/azure/group#az-group-delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).
