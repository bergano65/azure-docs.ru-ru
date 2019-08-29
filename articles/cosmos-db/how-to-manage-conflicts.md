---
title: Сведения об управлении конфликтами между регионами в Azure Cosmos DB
description: Сведения об управлении конфликтами в Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: c58828fd8ed0de73c03e9e741d14705ad88b1333
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093226"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Управление политиками разрешения конфликтов в Azure Cosmos DB

Если операции записи выполняются в нескольких регионах и несколько клиентов обращаются к одному и тому же элементу, может возникнуть конфликт. Такую проблему можно решить с помощью разных политик устранения конфликтов. В этой статье объясняется, как управлять политиками устранения конфликтов.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Создание политики разрешения конфликтов, реализующей подход "Сохраняются изменения, внесенные последними"

В этих примерах показано, как настроить контейнер с политикой разрешения конфликтов, реализующей подход "Сохраняются изменения, внесенные последними". В пути по умолчанию для политики "Сохраняются изменения, внесенные последними" указывается поле метки времени или свойство `_ts`. Для API SQL также можно указать определяемый пользователем путь с использованием числового типа. При возникновении конфликта сохраняется самое большое значение. Если путь не задан или является недопустимым, по умолчанию используется значение `_ts`. Сведения о конфликтах, устраненных с помощью этой политики, не будут отображаться в веб-канале конфликтов. Эту политику поддерживают все API.

### <a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>Пакет SDK для .NET версии 2

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/myCustomId",
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-lww-dotnet-v3"></a>Пакет SDK для .NET версии 3

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.lwwCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.LastWriterWins,
            ResolutionPath = "/myCustomId",
        }
    });
```

### <a id="create-custom-conflict-resolution-policy-lww-java-async"></a>Пакет SDK для Java (асинхронная модель)

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-lww-java-sync"></a>Пакет SDK для Java (синхронная модель)

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```

### <a id="create-custom-conflict-resolution-policy-lww-javascript"></a>Пакет SDK для Node.js, JavaScript и TypeScript

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/myCustomId"
    }
  }
);
```

### <a id="create-custom-conflict-resolution-policy-lww-python"></a>Пакет SDK для Python

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'LastWriterWins',
        'conflictResolutionPath': '/myCustomId'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>Создание пользовательской политики устранения конфликтов с помощью хранимой процедуры

В этих примерах показано, как настроить контейнер с пользовательской политикой разрешения конфликтов, использующей хранимую процедуру для решения конфликтов. Эти конфликты не будут отображаться в веб-канале конфликтов, если в хранимой процедуре нет ошибки. После создания политики с использованием контейнера необходимо создать хранимую процедуру. Ниже приведен пример пакета SDK для .NET. Эту политику поддерживает только API Core (SQL).

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>Пример пользовательской хранимой процедуры для устранения конфликтов

Пользовательские хранимые процедуры для устранения конфликтов нужно реализовать с помощью сигнатуры функции, представленной ниже. Имя функции не обязательно должно совпадать с именем, используемым при регистрации хранимой процедуры с использованием контейнера, но такой подход упрощает именование. Ниже описаны параметры, которые нужно реализовать для этой хранимой процедуры.

- **incomingItem.** Элемент вставляется или обновляется в фиксации, в которой возникают конфликты. Имеет значение NULL для операций удаления.
- **existingItem.** Элемент, зафиксированный в текущий момент. Значение NULL не устанавливается при обновлении и устанавливается при вставке или удалении.
- **isTombstone.** Логическое значение, указывающее, конфликтует ли incomingItem с ранее удаленным элементом. При значении true existingItem также имеет значение NULL.
- **conflictingItems.** Массив фиксированной версии всех элементов в контейнере, которые конфликтуют с incomingItem по идентификатору или любым другим свойствам уникального индекса.

> [!IMPORTANT]
> Так же, как и любая хранимая процедура, пользовательская процедура разрешения конфликтов может обращаться к данным с одним ключом секции. Процедура позволяет выполнять любые операции вставки, обновления или удаления для устранения конфликтов.

В этом примере представлена хранимая процедура, которая позволяет устранять конфликты, выбирая наименьшее значение из пути `/myCustomId`.

```javascript
function resolver(incomingItem, existingItem, isTombstone, conflictingItems) {
  var collection = getContext().getCollection();

  if (!incomingItem) {
      if (existingItem) {

          collection.deleteDocument(existingItem._self, {}, function (err, responseOptions) {
              if (err) throw err;
          });
      }
  } else if (isTombstone) {
      // delete always wins.
  } else {
      if (existingItem) {
          if (incomingItem.myCustomId > existingItem.myCustomId) {
              return; // existing item wins
          }
      }

      var i;
      for (i = 0; i < conflictingItems.length; i++) {
          if (incomingItem.myCustomId > conflictingItems[i].myCustomId) {
              return; // existing conflict item wins
          }
      }

      // incoming item wins - clear conflicts and replace existing with incoming.
      tryDelete(conflictingItems, incomingItem, existingItem);
  }

  function tryDelete(documents, incoming, existing) {
      if (documents.length > 0) {
          collection.deleteDocument(documents[0]._self, {}, function (err, responseOptions) {
              if (err) throw err;

              documents.shift();
              tryDelete(documents, incoming, existing);
          });
      } else if (existing) {
          collection.replaceDocument(existing._self, incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      } else {
          collection.createDocument(collection.getSelfLink(), incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      }
  }
}
```

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>Пакет SDK для .NET версии 2

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });

//Create the stored procedure
await clients[0].CreateStoredProcedureAsync(
UriFactory.CreateStoredProcedureUri(this.databaseName, this.udpCollectionName, "resolver"), new StoredProcedure
{
    Id = "resolver",
    Body = File.ReadAllText(@"resolver.js")
});
```

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet-v3"></a>Пакет SDK для .NET версии 3

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.udpCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom,
            ResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver")
        }
    });

await container.Scripts.CreateStoredProcedureAsync(
    new StoredProcedureProperties("resolver", File.ReadAllText(@"resolver.js"))
);
```

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-async"></a>Пакет SDK для Java (асинхронная модель)

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

После создания контейнера создайте хранимую процедуру `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-sync"></a>Пакет SDK для Java (синхронная модель)

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```

После создания контейнера создайте хранимую процедуру `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>Пакет SDK для Node.js, JavaScript и TypeScript

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

После создания контейнера создайте хранимую процедуру `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Пакет SDK для Python

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom',
        'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

После создания контейнера создайте хранимую процедуру `resolver`.

## <a name="create-a-custom-conflict-resolution-policy"></a>Создание политики разрешения конфликтов

В этих примерах показано, как настроить контейнер с пользовательской политикой разрешения конфликтов. Эти конфликты отображаются в веб-канале конфликтов.

### <a id="create-custom-conflict-resolution-policy-dotnet"></a>Пакет SDK для .NET версии 2

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-dotnet-v3"></a>Пакет SDK для .NET версии 3

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.manualCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom
        }
    });
```

### <a id="create-custom-conflict-resolution-policy-java-async"></a>Пакет SDK для Java (асинхронная модель)

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-java-sync"></a>Пакет SDK для Java (синхронная модель)

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```

### <a id="create-custom-conflict-resolution-policy-javascript"></a>Пакет SDK для Node.js, JavaScript и TypeScript

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a id="create-custom-conflict-resolution-policy-python"></a>Пакет SDK для Python

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
    'id': self.manual_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom'
    }
}
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="read-from-conflict-feed"></a>Чтение из веб-канала конфликтов

В этих примерах показано, как выполнять чтение из веб-канала конфликтов контейнера. Сведения о конфликтах отображаются в веб-каналах конфликтов, только если они не устранены автоматически или если используется пользовательская политика устранения конфликтов.

### <a id="read-from-conflict-feed-dotnet"></a>Пакет SDK для .NET версии 2

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

### <a id="read-from-conflict-feed-dotnet-v3"></a>Пакет SDK для .NET версии 3

```csharp
FeedIterator<ConflictProperties> conflictFeed = container.Conflicts.GetConflictIterator();
while (conflictFeed.HasMoreResults)
{
    FeedResponse<ConflictProperties> conflicts = await conflictFeed.ReadNextAsync();
    foreach (ConflictProperties conflict in conflicts)
    {
        // Read the conflicted content
        MyClass intendedChanges = container.Conflicts.ReadConflictContent<MyClass>(conflict);
        MyClass currentState = await container.Conflicts.ReadCurrentAsync<MyClass>(conflict, new PartitionKey(intendedChanges.MyPartitionKey));

        // Do manual merge among documents
        await container.ReplaceItemAsync<MyClass>(intendedChanges, intendedChanges.Id, new PartitionKey(intendedChanges.MyPartitionKey));

        // Delete the conflict
        await container.Conflicts.DeleteAsync(conflict, new PartitionKey(intendedChanges.MyPartitionKey));
    }
}
```

### <a id="read-from-conflict-feed-java-async"></a>Пакет SDK для Java (асинхронная модель)

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-java-sync"></a>Пакет SDK для Java (синхронная модель)

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-javascript"></a>Пакет SDK для Node.js, JavaScript и TypeScript

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Следующие шаги

Узнайте больше о следующих понятиях Azure Cosmos DB.

- [Глобальное распределение (взгляд изнутри)](global-dist-under-the-hood.md)
- [Настройка нескольких источников в приложениях в Azure Cosmos DB](how-to-multi-master.md)
- [Настройка клиентов для поддержки нескольких веб-сайтов](how-to-manage-database-account.md#configure-multiple-write-regions)
- [Добавление и удаление регионов из учетной записи Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
- [How to configure multi-master in your applications](how-to-multi-master.md) (Настройка нескольких источников в приложениях).
- [Секционирование и масштабирование в Azure Cosmos DB](partition-data.md)
- [Индексирование в Azure Cosmos DB](indexing-policies.md)
