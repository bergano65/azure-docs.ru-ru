---
title: Удаление кэша Azure для Redis — Azure CLI
description: В этом примере кода Azure CLI показано, как удалять Кэш Azure для Redis с помощью команды az redis delete.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: e5178ab4069cc5ffa8607c5feea6ffac86284a5b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411078"
---
# <a name="delete-an-azure-cache-for-redis"></a>Удаление кэша Azure для Redis

На примере этого скрипта вы узнаете, как удалить кэш Azure для Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

В этом скрипте используются следующие команды для удаления экземпляра кэша Azure для Redis. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az redis delete](https://docs.microsoft.com/cli/azure/redis) | Удаляет экземпляр кэша Azure для Redis. |


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов Azure CLI для кэша Azure для Redis см. в [документации по кэшу Azure для Redis](../cli-samples.md).
