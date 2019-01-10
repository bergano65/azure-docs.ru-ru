---
title: Сведения об управлении конфликтами между регионами в Azure Cosmos DB
description: Сведения об управлении конфликтами в Azure Cosmos DB
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 1f06b27868547dd448e3da547dd61b861db913d2
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034783"
---
# <a name="manage-conflicts-between-regions"></a>Управление конфликтами между регионами

Если выполняются операции записи в нескольких регионах, при возникновении конфликта данных его можно разрешить с помощью других политик устранения конфликтов. В этой статье описывается, как управлять политиками разрешения конфликтов с использованием разных языковых платформ.

## <a name="create-a-custom-conflict-resolution-policy"></a>Создание политики разрешения конфликтов

В этих примерах показано, как настроить контейнер с пользовательской политикой разрешения конфликтов. Эти конфликты отображаются в веб-канале конфликтов.

### <a id="create-custom-conflict-resolution-policy-dotnet"></a>Пакет SDK для .NET

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

## <a name="create-a-custom-conflict-resolution-policy-with-a-stored-procedure"></a>Создание пользовательской политики разрешения конфликтов с использованием хранимой процедуры

В этих примерах показано, как настроить контейнер с пользовательской политикой разрешения конфликтов, использующей хранимую процедуру для решения конфликтов. Эти конфликты не будут отображаться в веб-канале конфликтов, если в хранимой процедуре нет ошибки.

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>Пакет SDK для .NET

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
```

После создания контейнера создайте хранимую процедуру `resolver`.

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

```

После создания контейнера создайте хранимую процедуру `resolver`.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Создание политики разрешения конфликтов, реализующей подход "Сохраняются изменения, внесенные последними"

В этих примерах показано, как настроить контейнер с политикой разрешения конфликтов, реализующей подход "Сохраняются изменения, внесенные последними". Если путь не задан или является недопустимым, в качестве пути по умолчанию устанавливается свойство `_ts`. Это свойство представляет собой поле метки времени. Эти конфликты не будут отображаться в веб-канале конфликтов.

### <a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>Пакет SDK для .NET

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/regionId",
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-lww-java-async"></a>Пакет SDK для Java (асинхронная модель)

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy(conflictResolutionPath);
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-lww-java-sync"></a>Пакет SDK для Java (синхронная модель)

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/regionId");
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
      conflictResolutionPath: "/regionId"
    }
  }
);
```

Если вы не указали свойство `conflictResolutionPath`, по умолчанию будет использоваться свойство `_ts`.

### <a id="create-custom-conflict-resolution-policy-lww-python"></a>Пакет SDK для Python

```python
udp_collection = {
                'id': self.udp_collection_name,
                'conflictResolutionPolicy': {
                    'mode': 'Custom',
                    'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
                    }
                }
udp_collection = self.try_create_document_collection(create_client, database, udp_collection)
```

## <a name="read-from-conflict-feed"></a>Чтение из веб-канала конфликтов

В этих примерах показано, как выполнять чтение из веб-канала конфликтов контейнера. Конфликты отображаются в веб-канале конфликтов, только если они не разрешены автоматически.

### <a id="read-from-conflict-feed-dotnet"></a>Пакет SDK для .NET

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
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
Iterator<Conflict> conflictsIterartor = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterartor.hasNext()) {
    Conflict conflict = conflictsIterartor.next();
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
conflicts_iterartor = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterartor, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о следующих понятиях Azure Cosmos DB.

* [Секционирование и масштабирование в Azure Cosmos DB](partition-data.md)
* [Индексирование в Azure Cosmos DB](indexing-policies.md)

