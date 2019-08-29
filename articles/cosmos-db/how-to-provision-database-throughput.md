---
title: Подготовка пропускной способности базы данных в Azure Cosmos DB
description: Узнайте, как подготовить пропускную способность на уровне базы данных в Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: 29bc65c8afaa1fe4bdc39923bd2219184e8b3a96
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092998"
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


## <a name="provision-throughput-using-azure-cli"></a>Подготовка пропускной способности с помощью портала Azure CLI

```azcli-interactive
az cosmosdb database create --db-name
                            [--key]
                            [--name]
                            [--resource-group-name]
                            [--subscription]
                            [--throughput]
                            [--url-connection]
```




## <a name="provision-throughput-using-powershell"></a>Подготовка пропускной способности с помощью PowerShell

```azurepowershell-interactive
# Create a database and provision throughput of 400 RU/s
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$databaseResourceName = $accountName + "/sql/" + $databaseName

$databaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"= 400 }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseResourceName -PropertyObject $databaseProperties
```

## <a name="provision-throughput-using-net-sdk"></a>Подготовка пропускной способности с помощью пакета SDK для .NET

> [!Note]
> Используйте пакеты SDK для Cosmos или API SQL, чтобы подготовить пропускную способность для всех API. При необходимости также можно использовать указанный ниже пример для API Cassandra.

### <a id="dotnet-all"></a>Все API
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

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Следующие шаги

См. подробнее о подготовке пропускной способности в Azure Cosmos DB:

* [Globally scale provisioned throughput](scaling-throughput.md) (Глобальное масштабирование подготовленной пропускной способности)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Provision throughput for an Azure Cosmos DB container](how-to-provision-container-throughput.md) (Подготовка пропускной способности для контейнера Azure Cosmos DB)
* [Пропускная способность и единицы запросов в Azure Cosmos DB](request-units.md)
