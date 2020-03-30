---
title: Создание контейнеров Azure Cosmos с большим ключом раздела
description: Узнайте, как создать контейнер в Azure Cosmos DB с большим ключом раздела с помощью портала Azure и различных SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7184a6b85e93c41dfe914813301a4b1a0c88f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887688"
---
# <a name="create-containers-with-large-partition-key"></a>Создание контейнеров с большим ключом раздела

Azure Cosmos DB использует схему расставания на основе хэша для достижения горизонтального масштабирования данных. Во всех контейнерах Azure Cosmos, созданных до 3 мая 2019 года, используется функция хэша, которая вычисляет хэш на основе первых 100 байтов ключа раздела. Если есть несколько ключей раздела, которые имеют те же первые 100 байтов, то эти логические разделы рассматриваются как один и тот же логический раздел службой. Это может привести к тому, что такие проблемы, как квота размера раздела, будут неправильными, а уникальные индексы будут применяться через клавиши раздела. Для решения этой проблемы вводятся большие ключи раздела. Azure Cosmos DB теперь поддерживает большие клавиши раздела со значениями до 2 кБ.

Большие клавиши перегородки поддерживаются с помощью функциональности улучшенной версии функции хэша, которая может генерировать уникальный хэш из больших ключей перегородки до 2 КБ. Эта хэш-версия также рекомендуется для сценариев с высокой кардинальностью ключа раздела, независимо от размера ключа раздела. Кардинальность ключа раздела определяется как количество уникальных логических разделов, например в порядке логических разделов в контейнере. В этой статье описывается, как создать контейнер с большим ключом раздела с помощью портала Azure и различных SDK.

## <a name="create-a-large-partition-key-azure-portal"></a>Создание большого ключа раздела (портал Azure)

Чтобы создать большой ключ раздела при создании нового контейнера с помощью портала Azure, проверьте, что **ключ раздела «Моя часть» больше, чем опция «100 байт».** Не выберите флажок, если вам не нужны большие клавиши раздела или если у вас есть приложения, работающие на версии SDKs старше 1.18.

![Создание больших ключей раздела с помощью портала Azure](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Создание большого ключа раздела (PowerShell)

Для создания контейнера с большой поддержкой ключа раздела см.,

* [Создание контейнера Azure Cosmos с большим размером ключа раздела](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Создание большого ключа раздела (.Net SDK)

Чтобы создать контейнер с большим ключом раздела с помощью `PartitionKeyDefinitionVersion.V2` .NET SDK, укажите свойство. В следующем примере показано, как указать свойство версии в объекте PartitionKeyDefinition и установить его на разделKeyDefinitionVersion.V2.

### <a name="v3-net-sdk"></a>v3 .NET SDK

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>v2 .NET SDK

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

## <a name="supported-sdk-versions"></a>Поддерживаемые версии пакета SDK

Ключи большой перегородки поддерживаются следующими минимальными версиями SDK:

|Тип пакета SDK  | Минимальная версия   |
|---------|---------|
|.Net     |    1.18     |
|Синхронизация Java     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | версия выше, `2017-05-03` чем `x-ms-version` с помощью заголовка запроса.|
| Шаблон Resource Manager | версия 2 с `"version":2` использованием `partitionKey` свойства внутри объекта. |

В настоящее время нельзя использовать контейнеры с большим ключом раздела внутри в Power BI и Azure Logic Apps. Контейнеры можно использовать без большого ключа раздела из этих приложений.

## <a name="next-steps"></a>Дальнейшие действия

* [Секционирование в базе данных Azure Cosmos DB](partitioning-overview.md)
* [Запросные подразделения в Azure Cosmos DB](request-units.md)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Использование учетной записи Azure Cosmos](account-overview.md)
