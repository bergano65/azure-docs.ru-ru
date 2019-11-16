---
title: Пример скрипта Azure CLI. Получение сведений о кэше Redis для Azure
description: Пример скрипта Azure CLI. Получение сведений о кэше Redis для Azure
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: f3e6c6dab95722eebdc4a175379444ef5840cad1
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122483"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Получение сведений о кэше Redis для Azure

В этом сценарии описывается, как получить сведения об экземпляре кэша Redis для Azure, включая его состояние подготовки.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Описание скрипта

Для получения сведений об экземпляре кэша Redis для Azure в этом сценарии используются приведенные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Извлекает сведения об экземпляре кэша Redis для Azure. |


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов Azure CLI для кэша Azure для Redis см. в [документации по кэшу Azure для Redis](../cli-samples.md).
