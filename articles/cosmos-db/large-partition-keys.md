---
title: Создайте контейнеры Azure Cosmos с большим ключом раздела, используя портал Azure и различные пакеты SDK.
description: Узнайте, как создать контейнер в Azure Cosmos DB с большим ключом раздела, используя портал Azure и различные пакеты SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: a1216daade2df832b606fceb648fca998c3fdec8
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300136"
---
# <a name="create-containers-with-large-partition-key"></a>Создание контейнеров с большим ключом секции

Azure Cosmos DB использует схему секционирования на основе хэша для достижения горизонтального масштабирования данных. Все контейнеры Azure Cosmos, созданные до 3 2019, могут использовать хэш-функцию, которая вычислит хэш на основе первых 100 байт ключа секции. Если имеется несколько ключей разделов, имеющих один и тот же первый 100 байта, то эти логические секции рассматриваются службой как один и тот же логический раздел. Это может привести к проблемам, например к квотам на размер секций, а также к применению уникальных индексов по ключам секций. Для решения этой проблемы введены крупные ключи разделов. Azure Cosmos DB теперь поддерживает большие ключи разделов со значениями до 2 КБ.

Большие ключи разделов поддерживаются с помощью функций улучшенной версии хэш-функции, которая может формировать уникальный хэш-код из больших ключей разделов размером до 2 КБ. Эту версию хэша также рекомендуется использовать для сценариев с высокой кратностью ключа секции независимо от размера ключа секции. Количество элементов ключа секции определяется как число уникальных логических секций, например в порядке ~ 30000 логических секций в контейнере. В этой статье описывается создание контейнера с большим ключом секции с помощью портал Azure и различных пакетов SDK. 

## <a name="create-a-large-partition-key-net-sdk"></a>Создание большого ключа секции (пакет SDK для .NET)

Чтобы создать контейнер с большим ключом секции с помощью пакета SDK для .NET, укажите `PartitionKeyDefinitionVersion.V2` свойство. В следующем примере показано, как указать свойство Version в объекте Партитионкэйдефинитион и задать для него значение Партитионкэйдефинитионверсион. v2.

### <a name="v3-net-sdk"></a>v3 .NET SDK

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2, 
    })
```

### <a name="v2-net-sdk"></a>пакет SDK для .NET v2

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="create-a-large-partition-key-azure-portal"></a>Создание большого ключа секции (портал Azure) 

Чтобы создать большой ключ секции, во время создания контейнера с помощью портал Azure установите флажок **ключ раздела больше 100 байт** . По умолчанию все новые контейнеры участвуют в использовании больших ключей разделов. Снимите флажок, если не требуются ключи разделов большого размера, или если у вас есть приложения, работающие в пакетах SDK более ранних версий, чем 1,18.

![Создание больших ключей разделов с помощью портал Azure](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Создание большого ключа секции (PowerShell)

Чтобы создать контейнер с большим ключом секции с помощью PowerShell, включите `"version" = 2` `partitionKey` для объекта.

```azurepowershell-interactive
# Create a Cosmos SQL API container with large partition key support (version 2)
$resourceGroupName = "myResourceGroup"
$containerName = "mycosmosaccount" + "/sql/" + "myDatabase" + "/" + "myContainer"

# Container with large partition key support (version = 2)
$containerProperties = @{
  "resource"=@{
    "id"=$containerName;
    "partitionKey"=@{
        "paths"=@("/myPartitionKey");
        "kind"="Hash";
        "version" = 2
    };
    "indexingPolicy"=@{
        "indexingMode"="Consistent";
        "includedPaths"= @(@{
            "path"="/*"
        });
        "excludedPaths"= @(@{
            "path"="/myPathToNotIndex/*"
        })
    }
  }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerName -PropertyObject $containerProperties
```

## <a name="supported-sdk-versions"></a>Поддерживаемые версии пакета SDK

Ключи больших разделов поддерживаются со следующими минимальными версиями пакетов SDK:

|Тип пакета SDK  | Минимальная версия   |
|---------|---------|
|.Net     |    1,18     |
|Синхронизация Java     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | версия выше, `2017-05-03` чем с `x-ms-version` помощью заголовка запроса.|

В настоящее время нельзя использовать контейнеры с большим ключом секции в в Power BI и Azure Logic Apps. В этих приложениях можно использовать контейнеры без большого ключа секции.

## <a name="next-steps"></a>Следующие шаги

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Секционирование в Azure Cosmos DB)
* [Единицы запросов в Azure Cosmos DB](request-units.md)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Использование учетной записи Azure Cosmos](account-overview.md)


