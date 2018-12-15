---
title: Управление ресурсами Azure Cosmos DB с помощью Azure CLI
description: Управляйте учетной записью, базой данных и контейнерами Azure Cosmos DB с помощью службы Azure CLI.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: d79cae16c109b407840b8565eb80fb87bbed6432
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877553"
---
# <a name="manage-azure-cosmos-db-resources-using-azure-cli"></a>Управление ресурсами Azure Cosmos DB с помощью Azure CLI

В этом руководстве содержатся контейнеры Azure CLI, используемые для автоматизации управления учетными записями баз данных Azure Cosmos DB. Оно также включает команды для масштабирования пропускной способности контейнера. Страницы справки для всех команд интерфейса командной строки Azure Cosmos DB доступны в [справочнике по Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). Вы также можете найти несколько примеров в [Экземплярах Azure CLI для Azure Cosmos DB](cli-samples.md), в том числе и сведения о том, как создавать и управлять учетными записями, базами данных и контейнерами Cosmos DB для MongoDB, Gremlin, Cassandra и Table API.

При помощи этого примера-скрипта CLI создается учетная запись, база данных и контейнер API SQL в Azure Cosmos DB.  

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>создание учетной записи Azure Cosmos DB;

Чтобы создать учетную запись Azure Cosmos DB с SQL API, согласованным сеансом, поддержкой нескольких источников в восточной и западной части США, откройте Azure CLI или Cloud Shell и выполните следующую команду:

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --kind GlobalDocumentDB \
   --default-consistency-level "Session" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="create-a-database"></a>Создание базы данных

Чтобы создать базу данных Cosmos DB, откройте Azure CLI или Cloud Shell и выполните следующую команду:

```azurecli-interactive
az cosmosdb database create \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup"
```

## <a name="create-a-container"></a>Создание контейнера

Чтобы создать контейнер Cosmos DB с 1000 ЕЗ/с и ключом секции, откройте Azure CLI или Cloud Shell и выполните следующую команду:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --partition-key-path = "/myPartitionKey" \
   --throughput 1000
```

## <a name="change-the-throughput-of-a-container"></a>Изменение пропускной способности контейнера

Чтобы изменить пропускную способность контейнера Cosmos DB с 400 ЕЗ/с, откройте Azure CLI или Cloud Shell и выполните следующую команду:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --throughput 400
```

## <a name="list-account-keys"></a>Вывод ключей учетной записи

При создании учетной записи Azure Cosmos DB служба создает два главных ключа доступа, которые можно использовать для проверки подлинности при доступе к учетной записи Azure Cosmos DB. Предоставляя два ключа, Azure Cosmos DB позволяет вам повторно создавать ключи без перерывов в работе учетной записи Azure Cosmos DB. Ключи только для чтения, используемые для выполнения проверки подлинности операций чтения, также доступны. Доступны два ключа для чтения и записи (первичный и вторичный), а также два ключа только для чтения (первичный и вторичный). Получите ключи для своей учетной записи Azure с помощью следующей команды:

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="list-connection-strings"></a>Вывод строк подключения

Строку для подключения вашего приложения к учетной записи Cosmos DB можно получить с помощью следующей команды.

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="regenerate-account-key"></a>Повторное создание ключей учетных записей

Вам следует периодически менять ключи доступа для своей учетной записи Azure Cosmos DB, чтобы повысить уровень безопасности соединений. Назначается два ключа доступа, что позволяет поддерживать подключения к учетной записи Azure Cosmos DB с помощью одного ключа, одновременно повторно создавая второй ключ.

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup" \
   --key-kind primary
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см.:

- [Установка Azure CLI](/cli/azure/install-azure-cli)
- [Справочник по интерфейсу командной строки Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Дополнительные примеры Azure CLI для Azure Cosmos DB](cli-samples.md)
