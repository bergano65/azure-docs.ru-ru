---
title: Транзакционные пакетные операции в Azure Cosmos DB с помощью пакета SDK для .NET
description: Узнайте, как использовать Трансактионалбатч в пакете SDK для .NET Azure Cosmos DB для выполнения групповых операций с успешным или неудачными точками.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: c27eb2b4e0dc0d269e1cffc9e213cebeb93443e2
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328736"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>Транзакционные пакетные операции в Azure Cosmos DB с помощью пакета SDK для .NET

Пакет транзакций описывает группу операций с точками, которые должны либо успешно выполняться, либо завершаться сбоем с одним и тем же ключом секции в контейнере. В пакете SDK `TranscationalBatch` для .NET класс используется для определения этого пакета операций. Если все операции выполняются успешно в том порядке, в котором они описаны в транзакционной пакетной операции, транзакция будет зафиксирована. Однако при сбое любой операции выполняется откат всей транзакции.

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Что такое транзакция в Azure Cosmos DB

Транзакция в типичной базе данных может быть определена как последовательность операций, выполняемых как единая логическая единица работы. Каждая транзакция предоставляет гарантии свойств ACID (атомарность, согласованность, изоляция, устойчивость).

* **Атомарность** гарантирует, что все операции, выполняемые внутри транзакции, обрабатываются как единое целое, и все они фиксируются или ни один из них не равен.
* **Согласованность** гарантирует, что данные всегда находятся в допустимом состоянии в транзакциях.
* **Изоляция** гарантирует, что ни одна из двух транзакций не мешает друг другу — многие коммерческие системы предоставляют несколько уровней изоляции, которые можно использовать в зависимости от потребностей приложения.
* **Устойчивость** гарантирует, что все изменения, зафиксированные в базе данных, всегда будут присутствовать.
Azure Cosmos DB поддерживает [полные транзакции, совместимые с Acid, с изоляцией моментальных снимков](database-transactions-optimistic-concurrency.md) для операций в рамках одного [ключа логической секции](partitioning-overview.md).

## <a name="transactional-batch-operations-vs-stored-procedures"></a>Транзакционные пакетные операции и хранимые процедуры

Azure Cosmos DB в настоящее время поддерживает хранимые процедуры, которые также обеспечивают транзакционную область действия в операциях. Однако транзакционные пакетные операции обеспечивают следующие преимущества:

* **Параметр Language** — пакет транзакций поддерживается в пакете SDK и языке, с которым вы работаете, а хранимые процедуры должны быть написаны на JavaScript.
* **Управление версиями кода** . Управление версиями кода приложения и его адаптация в конвейере CI/CD гораздо более естественны, чем координация обновления хранимой процедуры и проверка того, что операция переключения происходит в нужное время. Это также упрощает откат изменений.
* **Производительность** — сокращает задержку в эквивалентных операциях до 30% по сравнению с выполнением хранимой процедуры.
* **Сериализация содержимого** — каждая операция в транзакционном пакете может использовать настраиваемые параметры сериализации для полезных данных.

## <a name="how-to-create-a-transactional-batch-operation"></a>Создание транзакционной пакетной операции

При создании транзакционной пакетной операции вы начинаете с экземпляра контейнера и вызываете `CreateTransactionalBatch` :

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

Далее необходимо вызвать `ExecuteAsync` :

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

После получения ответа необходимо проверить успешность его выполнения, а также извлечь результаты.

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

В случае сбоя операция с ошибкой будет иметь код состояния соответствующей ошибки. В то время как все остальные операции будут иметь код состояния 424 (неудачная зависимость). В приведенном ниже примере операция завершается ошибкой, так как пытается создать уже существующий элемент (409 HttpStatusCode. конфликт). Коды состояния упрощают поиск причины сбоя транзакции.

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>Как выполняются транзакционные пакетные операции

При `ExecuteAsync` вызове метода все операции в `TransactionalBatch` объекте группируются, сериализуются в одну полезную нагрузку и отправляются как один запрос в службу Azure Cosmos DB.

Служба получает запрос и выполняет все операции в области транзакций и возвращает ответ, используя тот же протокол сериализации. Этот ответ является успешным или неудачным и содержит все отдельные ответы на операции внутри.

Пакет SDK предоставляет ответ для проверки результата и, при необходимости, извлечения всех результатов внутренней операции.

## <a name="limitations"></a>Ограничения

В настоящее время существует два известных ограничения:

* Предельный размер запроса Azure Cosmos DB указывает, что размер `TransactionalBatch` полезных данных не может превышать 2 МБ, а максимальное время выполнения — 5 секунд.
* Существует ограничение в 100 операций для, чтобы обеспечить `TransactionalBatch` ожидаемую производительность и в рамках соглашения об уровне обслуживания.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [том, что можно сделать с помощью трансактионалбатч](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch)

* Ознакомьтесь с нашими [примерами](sql-api-dotnet-v3sdk-samples.md) , чтобы узнать больше о способах использования нашего пакета SDK для Cosmos DB .NET
