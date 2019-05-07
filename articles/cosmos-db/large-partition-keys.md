---
title: Создайте контейнеры Azure Cosmos с большой секции ключа, с помощью портала Azure и различные пакеты SDK.
description: Узнайте, как создать контейнер в Azure Cosmos DB с большой секции ключа, с помощью портала Azure и разные пакеты SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 46df484303237722f4eb66099748f2fcef8240b4
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205834"
---
# <a name="create-containers-with-large-partition-key"></a>Создание контейнеров с помощью ключа большой секции

Чтобы выполнить горизонтальное масштабирование данных в Azure Cosmos DB использует схему секционирования на основе хэша. Все контейнеры Azure Cosmos, созданных до 3 мая 2019 г. Используйте хэш-функции, который вычисляет хэш, основанный на первые 100 байт ключа секции. При наличии нескольких ключей секций с одной первые 100 байт, затем эти логические разделы, считаются как один логический раздел службой. Это может привести к проблемам, например квоты на размер раздела, неправильное и уникальных индексов, применяется по ключам секции. Чтобы устранить эту проблему, вводятся ключей больших секций. Azure Cosmos DB теперь поддерживает ключей больших секций с помощью значения размером до 2 КБ. 

Большая секция с помощью функции улучшенная версия функции хэширования, который может быть создан уникальный хэш из большой секции поддерживаются параметры клавиш до 2 КБ. Эта версия хэша, также рекомендуется для сценариев с высокой секции ключа количеством элементов вне зависимости от размера ключа секции. Количество элементов ключа секции определяется как число уникальных логических разделов, например порядке ~ 30000 логические разделы в контейнере. В этой статье описывается, как создать контейнер с большой секции ключ с помощью портала Azure и разные пакеты SDK. 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>Создайте ключ большой секции (.Net SDK версии 2)

При использовании пакета SDK для .net для создания контейнера с ключом большая секция, следует указать `PartitionKeyDefinitionVersion.V2` свойство. Приведенный ниже показано, как указать версию свойства в объекте PartitionKeyDefinition и присвойте ему значение PartitionKeyDefinitionVersion.V2:

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


## <a name="supported-sdk-versions"></a>Поддерживаемые версии пакета SDK

Поддерживаются следующие минимальные версии пакетов SDK для ключей больших секций:

|Тип пакета SDK  | Минимальная версия   |
|---------|---------|
|.Net     |    1.18     |
|Синхронизация Java     |   2.4.0      |
|Java Async   |  2.5.0        |
 
## <a name="next-steps"></a>Дальнейшие действия

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Секционирование в Azure Cosmos DB)
* [Единицы запросов в Azure Cosmos DB](request-units.md)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Использование учетной записи Azure Cosmos](account-overview.md)


