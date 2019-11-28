---
title: Руководство по Руководство по настройке глобального распределения Azure Cosmos DB с помощью API SQL
description: Руководство по Сведения о настройке глобального распределения Azure Cosmos DB с помощью API SQL.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: 7c7ea4ca8fc28ef47e60d5010c804223c3cfab3b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173833"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Руководство по Настройка глобального распределения Azure Cosmos DB с помощью API SQL

В этой статье показано, как настроить глобальное распределение базы данных Azure Cosmos DB и подключиться к ней с помощью API SQL на портале Azure.

В этой статье рассматриваются следующие задачи: 

> [!div class="checklist"]
> * настроили глобальное распределение на портале Azure;
> * настроили глобальное распределение с помощью [API SQL](sql-api-introduction.md).

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a>Подключение к предпочтительному региону с помощью API SQL

Чтобы воспользоваться преимуществами [глобального распределения](distribute-data-globally.md), клиентские приложения могут указать упорядоченный список предпочитаемых регионов, который будет использоваться для операций с документами. Это можно сделать, настроив политику подключения. Для операций записи и чтения с помощью пакета SDK для SQL выбирается наиболее оптимальная конечная точка на основании текущих данных о региональной доступности и списка предпочтений, указанного в конфигурации учетной записи Azure Cosmos DB.

Этот список предпочтений указывается при инициализации подключения с помощью пакетов SDK для SQL. Пакеты SDK принимают необязательный параметр PreferredLocations, представляющий собой упорядоченный список регионов Azure.

Пакет SDK будет автоматически отправлять все операции записи в текущий регион записи.

Все операции чтения будут отправляться в первой доступный регион из списка PreferredLocations. Если запрос завершится неудачей, клиент перейдет к следующему региону дальше по списку и так далее.

Пакеты SDK будут пытаться выполнять чтение данных из регионов, указанных в PreferredLocations. Например, если учетная запись базы данных доступна в четырех регионах, но клиент указывает в PreferredLocations только два региона для чтения (не для записи), то операции чтения не будут обслуживаться для региона, который не указан в PreferredLocations. Если указанные в PreferredLocations регионы не доступны, операции чтения обслуживаются в регионе записи.

Приложение может проверить текущие конечную точку записи и конечную точку чтения, выбранные пакетом SDK, просмотрев два свойства, WriteEndpoint и ReadEndpoint, доступные в SDK 1.8 и более поздней версии.

Если свойство PreferredLocations не задано, будут обслуживаться все запросы из текущего региона записи.

## <a name="net-sdk"></a>Пакет SDK для .NET
Пакет SDK можно использовать без изменения кода. В этом случае пакет SDK автоматически направляет операции чтения и записи в текущий регион записи.

В пакете SDK для .NET 1.8 и более поздней версии параметр ConnectionPolicy конструктора DocumentClient имеет свойство Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Это свойство имеет тип коллекции `<string>` и должно содержать список имен регионов. Строковые значения форматируются по столбцу "Имя региона" на странице [Регионы Azure][regions] без пробелов до или после первого и последнего знака соответственно.

Текущие конечные точки записи и чтения доступны в DocumentClient.WriteEndpoint и DocumentClient.ReadEndpoint, соответственно.

> [!NOTE]
> Не следует считать URL-адреса конечных точек долговременными константами. Служба может обновить их в любой момент. Пакет SDK обрабатывает это изменение автоматически.
>
>

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

## <a name="nodejsjavascript"></a>Node. js и JavaScript

> [!NOTE]
> Не следует считать URL-адреса конечных точек долговременными константами. Служба может обновить их в любой момент. Пакет SDK обработает это изменение автоматически.
>
>

Ниже приведен пример кода для Node.js и JavaScript.

```JavaScript
// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
const preferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
const client = new CosmosClient{ endpoint, key, connectionPolicy: { preferredLocations } });
```

## <a name="python-sdk"></a>Пакет SDK для Python

В коде ниже показано, как задать требуемое расположение с помощью пакета SDK для Python.

```python

connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v2-sdk"></a>Пакет SDK для Java версии 2

В коде ниже показано, как задать требуемое расположение с помощью пакета SDK для Java.

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Arrays.asList("East US", "West US", "Canada Central"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKeyOrResourceToken(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy)
                .build();
```

## <a name="rest"></a>REST
После того, как учетная запись базы данных станет доступной в нескольких регионах, клиенты смогут запрашивать ее доступность с помощью запроса GET к следующему универсальному коду ресурса (URI).

    https://{databaseaccount}.documents.azure.com/

Служба вернет список регионов и соответствующих универсальных кодов ресурса (URI) конечных точек Azure Cosmos DB для реплик. В ответе будет указан текущий регион записи. Затем клиент сможет выбрать подходящую конечную точку для всех последующих запросов REST API следующим образом.

Пример ответа

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* Все запросы PUT, POST и DELETE должны направляться на указанный универсальный код ресурса (URI) записи.
* Все запросы GET и другие запросы только для чтения могут направляться к любой конечной точке на выбор клиента.

Запросы на запись в регионы, доступные только для чтения, вернут код ошибки HTTP 403 (Forbidden).

В случае изменения региона записи после того, как клиент выполнил этап начального обнаружения, все последующие операции записи в предыдущий регион записи завершатся ошибкой с кодом HTTP 403 (Forbidden). Клиенту следует еще раз получить список регионов (запрос GET), чтобы получить обновленный регион записи.

На этом руководство завершено. Сведения об управлении согласованностью глобально реплицируемой учетной записи Azure Cosmos DB см. в [этой статье](consistency-levels.md). Сведения о том, как функционирует репликация глобальной базы данных в Azure Cosmos DB, см. в статье о [глобальном распределении данных в Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * настроили глобальное распределение на портале Azure;
> * настроили глобальное распределение с помощью API SQL.

Перейдите к следующему руководству, чтобы узнать о разработке в локальной среде с помощью локального эмулятора Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Разработка в локальной среде с помощью эмулятора](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

