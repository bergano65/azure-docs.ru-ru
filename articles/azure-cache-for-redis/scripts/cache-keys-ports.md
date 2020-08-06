---
title: Получение имени узла, портов и ключей кэша Redis для Azure — Azure CLI
description: В этом примере кода Azure CLI показано как получить имя узла, портов и ключей для экземпляра кэша Azure для Redis.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9fef834e550f144075ec9cc72cac3b11cc1e99e4
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504217"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Получение имени узла, портов и ключей для кэша Redis для Azure

В этом сценарии вы узнаете, как получить имя узла, порты и ключи, используемые для подключения к экземпляру кэша Redis для Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Описание скрипта

Чтобы получить имя узла, порты и ключи для экземпляра кэша Redis для Azure, в этом сценарии используются приведенные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Извлекает сведения об экземпляре кэша Redis для Azure. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis) | Извлекает ключи доступа для экземпляра кэша Redis для Azure. |


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов Azure CLI для кэша Azure для Redis см. в [документации по кэшу Azure для Redis](../cli-samples.md).
