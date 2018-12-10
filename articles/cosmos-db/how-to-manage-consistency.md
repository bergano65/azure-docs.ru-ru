---
title: Сведения об управлении согласованностью в Azure Cosmos DB
description: Сведения об управлении согласованностью в Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 9d8c1296fc811d97dc9e7e66ad9bd9fdc79d66f9
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634342"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Управление уровнями согласованности в Azure Cosmos DB

В этой статье рассматривается управление уровнями согласованности в Azure Cosmos DB. Вы узнаете, как настроить уровень согласованности по умолчанию, а также как переопределить ее и управлять маркерами сеанса вручную. Кроме того, здесь приведены общие сведения о метрике вероятностного ограниченного устаревания (PBS).

## <a name="configure-the-default-consistency-level"></a>Настройка уровня согласованности по умолчанию

Клиенты по умолчанию используют стандартный уровень согласованности. Они могут его переопределить.

### <a name="cli"></a>Интерфейс командной строки

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

В этом примере создается новая учетная запись Azure Cosmos DB с поддержкой нескольких мастеров в восточной и западной части США. Политика согласованности по умолчанию установлена ​​как сеанс.

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

Чтобы просмотреть или изменить уровень согласованности по умолчанию, войдите на портал Azure. Найдите учетную запись Azure Cosmos DB и откройте панель **Согласованность по умолчанию**. Выберите требуемый уровень согласованности в качестве нового значения по умолчанию, а затем выберите **Сохранить**.

![Меню согласованности на портале Azure](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Переопределение уровня согласованности по умолчанию

Клиенты могут переопределить уровень согласованности по умолчанию, который задается службой. Этот параметр можно настроить для клиента или на один запрос.

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

Чтобы управлять токенами сеанса вручную, получите токен сеанса из ответа и установите их для каждого запроса. Если у вас нет необходимости управлять токенами сеанса вручную, то вам не нужно использовать эти примеры. Пакет SDK отслеживает токены сеанса автоматически. Если токен сеанса не задан вручную, по умолчанию в пакете SDK используется последний.

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

Чтобы просмотреть метрики PBS, перейдите к учетной записи Azure Cosmos DB на портале Azure. Откройте панель **Метрики** и выберите вкладку **Согласованность**. Просмотрите график с названием **Probability of strongly consistent reads based on your workload (see PBS)** (Вероятность строго согласованных операций чтения на основании вашей рабочей нагрузки (см. PBS)).

![График PBS на портале Azure](./media/how-to-manage-consistency/pbs-metric.png)

Чтобы просмотреть эту метрику, используйте меню метрик в Azure Cosmos DB. Она не будет отображаться в интерфейсе метрик в службе Azure Monitor.

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о том, как управлять конфликтами данных, или перейдите к следующей ключевой концепции в Azure Cosmos DB. Ознакомьтесь со следующими статьями:

* [Управление конфликтами между регионами](how-to-manage-conflicts.md)
* [Секционирование и масштабирование в Azure Cosmos DB](partition-data.md)
