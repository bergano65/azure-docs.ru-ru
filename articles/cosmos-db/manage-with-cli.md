---
title: Управление ресурсами Azure Cosmos DB с помощью Azure CLI
description: Управляйте учетной записью, базой данных и контейнерами Azure Cosmos DB с помощью службы Azure CLI.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: f9d8bf9161343e4b36a3c16209873962b69d8af5
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615207"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Управление ресурсами Azure Cosmos с помощью Azure CLI

В следующем разделе описываются стандартные команды для автоматизации Azure Cosmos DB управления учетными записями, базами данных и контейнерами с помощью Azure CLI. Страницы справки для всех команд интерфейса командной строки Azure Cosmos DB доступны в [справочнике по Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). Вы также можете найти несколько примеров в [Экземплярах Azure CLI для Azure Cosmos DB](cli-samples.md), в том числе и сведения о том, как создавать и управлять учетными записями, базами данных и контейнерами Cosmos DB для MongoDB, Gremlin, Cassandra и Table API.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>создание учетной записи Azure Cosmos DB;

Чтобы создать учетную запись Azure Cosmos DB с помощью API SQL, согласованность сеансов в восточной части США и западной части США, выполните следующую команду:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount # must be lowercase and < 31 characters \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=EastUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=WestUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Имя учетной записи Azure Cosmos должно быть в нижнем регистре.

## <a name="create-a-database"></a>Создание базы данных

Чтобы создать базу данных Cosmos, выполните следующую команду:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Создать контейнер

Чтобы создать контейнер Cosmos с единицами запросов в секунду (400) и ключом секции, выполните следующую команду:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --partition-key-path /myPartitionKey \
   --throughput 400
```

## <a name="change-the-throughput-of-a-container"></a>Изменение пропускной способности контейнера

Чтобы изменить пропускную способность контейнера Cosmos на 1000 единиц запросов в секунду, выполните следующую команду:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>Вывод ключей учетной записи

Чтобы получить ключи для учетной записи Cosmos, выполните следующую команду:

```azurecli-interactive
# List account keys
az cosmosdb keys list \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Вывод строк подключения

Чтобы получить строки подключения для учетной записи Cosmos, выполните следующую команду:

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Повторное создание ключей учетных записей

Чтобы повторно создать новый первичный ключ для учетной записи Cosmos, выполните следующую команду:

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения об Azure CLI см.:

- [Установка Azure CLI](/cli/azure/install-azure-cli)
- [Справочник по интерфейсу командной строки Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Дополнительные примеры Azure CLI для Azure Cosmos DB](cli-samples.md)
