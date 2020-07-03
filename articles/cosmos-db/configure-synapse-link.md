---
title: Настройка и использование Azure Synapse Link для Azure Cosmos DB (предварительная версия)
description: Сведения о включении Synapse Link для учетных записей Cosmos Azure, создании контейнера с включенным аналитическим хранилищем, подключении базы данных Azure Cosmos к рабочей области Synapse и выполнении запросов.
author: SriChintala
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: srchi
ms.openlocfilehash: 384d4ebf211c80ac069889356f269407ba7d3d75
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596533"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>Настройка и использование Azure Synapse Link для Azure Cosmos DB (предварительная версия)

Synapse Link для Azure Cosmos DB — это облачная гибридная транзакционно-аналитическая возможность обработки (HTAP), которая позволяет вам выполнять аналитику рабочих данных в Azure Cosmos DB практически в реальном времени. Synapse Link обеспечивает тесную эффективную интеграцию между Azure Cosmos DB и Azure Synapse Analytics.


> [!IMPORTANT]
> Чтобы использовать Azure Synapse Link, убедитесь, что вы подготовили учетную запись Azure Cosmos и рабочую область Azure Synapse Analytics в одном из перечисленных выше поддерживаемых регионов. Список поддерживаемых регионов см. на странице [обновлений службы Azure](https://azure.microsoft.com/updates/). 

Выполните следующие действия, чтобы создать аналитические запросы с помощью Synapse Link для Azure Cosmos DB:

* [включите Synapse Link для учетных записей Azure Cosmos](#enable-synapse-link);
* [создайте контейнер Azure Cosmos с включенным аналитическим хранилищем](#create-analytical-ttl);
* [подключите базу данных Azure Cosmos к рабочей области Synapse](#connect-to-cosmos-database);
* [выполните запрос к аналитическому хранилищу с помощью Synapse Spark](#query-analytical-store).

## <a name="enable-azure-synapse-link-for-azure-cosmos-accounts"></a><a id="enable-synapse-link"></a>Включение Azure Synapse Link для учетных записей Cosmos Azure

### <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com/).

1. [Создайте учетную запись Azure](create-sql-api-dotnet.md#create-account) или выберите существующую учетную запись Azure Cosmos.

1. Перейдите к учетной записи Azure Cosmos и откройте панель **Функции**.

1. Выберите **Synapse Link** из списка функций.

   ![Поиск предварительной версии функции Synapse Link](./media/configure-synapse-link/find-synapse-link-feature.png)

1. Далее вам будет предложено включить Synapse Link в вашей учетной записи. Выберите "Включить".

   ![Включение функции Synapse Link](./media/configure-synapse-link/enable-synapse-link-feature.png)

1. Теперь ваша учетная запись включена для использования Synapse Link. Далее ознакомьтесь со сведениями о создании контейнеров с поддержкой аналитического хранилища для автоматического запуска репликации операционных данных из хранилища транзакций в аналитическое хранилище.

### <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager

[Шаблон Azure Resource Manager](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store) позволяет создать учетную запись Azure Cosmos с возможностью Synapse Link для API SQL. Этот шаблон позволяет создать учетную запись API (SQL) Core в одном регионе с настроенным контейнером, для которого включена поддержка аналитического срока жизни и возможность выбрать для пропускной способности настройку вручную или с помощью автомасштабирования. Чтобы развернуть этот шаблон, щелкните **Развертывание в Azure** на странице файла сведений.

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> Создание контейнера Azure Cosmos с помощью аналитического хранилища

Вы можете включить аналитическое хранилище в контейнере Azure Cosmos при создании контейнера. Вы можете использовать портал Azure или настроить свойство `analyticalTTL` во время создания контейнера с помощью пакетов SDK Azure Cosmos DB.

> [!NOTE]
> В настоящее время аналитическое хранилище можно включить для **новых** контейнеров (как в новых, так и в существующих учетных записях).

### <a name="azure-portal"></a>Портал Azure

1. Откройте [портал Azure](https://portal.azure.com/) или [обозреватель Azure Cosmos](https://cosmos.azure.com/).

1. Перейдите к своей учетной записи Azure Cosmos и откройте вкладку **Data Explorer**.

1. Выберите **Создать контейнер** и введите имя базы данных, контейнера, ключ секции и сведения о пропускной способности. Активируйте параметр **Analytical store** (Аналитическое хранилище). После включения аналитического хранилища создается контейнер со свойством `AnalyicalTTL`, для которого задано значение по умолчанию -1 (неограниченный срок хранения). В этом аналитическом хранилище содержатся все исторические версии записей.

   ![Включение аналитического хранилища для контейнера Azure Cosmos](./media/configure-synapse-link/create-container-analytical-store.png)

1. Если вы ранее не включили Synapse Link в этой учетной записи, вам будет предложено сделать это, так как создание контейнера с включенным аналитическим хранилищем является необходимым условием. При появлении запроса выберите **Включить Synapse Link**.

1. Нажмите кнопку **OK**, чтобы создать контейнер Azure Cosmos с включенным аналитическим хранилищем.

### <a name="net-sdk"></a>Пакет SDK для .NET

Следующий код создает контейнер с аналитическим хранилищем с помощью пакета SDK для .NET. Задайте требуемое значение для свойства аналитического срока жизни. Список допустимых значений см. в статье о [поддерживаемых значениях аналитического срока жизни](analytical-store-introduction.md#analytical-ttl):

```csharp
// Create a container with a partition key, and analytical TTL configured to  -1 (infinite retention)
string containerId = “myContainerName”;
int analyticalTtlInSec = -1;
ContainerProperties cpInput = new ContainerProperties()
            {
Id = containerId,
PartitionKeyPath = "/id",
AnalyticalStorageTimeToLiveInSeconds = analyticalTtlInSec,
};
 await this. cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(cpInput);
```

### <a name="java-v4-sdk"></a>Пакет SDK для Java версии 4

Следующий код создает контейнер с аналитическим хранилищем с помощью пакета SDK для Java версии 4. Задайте для свойства `AnalyticalStoreTimeToLiveInSeconds` необходимое значение:

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v3-sdk"></a>Пакет SDK для Python версии 3

Следующий код создает контейнер с аналитическим хранилищем с помощью пакета SDK для Python:

```python
import azure.cosmos.cosmos_client as cosmos_client
def create_collection_if_not_exists(cosmosEndpoint, cosmosKey, databaseName, collectionName):
    client = cosmos_client.CosmosClient(url_connection=cosmosEndpoint, auth={'masterKey': cosmosKey})

db = client.QueryDatabases("select * from c where c.id = '" + databaseName + "'").fetch_next_block()[0]
options = {
    'offerThroughput': 1000
}

container_definition = {
    'id': collectionName,
    "partitionKey": {  
        "paths": [  
        "/id"  
        ],  
        "kind": "Hash" 
    },
    "indexingPolicy": {  
    "indexingMode": "consistent",  
    "automatic": True,  
    "includedPaths": [],  
    "excludedPaths": [{
        "path": "/*"
    }]  
    },
    "defaultTtl": -1,
    "analyticalStorageTtl": -1
}

container = client.CreateContainer(db['_self'], container_definition, options)
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> Обновление срока жизни аналитического хранилища

После включения аналитического хранилища с определенным значением срока жизни вы можете позже задать для него другое допустимое значение. Значение можно обновить с помощью портала Azure или пакетов SDK. Сведения о различных параметрах конфигурации аналитического срока жизни см. в статье о [поддерживаемых значениях аналитического срока жизни](analytical-store-introduction.md#analytical-ttl).

#### <a name="azure-portal"></a>Портал Azure

Если контейнер с включенным аналитическим хранилищем был создан на портале Azure, он содержит стандартное значение аналитического срока жизни, которое равно -1. Чтобы обновить это значение, выполните следующие действия:

1. Откройте [портал Azure](https://portal.azure.com/) или [обозреватель Azure Cosmos](https://cosmos.azure.com/).

1. Перейдите к своей учетной записи Azure Cosmos и откройте вкладку **Data Explorer**.

1. Выберите существующий контейнер с включенным аналитическим хранилищем. Разверните его и измените следующие значения:

  * Откройте окно **Scale & Settings** (Параметры масштабирования).
  * В разделе **Параметр** найдите ** Analytical Storage Time to Live** (Срок жизни аналитического хранилища).
  * Выберите **Включен (по умолчанию)** или **Включен** и задайте значение срока жизни.
  * Щелкните **Сохранить** , чтобы сохранить изменения.

#### <a name="net-sdk"></a>Пакет SDK для .NET

В следующем коде показано, как обновить срок жизни для аналитического хранилища с помощью пакета SDK для .NET:

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

#### <a name="java-v4-sdk"></a>Пакет SDK для Java версии 4

В следующем коде показано, как обновить срок жизни для аналитического хранилища с помощью пакета SDK для Java версии 4:

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Подключение к рабочей области Synapse

Выполните инструкции из статьи о [подключении к Azure Synapse Link](), чтобы получить доступ к базе данных Azure Cosmos DB из Azure Synapse Analytics Studio с помощью Azure Synapse Link.

## <a name="query-using-synapse-spark"></a><a id="query-analytical-store"></a> Создание запроса с помощью Synapse Spark

Инструкции по выполнению запросов с помощью Synapse Spark см. в статье о [запросах в аналитическое хранилище Azure Cosmos DB](). В этой статье приводятся некоторые примеры того, как можно взаимодействовать с аналитическим хранилищем с помощью жестов Synapse. Чтобы просмотреть эти жесты, щелкните правой кнопкой мыши контейнер. С помощью жестов можно быстро создать код и скорректировать его в соответствии с потребностями. Они также идеально подходят для обнаружения данных одним щелчком мыши.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих документах:

* [Сведения об Azure Synapse Link для Azure Cosmos DB (предварительная версия)](synapse-link.md).

* [Общие сведения об аналитическом хранилище Azure Cosmos DB (предварительная версия)](analytical-store-introduction.md).

* [Часто задаваемые вопросы о Azure Synapse Link для Azure Cosmos DB](synapse-link-frequently-asked-questions.md).

* [Ключевые концепции Apache Spark в Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [Использование SQL по запросу (предварительная версия) в Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
