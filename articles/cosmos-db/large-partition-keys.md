---
title: Создайте контейнеры Azure Cosmos с большой секции ключа, с помощью портала Azure и различные пакеты SDK.
description: Узнайте, как создать контейнер в Azure Cosmos DB с большой секции ключа, с помощью портала Azure и разные пакеты SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: bd1697378e5db0432d181f9f688ccc2468b306e7
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566019"
---
# <a name="create-containers-with-large-partition-key"></a>Создание контейнеров с помощью ключа большой секции

Чтобы выполнить горизонтальное масштабирование данных в Azure Cosmos DB использует схему секционирования на основе хэша. Все контейнеры Azure Cosmos, созданных до 3 мая 2019 г. Используйте хэш-функции, который вычисляет хэш, основанный на первые 100 байт ключа секции. При наличии нескольких ключей секций с одной первые 100 байт, затем эти логические разделы, считаются как один логический раздел службой. Это может привести к проблемам, например квоты на размер раздела, неправильное и уникальных индексов, применяется по ключам секции. Чтобы устранить эту проблему, вводятся ключей больших секций. Azure Cosmos DB теперь поддерживает ключей больших секций с помощью значения размером до 2 КБ.

Большая секция с помощью функции улучшенная версия функции хэширования, который может быть создан уникальный хэш из большой секции поддерживаются параметры клавиш до 2 КБ. Эта версия хэша, также рекомендуется для сценариев с высокой секции ключа количеством элементов вне зависимости от размера ключа секции. Количество элементов ключа секции определяется как число уникальных логических разделов, например порядке ~ 30000 логические разделы в контейнере. В этой статье описывается, как создать контейнер с большой секции ключ с помощью портала Azure и разные пакеты SDK. 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>Создайте ключ большой секции (.Net SDK версии 2)

Чтобы создать контейнер с большой секции ключ с помощью пакета SDK для .NET, укажите `PartitionKeyDefinitionVersion.V2` свойство. Приведенный ниже показано, как указать версию свойства в объекте PartitionKeyDefinition и присвойте ему значение PartitionKeyDefinitionVersion.V2.

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

## <a name="create-a-large-partition-key-azure-portal"></a>Создайте ключ большой секции (портал Azure) 

Для создания ключа большой секции, хотя создать контейнер с помощью портала Azure, установите флажок **Мой ключ секции — более 100 байт** параметр. По умолчанию все новые контейнеры участвуют в с помощью ключей больших секций. Снимите флажок, если вам не требуется ключей больших секций или при наличии приложений, выполняющихся на пакеты SDK версии старше 1.18.

![Создание ключей больших секций с помощью портала Azure](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Создайте ключ большой секции (PowerShell)

Чтобы создать контейнер с ключом большой секции с помощью PowerShell, включите `"version" = 2` для `partitionKey` объекта.

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

Поддерживаются следующие минимальные версии пакетов SDK для ключей больших секций:

|Тип пакета SDK  | Минимальная версия   |
|---------|---------|
|.Net     |    1.18     |
|Синхронизация Java     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | версии выше, чем `2017-05-03` с помощью `x-ms-version` заголовка запроса.|

В настоящее время нельзя использовать контейнеры с ключом большой секции в в Power BI и Azure Logic Apps. Можно использовать контейнеры без ключа секции большое из этих приложений.

## <a name="next-steps"></a>Дальнейшие действия

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Секционирование в Azure Cosmos DB)
* [Единицы запросов в Azure Cosmos DB](request-units.md)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Использование учетной записи Azure Cosmos](account-overview.md)


