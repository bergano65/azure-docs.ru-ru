---
title: Работа с датами в Azure Cosmos DB
description: Узнайте, как хранить, индексировать и загонять объекты DataTime в Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 92fa35fbe8e5eef4dbdc8b6c47a9055affd449a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273188"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Работа с датами в Azure Cosmos DB

Azure Cosmos DB обеспечивает гибкость схемы данных и обширные возможности индексирования благодаря использованию собственной модели данных на основе [JSON](https://www.json.org). Все ресурсы Azure Cosmos DB, включая базы данных, контейнеры, документы и хранимые процедуры, моделируются и хранятся в виде документов JSON. Чтобы гарантировать свойство переносимости, JSON (и Azure Cosmos DB) поддерживает только небольшой набор основных типов: строка, число, логическое значение, массив, объект и Null. Но JSON благодаря своей гибкости позволяет разработчикам и платформам моделировать и более сложные типы, составляя их из поддерживаемых примитивов в виде объектов или массивов.

Помимо основных типов многим приложениям требуется формат DateTime для представления дат и отметок времени. В этой статье описывается, как разработчики могут хранить, извлекать и искать даты в Azure Cosmos DB с помощью пакета SDK для .NET.

## <a name="storing-datetimes"></a>Хранение значений даты и времени

Azure Cosmos DB поддерживает типы JSON, такие как - строка, число, булейн, null, массив, объект. Он не поддерживает напрямую тип DateTime. В настоящее время Azure Cosmos DB не поддерживает локализацию дат. Таким образом, вам нужно хранить DateTimes в качестве строк. Рекомендуемый формат строк DateTime в Azure Cosmos DB соответствует `YYYY-MM-DDThh:mm:ss.sssZ` стандарту ISO 8601 UTC. Рекомендуется хранить все даты в Azure Cosmos DB как UTC. Преобразование строк даты в этот формат позволит сортировать даты лексикографически. При хранении дат, не относящихся к UTC, логика должна обрабатываться на стороне клиента. Чтобы преобразовать локальное DateTime в UTC, смещение должно быть известно/сохранено как свойство в JSON, и клиент может использовать смещение для вычисления значения UTC DateTime.

Многим приложениям вполне достаточно используемого по умолчанию строкового представления дат и времени, поскольку оно:

* позволяет сравнивать строки, а следовательно и сохранять относительный порядок значений даты и времени, после преобразования в строки;
* не требует создавать пользовательский код или специальные атрибуты для преобразования JSON;
* сохраняет даты в понятном для человека формате JSON;
* Позволяет воспользоваться преимуществами индексов Azure Cosmos DB для быстрой обработки запросов.

Например, следующий фрагмент кода сохраняет объект `Order`, содержащий два свойства в формате даты и времени (`ShipDate` и `OrderDate`) в виде документа, использующего пакет SDK для .NET.

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });
```

Этот документ хранится в Azure Cosmos DB в следующем виде:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Кроме того, вы можете хранить даты как отметки времени Unix, то есть как число, представляющее количество секунд, прошедших с 1 января 1970 года. Внутреннее свойство Azure Cosmos DB для отметки времени (`_ts`) использует именно этот подход. Для сериализации значений даты и времени в числа используйте класс [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx).

## <a name="querying-datetimes-in-linq"></a>Запрос по датам и времени в LINQ

Пакет SDK .NET для SQL автоматически поддерживает запросы с использованием LINQ к данным, хранящимся в Azure Cosmos DB. Например, на следующем фрагменте отображается запрос LIN', который фильтрует заказы, которые были отправлены за последние три дня:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

В переводе на следующее заявление S'L и выполнено на Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")
```

Вы можете узнать больше о языке запросов Azure Cosmos DB в S'L и поставщике LIN' в [запросе Cosmos DB в LINЗ.](sql-query-linq-to-sql.md)

## <a name="indexing-datetimes-for-range-queries"></a>Индексирование значений даты и времени для запросов в диапазоне

Запросы являются общими для значений DateTime. Для эффективного выполнения этих запросов необходимо определить индекс на любых свойствах в фильтре запроса.

Дополнительные сведения о настройке политик индексирования см. в статье [Как работает индексирование данных в Azure Cosmos DB?](index-policy.md). 

## <a name="next-steps"></a>Next Steps

* Скачайте и запустите [примеры кода, хранящиеся на GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples).
* Дополнительные сведения об [SQL-запросах](sql-query-getting-started.md)
* Дополнительные сведения о политиках индексации в Azure Cosmos DB см. в [этой статье](index-policy.md).
