---
title: Используйте профиль выполнения для оценки запросов в API Azure Cosmos DB Gremlin
description: Узнайте, как устранить неполадки и улучшить запросы Gremlin с помощью шага профиля выполнения.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441852"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>How to use the execution profile step to evaluate your Gremlin queries (Использование шага профиля выполнения для анализа запросов Gremlin)

В этой статье приводятся общие сведения об использовании шага профиля выполнения для API Gremlin для баз данных графов Azure Cosmos DB. На этом шаге предоставляются важные сведения для устранения неполадок и оптимизации запросов, а также обеспечивается совместимость с любым запросом Gremlin, который можно выполнить для учетной записи API Gremlin в Cosmos DB.

Чтобы использовать этот шаг, `executionProfile()` просто прикажите вызов функции в конце запроса Gremlin. **Ваш запрос Gremlin будет выполнен,** и результат операции вернет объект ответа JSON с профилем выполнения запроса.

Пример:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

После вызова `executionProfile()` шага ответом будет объект JSON, который включает в себя выполненный шаг Gremlin, общее время, которое потребовалось, и массив операторов времени выполнения Cosmos DB, в результате чего операторы оператора.

> [!NOTE]
> Эта реализация для профиля выполнения не определена в спецификации Apache Tinkerpop. Он специфичен для реализации API Azure Cosmos DB Gremlin API.


## <a name="response-example"></a>Пример ответа

Ниже приводится аннотированный пример вывода, который будет возвращен:

> [!NOTE]
> Этот пример аннотирован комментариями, объясняющие общую структуру ответа. Фактический ответ executionProfile не будет содержать никаких комментариев.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> Шаг executionProfile выполнит запрос Gremlin. Это включает `addV` `addE`в себя или шаги, которые приведут к созданию и будут совершать изменения, указанные в запросе. В результате будут также взиматься плата за единицы запроса, генерируемые запросом Гремлина.

## <a name="execution-profile-response-objects"></a>Объекты ответа профиля выполнения

Ответ функции executionProfile() даст иерархию объектов JSON со следующей структурой:
  - **Объект операции Gremlin**: Представляет всю операцию Gremlin, которая была выполнена. Содержит следующие свойства.
    - `gremlin`: Явное заявление Гремлина, которое было выполнено.
    - `totalTime`: Время, в миллисекундах, то выполнение шага понесенного in. 
    - `metrics`: Массив, содержащий каждого из операторов времени выполнения Cosmos DB, которые были выполнены для выполнения запроса. Этот список отсортирован в порядке исполнения.
    
  - **Операторы времени выполнения Cosmos DB:** представляет каждый из компонентов всей операции Gremlin. Этот список отсортирован в порядке исполнения. Каждый объект содержит следующие свойства:
    - `name`: Имя оператора. Это тип шага, который был оценен и выполнен. Подробнее читайте в таблице ниже.
    - `time`: Количество времени, в миллисекундах, что данный оператор взял.
    - `annotations`: Содержит дополнительную информацию, специфичную для оператора, который был выполнен.
    - `annotations.percentTime`: Процент от общего времени, которое потребовалось для выполнения конкретного оператора.
    - `counts`: Количество объектов, которые были возвращены из слоя хранилища этим оператором. Это содержится `counts.resultCount` в масштабируемом значении внутри.
    - `storeOps`: Представляет собой операцию хранения, которая может охватывать один или несколько разделов.
    - `storeOps.fanoutFactor`: Представляет количество разделов, к которым была доступна эта конкретная операция хранения.
    - `storeOps.count`: Представляет количество результатов, которые вернула эта операция хранения.
    - `storeOps.size`: Представляет размер байтов результата данной операции хранения.

Оператор космического рынка DB Gremlin Runtime|Описание
---|---
`GetVertices`| Этот шаг получает предопределенный набор объектов из слоя настойчивости. 
`GetEdges`| Этот шаг получает края, которые примыкают к набору вершин. Этот шаг может привести к одной или многим операциям хранения.
`GetNeighborVertices`| Этот шаг получает вершины, которые соединены с набором краев. Края содержат клавиши перегородки и идентификаторы как источника, так и целевых головокружений.
`Coalesce`| Этот шаг учитывает оценку двух операций всякий раз, `coalesce()` когда выполняется шаг Гремлина.
`CartesianProductOperator`| Этот шаг вычисляет декартовый продукт между двумя наборами данных. Обычно выполняется всякий раз, `to()` `from()` когда предикаты или используются.
`ConstantSourceOperator`| Этот шаг вычисляет выражение для получения постоянного значения в результате.
`ProjectOperator`| Этот шаг готовит и сериализирует ответ с использованием результатов предыдущих операций.
`ProjectAggregation`| Этот шаг готовит и сериализирует ответ для агрегированной операции.

> [!NOTE]
> Этот список будет продолжать обновляться по мере добавления новых операторов.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Примеры анализа ответа профиля выполнения

Ниже приведены примеры общих оптимизаций, которые можно увидеть с помощью ответа профиля выполнения:
  - Слепой запрос вентилятора.
  - Нефильтрованный запрос.

### <a name="blind-fan-out-query-patterns"></a>Слепые шаблоны запросов вентилятора

Предположим следующий ответ профиля исполнения от **разделитого графика:**

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

Из него можно сделать следующие выводы:
- Запрос представляет собой единый поиск идентификатора, `g.V('id')`так как заявление Gremlin следует шаблону.
- Судя по `time` метрике, задержка этого запроса кажется высокой, так как это [более 10 мс для одной операции считываний.](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide)
- Если мы посмотрим `storeOps` на объект, `fanoutFactor` мы `5`можем видеть, что есть, что означает, что [5 разделов](https://docs.microsoft.com/azure/cosmos-db/partition-data) были доступны этой операции.

В заключение этого анализа мы можем определить, что первый запрос получает доступ к большему количестве разделов, чем это необходимо. Это можно решить, указав ключ раздела в запросе как предикат. Это приведет к меньшей задержке и меньшей стоимости запроса. Узнайте больше о [секционировании графов](graph-partitioning.md). Более оптимальным запросом `g.V('tt0093640').has('partitionKey', 't1001')`будет .

### <a name="unfiltered-query-patterns"></a>Нефильтрованные шаблоны запросов

Сравните следующие два ответа профиля выполнения. Для простоты в этих примерах используется единый разделительный график.

Этот первый запрос получает все вершины `tweet` с этикеткой, а затем получает их соседние вершины:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Обратите внимание на профиль того же запроса, `has('lang', 'en')`но теперь с дополнительным фильтром, , прежде чем исследовать соседние вершины:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Эти два запроса достигли одного и того же результата, однако для первого потребуется больше единиц запросов, поскольку для выполнения большего исходного набора данных необходимо будет развиться перед запросом на соседние элементы. Мы можем видеть индикаторы такого поведения при сравнении следующих параметров из обоих ответов:
- Значение `metrics[0].time` выше в первом ответе, что указывает на то, что этот единственный шаг занял больше времени, чтобы решить.
- Значение `metrics[0].counts.resultsCount` выше, а также в первом ответе, что указывает на то, что первоначальный рабочий набор данных был больше.

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте об [поддерживаемых функциях Gremlin](gremlin-support.md) в Azure Cosmos DB. 
* Узнайте больше о [API Gremlin в Azure Cosmos DB](graph-introduction.md).
