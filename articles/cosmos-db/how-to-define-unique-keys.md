---
title: Определение уникальных ключей для контейнера Azure Cosmos
description: Узнайте, как определить уникальные ключи для контейнера Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: thweiss
ms.openlocfilehash: 2f61afd12a50b1b135419585bfada52d7ef98f3a
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811643"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Определение уникальных ключей для контейнера Azure Cosmos

В этой статье описаны различные способы определения [уникальных ключей](unique-keys.md) при создании контейнера Azure Cosmos. Сейчас выполнить эту операцию можно с помощью портала Azure или одного из пакетов SDK.

## <a name="use-the-azure-portal"></a>Использование портала Azure

1. Войдите на [портале Azure](https://portal.azure.com/).

1. [Создайте учетную запись Azure Cosmos](create-sql-api-dotnet.md#create-account) или выберите имеющуюся.

1. Откройте панель **обозревателя данных** и выберите контейнер, с которым собираетесь работать.

1. Щелкните **Создать контейнер**.

1. В диалоговом окне **Добавить контейнер** щелкните **+ Add unique key** (+ Добавить уникальный ключ), чтобы добавить запись уникального ключа.

1. Введите пути к ограничению уникального ключа.

1. При необходимости добавьте больше записей уникального ключа. Для этого щелкните **+ Add unique key** (+ Добавить уникальный ключ).

    ![Снимок экрана записи ограничения уникального ключа на портале Azure](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-powershell"></a>С использованием PowerShell

Чтобы создать контейнер с уникальными ключами, см. раздел [Создание контейнера Azure Cosmos с уникальным ключом и TTL](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="use-the-net-sdk-v2"></a>Использование пакета SDK для .NET версии 2

При создании контейнера с помощью [пакета SDK для .NET версии 2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) объект `UniqueKeyPolicy` можно использовать для определения ограничений уникального ключа.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-net-sdk-v3"></a>Использование пакета SDK для .NET v3

При создании нового контейнера с помощью [пакета SDK для .NET v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)используйте API Fluent пакета SDK, чтобы объявить уникальные ключи в кратком и удобочитаемом виде.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Использование пакета SDK для Java

При создании контейнера с помощью [пакета SDK для Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) объект `UniqueKeyPolicy` можно использовать для определения ограничений уникального ключа.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Использование пакета SDK для Node.js

При создании контейнера с помощью [пакета SDK для Node.js](https://www.npmjs.com/package/@azure/cosmos) объект `UniqueKeyPolicy` можно использовать для определения ограничений уникального ключа.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>Использование пакета SDK для Python

При создании контейнера с помощью [пакета SDK для Python](https://pypi.org/project/azure-cosmos/) ограничения уникального ключа могут быть указаны как часть словаря в виде параметра.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>Следующие шаги

- Подробнее о [секционировании](partition-data.md)
- [Как работает индексация](index-overview.md)
