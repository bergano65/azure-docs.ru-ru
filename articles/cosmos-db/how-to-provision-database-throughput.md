---
title: Подготовка пропускной способности базы данных в Azure Cosmos DB
description: Узнайте, как подготовить пропускную способность на уровне базы данных в Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: d73dd5ffe8cc8ed00288209b628d7175b795b335
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243770"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Подготовка пропускной способности для базы данных в Azure Cosmos DB

Узнайте, как подготовить пропускную способность для базы данных в Azure Cosmos DB. Вы можете подготовить пропускную способность для [одного](how-to-provision-container-throughput.md) контейнера или для базы данных и разделить ее между контейнерами внутри базы данных. Сведения о том, когда следует использовать пропускную способность уровня контейнера и уровня базы данных, см. в статье [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md). Вы можете подготовить пропускную способность уровня базы данных с помощью портала Azure или пакетов SDK для Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Подготовка пропускной способности с помощью портала Azure

### <a id="portal-sql"></a>API SQL (Core)

1. Войдите на [портале Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Azure Cosmos](create-sql-api-dotnet.md#create-account) или выберите существующую.

1. Откройте панель **обозревателя данных** и выберите **Новая база данных**. Укажите следующие сведения:

   * Введите идентификатор базы данных. 
   * Выберите **Подготовить пропускную способность**.
   * Укажите пропускную способность (например, 1000 ЕЗ/с).
   * Нажмите кнопку **ОК**.

![Снимок экрана: диалоговое окно "Новая база данных"](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>Подготовка пропускной способности с помощью пакета SDK для .NET

> [!Note]
> Используйте пакеты SDK для Cosmos или API SQL, чтобы подготовить пропускную способность для всех API. При необходимости также можно использовать указанный ниже пример для API Cassandra.

### <a id="dotnet-all"></a>Все API

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>Дополнительная информация

См. подробнее о подготовке пропускной способности в Azure Cosmos DB:

* [Globally scale provisioned throughput](scaling-throughput.md) (Глобальное масштабирование подготовленной пропускной способности)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Provision throughput for an Azure Cosmos DB container](how-to-provision-container-throughput.md) (Подготовка пропускной способности для контейнера Azure Cosmos DB)
* [Пропускная способность и единицы запросов в Azure Cosmos DB](request-units.md)