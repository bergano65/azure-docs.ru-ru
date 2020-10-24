---
title: Создание контейнера в Azure Cosmos DB API SQL
description: Узнайте, как создать контейнер в Azure Cosmos DB API SQL с помощью портал Azure, .NET, Java, Python, Node.js и других пакетов SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: eb9e8caff81e86b7db732c970eee1e1a08be5241
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491110"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Создание контейнера в Azure Cosmos DB API SQL

В этой статье объясняются различные способы создания контейнера в Azure Cosmos DB API SQL. В нем показано, как создать контейнер с помощью портал Azure, Azure CLI, PowerShell или поддерживаемых пакетов SDK. В этой статье показано, как создать контейнер, указать ключ секции и подготовить пропускную способность.

В этой статье объясняются различные способы создания контейнера в Azure Cosmos DB API SQL. Если вы используете другой API, см. статью [API for MongoDB](how-to-create-container-mongodb.md), [API Cassandra](how-to-create-container-cassandra.md), [Gremlin API](how-to-create-container-gremlin.md)и [API таблиц](how-to-create-container-table.md) статьи, чтобы создать контейнер.

> [!NOTE]
> При создании контейнеров следите за тем, чтобы не создать два контейнера с одинаковыми именами, но в разных регистрах. Причина в том, что некоторые компоненты платформы Azure не учитывают регистр. Это может привести к путанице или конфликтам в данных телеметрии и действиях с контейнерами с такими именами.

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Создание контейнера с помощью портала Azure

1. Войдите на [портал Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Azure Cosmos](create-sql-api-dotnet.md#create-account) или выберите существующую.

1. Откройте панель **Обозреватель данных** и выберите **создать контейнер**. После этого предоставьте следующие сведения.

   * Укажите, создаете ли вы новую базу данных или используете существующую.
   * Введите идентификатор контейнера.
   * Введите ключ секции.
   * Укажите подготавливаемую пропускную способность (например, 1000 ЕЗ/с).
   * Нажмите кнопку **ОК**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Снимок экрана панели обозреватель данных с выделенным новым контейнером":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>Создание контейнера с помощью Azure CLI

[Создайте контейнер с Azure CLI](manage-with-cli.md#create-a-container). Список всех Azure CLI примеров для всех Azure Cosmos DB API см. в [Azure CLI примерах для Azure Cosmos DB](cli-samples.md).

## <a name="create-a-container-using-powershell"></a>Создание контейнера с помощью PowerShell

[Создайте контейнер с помощью PowerShell](manage-with-powershell.md#create-container). Список всех примеров PowerShell для всех Azure Cosmos DB API см. в статье [примеры PowerShell](powershell-samples.md) .

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>Создайте контейнер с помощью .NET SDK

При возникновении исключения timeout при создании коллекции выполните операцию чтения, чтобы проверить, успешно ли создана коллекция. Операция чтения создает исключение до тех пор, пока операция создания коллекции не будет выполнена успешно. Список кодов состояния, поддерживаемых операцией Create, см. в статье [коды состояния HTTP для Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) .

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="next-steps"></a>Дальнейшие действия

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Секционирование в Azure Cosmos DB)
* [Единицы запросов в Azure Cosmos DB](request-units.md)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Работа с учетной записью Azure Cosmos](./account-databases-containers-items.md)