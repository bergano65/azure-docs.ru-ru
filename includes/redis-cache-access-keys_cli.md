---
title: включение файла
description: включение файла
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 778151e401624398b70101a242e4cf0be8e0a1b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485376"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Получение имени узла, портов и ключей доступа с помощью Azure CLI

Для получения имени узла и порты, с помощью Azure CLI, можно вызвать [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show)и для получения ключей, можно вызвать [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). Следующий скрипт вызывает эти две команды и выводит имя узла, порты и ключи на консоль.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Cache for Redis instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Cache for Redis instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Дополнительные сведения об этом сценарии см. в разделе [получения имени узла, портов и ключей для кэша Azure Redis](../articles/azure-cache-for-redis/scripts/cache-keys-ports.md). Дополнительные сведения об Azure CLI см. в разделе [установить Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) и [приступить к работе с Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
