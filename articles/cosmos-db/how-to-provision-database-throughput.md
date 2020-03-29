---
title: Подготовка пропускной способности базы данных в Azure Cosmos DB
description: Узнайте, как обеспечить пропускную способность на уровне базы данных в Azure Cosmos DB с помощью портала Azure, CLI, PowerShell и различных других SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: ef7d06dfb074a3453f5589284cbdaf079c48d111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933775"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Подготовка пропускной способности для базы данных в Azure Cosmos DB

Узнайте, как подготовить пропускную способность для базы данных в Azure Cosmos DB. Вы можете обеспечить пропускную емкость для одного [контейнера,](how-to-provision-container-throughput.md)или для базы данных и поделиться пропускной емкостью среди контейнеров в нем. Чтобы узнать, когда использовать емкость уровня контейнеров и базы данных, см. [Use cases for provisioning throughput on containers and databases](set-throughput.md) Вы можете подготовить пропускную способность уровня базы данных с помощью портала Azure или пакетов SDK для Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Подготовка пропускной способности с помощью портала Azure

### <a name="sql-core-api"></a><a id="portal-sql"></a>API SQL (Core)

1. Войдите на [портал Azure](https://portal.azure.com/).

1. [Создайте новую учетную запись Azure Cosmos](create-sql-api-dotnet.md#create-account) или выберите существующую.

1. Откройте панель **обозревателя данных** и выберите **Новая база данных**. Укажите следующие сведения:

   * Введите идентификатор базы данных.
   * Выберите **Подготовить пропускную способность**.
   * Укажите пропускную способность (например, 1000 ЕЗ/с).
   * Нажмите кнопку **ОК**.

    ![Снимок экрана: диалоговое окно "Новая база данных"](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Продавчивая пропускная способность с помощью Azure CLI или PowerShell

Для создания базы данных с общей пропускной воспроизвоем,

* [Создание базы данных с помощью Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Создание базы данных с помощью Powershell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Подготовка пропускной способности с помощью пакета SDK для .NET

> [!Note]
> Используйте пакеты SDK для Cosmos или API SQL, чтобы подготовить пропускную способность для всех API. При необходимости также можно использовать указанный ниже пример для API Cassandra.

### <a name="all-apis"></a><a id="dotnet-all"></a>Все AIS

### <a name="net-v2-sdk"></a>Пакет SDK для .Net версии 2

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

### <a name="net-v3-sdk"></a>Пакет SDK для .Net версии 3

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Кассандра API
Аналогичная команда может быть выполнена через любой драйвер, совместимый с C'L. 
```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Дальнейшие действия

См. подробнее о подготовке пропускной способности в Azure Cosmos DB:

* [Глобальное масштабирование подготовленной пропускной способности](scaling-throughput.md)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Provision throughput for an Azure Cosmos DB container](how-to-provision-container-throughput.md) (Подготовка пропускной способности для контейнера Azure Cosmos DB)
* [Пропускная способность и единицы запросов в Azure Cosmos DB](request-units.md)
