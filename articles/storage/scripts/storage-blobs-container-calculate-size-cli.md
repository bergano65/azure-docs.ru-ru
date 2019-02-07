---
title: Пример скрипта Azure CLI. Вычисление размера контейнера большого двоичного объекта | Документация Майкрософт
description: Сведения о вычислении размера контейнера в хранилище BLOB-объектов Azure за счет сложения размеров больших двоичных объектов, хранящихся в нем.
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/28/2017
ms.author: tamram
ms.openlocfilehash: 3cb1e35617a58fcde7968ab45d437d865c91f983
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696945"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Вычисление размера контейнера в хранилище BLOB-объектов

Этот скрипт позволяет вычислить размер контейнера в хранилище BLOB-объектов Azure за счет сложения размеров больших двоичных объектов, хранящихся в нем.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Этот скрипт CLI предоставляет прогнозируемый размер контейнера и не должен использоваться для вычислений при выставлении счетов.

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните команду ниже, чтобы удалить группу ресурсов, контейнер и все связанные ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Ниже приведены команды, на основе которых скрипт вычисляет размер контейнера в хранилище BLOB-объектов. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Команда | Примечания |
|---|---|
| [az group create](/cli/azure/group) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage blob upload](/cli/azure/storage/account) | Отправляет локальные файлы в контейнер хранилища BLOB-объектов Azure. |
| [az storage blob list](/cli/azure/storage/account/keys) | Возвращает список больших двоичных объектов в контейнере хранилища BLOB-объектов Azure. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов CLI хранилища см. в статье [Примеры скриптов Azure CLI для хранилища BLOB-объектов Azure](../blobs/storage-samples-blobs-cli.md).
