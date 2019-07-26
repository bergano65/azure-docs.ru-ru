---
title: Сведения об управлении согласованностью в Azure Cosmos DB
description: Сведения об управлении согласованностью в Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: 2617aba0d790209d83f410ee632ffad43c952d55
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356418"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Управление уровнями согласованности в Azure Cosmos DB

В этой статье рассматривается управление уровнями согласованности в Azure Cosmos DB. Вы узнаете, как настроить уровень согласованности по умолчанию, а также как переопределить ее и управлять маркерами сеанса вручную. Кроме того, здесь приведены общие сведения о метрике вероятностного ограниченного устаревания (PBS).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Настройка уровня согласованности по умолчанию

Клиенты по умолчанию используют [стандартный уровень согласованности](consistency-levels.md). Они могут его переопределить в любой момент.

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Eventual
```

### <a name="powershell"></a>PowerShell

В этом примере создается учетная запись Azure Cosmos DB с поддержкой нескольких регионов для записи в восточной и западной части США. Задан уровень согласованности по умолчанию *Сеанс*.

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

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="azure-portal"></a>Портал Azure

Чтобы просмотреть или изменить уровень согласованности по умолчанию, войдите на портал Azure. Найдите учетную запись Azure Cosmos DB и откройте область **Согласованность по умолчанию**. Выберите требуемый уровень согласованности в качестве нового значения по умолчанию, а затем выберите **Сохранить**. На портале Azure также представлена визуализация разных уровней согласованности на основе музыкальных нот. 

![Меню согласованности на портале Azure](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Переопределение уровня согласованности по умолчанию

Клиенты могут переопределить уровень согласованности по умолчанию, который задается службой. Уровень согласованности можно задать для каждого запроса. В этом случае будет переопределен уровень согласованности по умолчанию на уровне учетной записи.

### <a id="override-default-consistency-dotnet"></a>Пакет SDK для .NET версии 2

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-dotnet-v3"></a>Пакет SDK для .NET версии 3

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item, 
        new PartitionKey(itemPartitionKey), 
        requestOptions);
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
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```

### <a id="override-default-consistency-javascript"></a>Пакет SDK для Node.js, JavaScript и TypeScript

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Пакет SDK для Python

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Использование маркеров сеанса

Один из этих уровней согласованности в Azure Cosmos DB — согласованность *сеансов*. Этот уровень по умолчанию применяются к учетным записям Cosmos. Если задана согласованность *сеансов*, то клиент будет использовать внутренний маркер сеанса для каждой операции чтения или запроса, чтобы обеспечить соблюдение заданного уровня согласованности.

Чтобы управлять токенами сеанса вручную, получите токен сеанса из ответа и установите их для каждого запроса. Если у вас нет необходимости управлять токенами сеанса вручную, то вам не нужно использовать эти примеры. Пакет SDK отслеживает токены сеанса автоматически. Если токен сеанса не задан вручную, по умолчанию в пакете SDK используется последний.

### <a id="utilize-session-tokens-dotnet"></a>Пакет SDK для .NET версии 2

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-dotnet-v3"></a>Пакет SDK для .NET версии 3

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
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

Насколько итоговая согласованность является итоговой? В среднем мы можем предложить границы устаревания с учетом журнала версий и времени. Метрика [**Probabilistically Bounded Staleness (PBS)** ](https://pbs.cs.berkeley.edu/) (Вероятностное ограниченное устаревание) пытается количественно оценить вероятность устаревания и отображает полученные результаты. Чтобы просмотреть метрику PBS, перейдите к учетной записи Azure Cosmos DB на портале Azure. Откройте панель **Метрики** и выберите вкладку **Согласованность**. Просмотрите график с названием **Probability of strongly consistent reads based on your workload (see PBS)** (Вероятность строго согласованных операций чтения на основании вашей рабочей нагрузки (см. PBS)).

![График PBS на портале Azure](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о том, как управлять конфликтами данных, или перейдите к следующей ключевой концепции в Azure Cosmos DB. Ознакомьтесь со следующими статьями:

* [Настраиваемые уровни согласованности данных в Azure Cosmos DB](consistency-levels.md)
* [Управление конфликтами между регионами](how-to-manage-conflicts.md)
* [Секционирование и масштабирование в Azure Cosmos DB](partition-data.md)
* [Consistency Tradeoffs in Modern Distributed Database Systems Design](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k) (Достижение компромиссов согласованности в современных распределенных базах данных)
* [Высокая доступность](high-availability.md)
* [Соглашение об уровне обслуживания для Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
