---
title: Управление ресурсами Azure Cosmos DB с помощью Azure CLI
description: Управляйте учетной записью, базой данных и контейнерами Azure Cosmos DB с помощью службы Azure CLI.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 144515fef9da714ab80f15bb39757ed2283c6dd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243375"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Управление ресурсами Azure Cosmos с помощью Azure CLI

В этом руководстве содержатся общие команды для автоматизации управления учетными записями Azure Cosmos DB, баз данных и контейнеров с помощью Azure CLI. Страницы справки для всех команд интерфейса командной строки Azure Cosmos DB доступны в [справочнике по Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). Вы также можете найти несколько примеров в [Экземплярах Azure CLI для Azure Cosmos DB](cli-samples.md), в том числе и сведения о том, как создавать и управлять учетными записями, базами данных и контейнерами Cosmos DB для MongoDB, Gremlin, Cassandra и Table API.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>создание учетной записи Azure Cosmos DB;

Чтобы создать учетную запись Azure Cosmos DB с SQL API, согласованность уровня сеанса, в восточной части США и Западная часть США, выполните следующую команду:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations EastUS=0 WestUS=1 \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Имя учетной записи Azure Cosmos должны быть строчными.

## <a name="create-a-database"></a>Создание базы данных

Чтобы создать базу данных Cosmos DB, выполните следующую команду:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Создание контейнера

Чтобы создать контейнер Cosmos DB с ЕЗ/с, 400 и ключ секции, выполните следующую команду:

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

Чтобы изменить 1000 ЕЗ/с пропускной способности контейнера Cosmos DB, выполните следующую команду:

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
az cosmosdb list-keys \
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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см.:

- [Установка Azure CLI](/cli/azure/install-azure-cli)
- [Справочник по интерфейсу командной строки Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Дополнительные примеры Azure CLI для Azure Cosmos DB](cli-samples.md)
