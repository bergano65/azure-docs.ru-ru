---
title: Подготовка пропускной способности базы данных в Azure Cosmos DB
description: Узнайте, как подготавливать пропускную способность на уровне базы данных в Azure Cosmos DB с помощью портала Azure, CLI, PowerShell и других пакетов SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 668aa51bdb57dc4bcde0e3a95c481bb60e3d8ed3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88997376"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db"></a>Подготовка стандартной (масштабируемой вручную) пропускной способности в базе данных Azure Cosmos DB

Подготовка стандартной (масштабируемой вручную) пропускной способности в базе данных в Azure Cosmos DB. Вы можете подготовить пропускную способность для [одного](how-to-provision-container-throughput.md) контейнера или для базы данных и разделить ее между контейнерами внутри базы данных. Сведения о том, когда следует использовать пропускную способность уровня контейнера и уровня базы данных, см. в статье [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md). Вы можете подготовить пропускную способность уровня базы данных с помощью портала Azure или пакетов SDK для Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Подготовка пропускной способности с помощью портала Azure

### <a name="sql-core-api"></a><a id="portal-sql"></a>API SQL (Core)

1. Войдите на [портал Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Azure Cosmos](create-sql-api-dotnet.md#create-account) или выберите существующую.

1. Откройте панель **обозревателя данных** и выберите **Новая база данных**. Укажите следующие сведения:

   * Введите идентификатор базы данных.
   * Выберите **Подготовить пропускную способность**.
   * Укажите пропускную способность (например, 1000 ЕЗ/с).
   * Щелкните **ОК**.

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png" alt-text="Снимок экрана: диалоговое окно &quot;Новая база данных&quot;":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Подготовка пропускной способности с помощью портала Azure CLI или PowerShell

Чтобы получить сведения о создании базы данных с совместно используемой пропускной способностью см.:

* [Создание базы данных с помощью Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Создание базы данных с помощью PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Подготовка пропускной способности с помощью пакета SDK для .NET

> [!Note]
> Используйте пакеты SDK для Cosmos или API SQL, чтобы подготовить пропускную способность для всех API. При необходимости также можно использовать указанный ниже пример для API Cassandra.

### <a name="all-apis"></a><a id="dotnet-all"></a>Все API

# <a name="net-sdk-v2"></a>[Пакет SDK для .NET версии 2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[Пакет SDK для .NET версии 3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>API Cassandra

Аналогичную команду можно выполнить с помощью любого драйвера, совместимого с CQL.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Дальнейшие действия

См. подробнее о подготовке пропускной способности в Azure Cosmos DB:

* [Globally scale provisioned throughput](scaling-throughput.md) (Глобальное масштабирование подготовленной пропускной способности)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Подготовка стандартной (масштабируемой вручную) пропускной способности для контейнера](how-to-provision-container-throughput.md)
* [Подготовка автомасштабируемой пропускной способности для контейнера Azure Cosmos DB](how-to-provision-autoscale-throughput.md)
* [Пропускная способность и единицы запросов в Azure Cosmos DB](request-units.md)
