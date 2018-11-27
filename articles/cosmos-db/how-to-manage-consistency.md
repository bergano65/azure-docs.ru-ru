---
title: Сведения об управлении согласованностью в Azure Cosmos DB
description: Сведения об управлении согласованностью в Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 68c8c3767ff3a3d2873c1ff50928ab8d2cada4b1
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263757"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Управление уровнями согласованности в Azure Cosmos DB

В этой статье описываются различные способы настройки согласованности по умолчанию, а также как переопределить эту согласованность для клиента и управлять маркерами сеанса вручную. Кроме того, здесь приведены общие сведения о метрике вероятностного ограниченного устаревания (PBS).

## <a name="configure-the-default-consistency-level"></a>Настройка уровня согласованности по умолчанию

Клиенты по умолчанию будут использовать стандартный уровень согласованности. Его можно переопределить для клиентов.

### <a name="cli"></a>Интерфейс командной строки

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

В приведенном ниже примере создается учетная запись Cosmos DB с поддержкой нескольких источников в регионах "Восточная часть США" и "Западная часть США". Политика согласованности по умолчанию настраивается как политика сеанса.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="portal"></a>Microsoft Azure

Чтобы просмотреть или изменить уровень согласованности по умолчанию, войдите на портал Azure. Найдите учетную запись Cosmos DB, откройте панель **Согласованность по умолчанию**. Выберите нужный уровень согласованности в качестве нового уровня по умолчанию, а затем нажмите кнопку "Сохранить".

![Изображение меню согласованности на портале Azure](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Переопределение уровня согласованности по умолчанию

Клиенты могут переопределить уровень согласованности по умолчанию, который задается службой. Это можно сделать для всего клиента или на один запрос.

### <a id="override-default-consistency-dotnet"></a>Пакет SDK для .NET

```csharp
// Override consistency at the client level
ConsistencyPolicy consistencyPolicy = new ConsistencyPolicy
    {
        DefaultConsistencyLevel = ConsistencyLevel.BoundedStaleness,
        MaxStalenessIntervalInSeconds = 5,
        MaxStalenessPrefix = 100
    };
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, consistencyPolicy);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-java-async"></a>Пакет SDK для Java (асинхронная модель)

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

### <a id="override-default-consistency-java-sync"></a>Пакет SDK для Java (синхронная модель)

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

### <a id="override-default-consistency-javascript"></a>Пакет SDK для Node.js, JavaScript и TypeScript

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Пакет SDK для Python

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>Использование маркеров сеанса

Если вы хотите управлять маркерами сеанса вручную, вы можете получить от них отклики и задавать их на один запрос. Если у вас нет необходимости управлять маркерами сеанса вручную, то вам не нужно использовать приведенные ниже примеры. Пакет SDK будет автоматически отслеживать маркеры сеанса и использовать самый последний, если вы не установите токен сеанса самостоятельно.

### <a id="utilize-session-tokens-dotnet"></a>Пакет SDK для .NET

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-java-async"></a>Пакет SDK для Java (асинхронная модель)

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

### <a id="utilize-session-tokens-java-sync"></a>Пакет SDK для Java (синхронная модель)

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Пакет SDK для Node.js, JavaScript и TypeScript

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Пакет SDK для Python

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Мониторинг метрики вероятностного ограниченного устаревания (PBS)

Чтобы просмотреть метрики PBS, перейдите к учетной записи Cosmos DB на портале Azure и откройте панель **Метрики**. Затем щелкните вкладку **Согласованность** и просмотрите на график с названием **Probability of strongly consistent reads based on your workload (see PBS)** (Вероятность строго согласованных операций чтения на основании вашей рабочей нагрузки (см. PBS)).

![Изображение графика PBS на портале Azure](./media/how-to-manage-consistency/pbs-metric.png)

Чтобы просмотреть эту метрику, используйте меню метрик в Cosmos DB. Она не будет отображаться в интерфейсе метрик в службе Azure Monitor.

## <a name="next-steps"></a>Дополнительная информация

Чтобы получить дополнительные сведения об управлении конфликтами данных или рассмотреть следующее основное понятие Cosmos DB, обратитесь к следующей документации.

* [Управление конфликтами между регионами](how-to-manage-conflicts.md)
* [Секционирование и масштабирование в Azure Cosmos DB](partition-data.md)
