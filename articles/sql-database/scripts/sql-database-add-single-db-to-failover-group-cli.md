---
title: Пример для CLI. Добавление отдельной базы данных в группу отработки отказа в службе "База данных SQL Azure"
description: Пример скрипта Azure CLI для создания отдельной базы данных в службе "База данных SQL Azure", добавления этой базы данных в группу отработки отказа и тестовой отработки отказа.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: bc238f08021bb9fb16b8c7319e63acebdfec3948
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061907"
---
# <a name="use-cli-to-add-an-azure-sql-database-into-a-failover-group"></a>Добавление базы данных в группу отработки отказа в службе "База данных SQL Azure" с помощью CLI

Этот пример скрипта Azure CLI позволяет создать отдельную базу данных и группу отработки отказа, добавить в нее базу данных и выполнить тестовую отработку отказа.

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Пример скрипта

### <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Выполнение скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to failover group")]

### <a name="clean-up-deployment"></a>Очистка развертывания

Используйте приведенную ниже команду, чтобы удалить группу ресурсов и все связанные с ней ресурсы.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Примеры

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| | |
|---|---|
| [az sql db](/cli/azure/sql/db) | Команды базы данных. |
| [az sql failover-group](/cli/azure/sql/failover-group) | Команды группы отработки отказа. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры сценариев интерфейса командной строки для Базы данных SQL Azure см. в [документации по Базе данных SQL](../sql-database-cli-samples.md).
