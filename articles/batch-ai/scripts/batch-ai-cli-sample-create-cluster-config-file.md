---
title: Пример скрипта Azure CLI. Создание кластера Batch AI с помощью файла конфигурации | Документация Майкрософт
description: Пример скрипта Azure CLI. Создание кластера Batch AI путем указания параметров конфигурации в JSON-файле.
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
ms.date: 08/16/2018
ms.author: danlep
ms.openlocfilehash: 01281c0328fab85814ff93c73f9ea25e0d4c1b08
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232806"
---
# <a name="cli-example-create-a-batch-ai-cluster-using-a-cluster-configuration-file"></a>Пример использования CLI. Создание кластера Batch AI с помощью файла конфигурации кластера

Этот скрипт демонстрирует использование JSON-файла конфигурации для настройки кластера Batch AI. Эти параметры можно использовать вместо соответствующих параметров командной строки для `az batchai cluster create`. Файл конфигурации удобно использовать, если вам нужно подключить несколько файловых систем на узлах кластера или использовать идентичную конфигурацию в нескольких кластерах.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы хотите установить и использовать CLI на локальном компьютере, для этого руководства необходимо использовать Azure CLI версии 2.0.38 или более поздней. Запустите `az --version`, чтобы определить версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-config-file.sh "Create Batch AI cluster - configuration file")]

## <a name="clean-up-deployment"></a>Очистка развертывания

Выполните следующие команды, чтобы удалить группы ресурсов и все связанные с ними ресурсы.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Создание учетной записи хранения. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Создает общую папку в учетной записи хранения. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Создает рабочую область Batch AI. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Создает кластер Batch AI. |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | Отображает сведения о кластере Batch AI. |
| [az group delete](/cli/azure/group#az-group-delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).
