---
title: Пример для CLI. Мониторинг и масштабирование отдельной базы данных SQL Azure
description: Пример сценария Azure CLI для отслеживания и масштабирования отдельной базы данных SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 191de1fdbbee3e31bfcd366cbec8a70732b23b5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061814"
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Мониторинг и масштабирование отдельной базы данных SQL с помощью интерфейса командной строки

Этот пример сценария Azure CLI масштабирует отдельную базу данных SQL Azure до другого объема вычислительных ресурсов после запроса на получение сведений о размере базы данных.

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Пример скрипта

### <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Выполнение скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

> [!TIP]
> Список операций, выполняемых в базе данных, можно получить с помощью команды [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list). Чтобы отменить операцию обновления в базе данных, воспользуйтесь командой [az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel).

### <a name="clean-up-deployment"></a>Очистка развертывания

Используйте приведенную ниже команду, чтобы удалить группу ресурсов и все связанные с ней ресурсы.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Примеры

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| | |
|---|---|
| [az sql server](/cli/azure/sql/server) | Команды сервера. |
| [az sql db show-usage](/cli/azure/sql#az-sql-show-usage) | Отображает сведения об используемом размере отдельной базы данных или базы данных в пуле. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры сценариев интерфейса командной строки для Базы данных SQL Azure см. в [документации по Базе данных SQL](../sql-database-cli-samples.md).
