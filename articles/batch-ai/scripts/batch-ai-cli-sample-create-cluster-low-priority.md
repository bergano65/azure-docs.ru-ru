---
title: Пример скрипта Azure CLI — создание кластера Batch AI с низким приоритетом | Документация Майкрософт
description: Пример скрипта Azure CLI — создание кластера узлов (виртуальных машин) Batch AI с низким приоритетом и управление им
services: batch-ai
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.devlang: azurecli
ms.topic: sample
ms.tgt-pltfrm: multiple
ms.workload: na
ms.date: 07/26/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: e1a37104a5dc6e89b147c8bb9e14b4eda36d8eef
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697861"
---
# <a name="cli-example-create-and-manage-a-batch-ai-cluster-of-low-priority-nodes"></a>Пример использования CLI. Создание кластера узлов Batch AI с низким приоритетом и управление им

[!INCLUDE [batch-ai-retiring](../../../includes/batch-ai-retiring.md)]

Этот скрипт демонстрирует некоторые из команд Azure CLI для создания кластера Batch AI, состоящего из узлов (виртуальных машин) с низким приоритетом, и управления этим кластером.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI на локальном компьютере, для выполнения инструкций из этого руководства вам потребуется Azure CLI версии 2.0.38 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-low-priority.sh "Create Batch AI cluster - low-priority nodes")]

## <a name="clean-up-deployment"></a>Очистка развертывания

Выполните следующие команды, чтобы удалить группы ресурсов и все связанные с ними ресурсы.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup

# Remove resource group for the auto-storage account.
az group delete --name batchaiautostorage
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Создает рабочую область Batch AI. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Создает кластер Batch AI. |
| [az batchai cluster show](/cli/azure/batchai/cluster) | Отображает сведения о кластере Batch AI. |
| [az batchai cluster node list](/cli/azure/batchai/cluster/node) | Вывод списка узлов кластера Batch AI. |
| [az batchai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) | Изменяет размер кластера Batch AI.  |
| [az group delete](/cli/azure/group#az-group-delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).
