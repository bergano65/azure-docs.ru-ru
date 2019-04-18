---
title: Оцените запросы с функцией профиль выполнения для Azure Cosmos DB Gremlin API
description: Узнайте, как для устранения неполадок и улучшить с помощью шаг профиля выполнения запросов Gremlin.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 2f3967c64e79b2bc7b01b35eff26f5ac0d4e3db4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288611"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Как использовать шаг профиля выполнения для оценки запросов Gremlin

В этой статье Общие сведения об использовании шаг профиля выполнения для баз данных Azure Cosmos DB Gremlin API graph. Здесь приводятся важные сведения для устранения неполадок и оптимизации запросов и он совместим с любой запрос Gremlin, который может вызываться для учетной записи Cosmos DB Gremlin API.

Чтобы использовать этот шаг, просто добавьте `executionProfile()` вызов в конце запроса Gremlin функции. **Выполняется запрос Gremlin** и результат операции будет возвращать объект ответа JSON с профилем выполнения запроса.

Например: 

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

После вызова метода `executionProfile()` шаг, ответ будет объект JSON, включающий выполненного шага Gremlin, общее время, затраченное и массив операторы среды выполнения Cosmos DB, которые привели к инструкции.

> [!NOTE]
> Эта реализация для профиля выполнения не определен в спецификации Apache Tinkerpop. Он относится только к API Azure Cosmos DB Gremlin в реализации.


## <a name="response-example"></a>Пример ответа

Ниже приведен пример выходных данных, будет возвращено:

> [!NOTE]
> В этом примере помечается комментарии с объяснениями Общая структура ответа. Ответ фактическое executionProfile не будет содержать никаких комментариев.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation obtains a set of Edge objects. Depending on the query, these might be directly adjacent to a set of vertices, or separate, in the case of an E() query.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation represents the serialization and preparation for a result from the preceding graph operations.
        // The metrics include: time, percentTime of total execution time and resultCount.
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
> На шаге executionProfile выполняет запрос Gremlin. Сюда входят `addV` или `addE`шаги, которые приведут к созданию и будет фиксировать изменения, указанные в запросе. В результате будет также взиматься единиц запросов, созданный запросом Gremlin.

## <a name="execution-profile-response-objects"></a>Объекты ответа профиля выполнения

Ответ функции executionProfile() даст иерархии объектов JSON со следующей структурой:
  - **Объект операции gremlin**: Представляет всей операции Gremlin, который был выполнен. Содержит следующие свойства.
    - `gremlin`: Явная инструкция Gremlin, был выполнен.
    - `totalTime`: Время, в миллисекундах, за выполнение шага. 
    - `metrics`: Массив, содержащий операторов среды выполнения Cosmos DB, которые были выполнены для выполнения запроса. Этот список отсортирован в порядке выполнения.
    
  - **Операторы среды выполнения Cosmos DB**: Представляет каждый из компонентов всей операции Gremlin. Этот список отсортирован в порядке выполнения. Каждый объект содержит следующие свойства:
    - `name`: Имя оператора. Это тип шага, который был рассчитываются и исполняются. Дополнительные сведения в следующей таблице.
    - `time`: Количество времени в миллисекундах, затраченное данного оператора.
    - `annotations`: Содержит дополнительные сведения, относящиеся к оператор, который был выполнен.
    - `annotations.percentTime`: Процент от общего времени, затраченное на выполнение конкретного оператора.
    - `counts`: Количество объектов, которые были возвращены из уровня хранилища от данного оператора. Она хранится в `counts.resultCount` скалярное значение в пределах.
    - `storeOps`: Представляет операцию хранилища, которая может охватывать одну или несколько секций.
    - `storeOps.fanoutFactor`: Представляет число секций, к которым эта операция хранилища.
    - `storeOps.count`: Представляет количество результатов, возврат из операции хранилища.
    - `storeOps.size`: Представляет размер в байтах результата операции заданного хранилища.

Оператор среды выполнения Cosmos DB Gremlin|ОПИСАНИЕ
---|---
`GetVertices`| Этот шаг получает набор объектов, сопоставление из уровня сохраняемости. 
`GetEdges`| Этот шаг получает края, образующие располагаются по бокам набора вершин. Этот шаг может вызвать одну или несколько операций в хранилище.
`GetNeighborVertices`| Этот шаг получает вершины, которые подключены к набору границ. Краев содержит секцию клавиш и идентификаторы их исходные и целевые вершин.
`Coalesce`| Этот шаг учетных записей для вычисления двух операций каждый раз, когда `coalesce()` выполняется шаг Gremlin.
`CartesianProductOperator`| Этот шаг вычисляет декартово произведение между двумя наборами данных. Обычно выполняется каждый раз, когда предикаты `to()` или `from()` используются.
`ConstantSourceOperator`| Этот шаг вычисляет выражение и выдает в результате значение константы.
`ProjectOperator`| Этот шаг подготавливает и выполняет сериализацию ответа, используя результат предыдущих операций.
`ProjectAggregation`| Этот шаг подготавливает и выполняет сериализацию ответа для статистическая операция.

> [!NOTE]
> Этот список будет обновляться по мере добавления новых операторов.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Примеры по анализу ответ профиля выполнения

Ниже приведены примеры распространенных оптимизации, которые может быть замечена в профиле выполнения ответа:
  - Людей с нарушениями зрения развертываемый запрос.
  - Без фильтрации запроса.

### <a name="blind-fan-out-query-patterns"></a>Шаблоны запросов размножения людей с нарушениями зрения

Предположим, следующий ответ профиль выполнения от **секционированного графа**:

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
- Запрос является одной идентификатор операции поиска, так как инструкция Gremlin соответствует шаблону `g.V('id')`.
- Если судить по `time` метрики, задержка запроса кажется, что должна быть высокой, так как это [более чем 10 мс для одной операции чтения точки](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Просматривая `storeOps` объекта, мы видим, что `fanoutFactor` — `5`, означающее, что [5 разделов](https://docs.microsoft.com/azure/cosmos-db/partition-data) обращается к этой операции.

В заключение этого анализа мы можем определить, что первый запрос обращается к больше секций, чем необходимо. Это можно обратиться, указав ключ секционирования в качестве предиката запроса. Это приведет к задержке меньше и меньше затрат на запрос. Дополнительные сведения о [секционирование данных graph](graph-partitioning.md). Более оптимальный запроса будет `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>Шаблоны запросов без фильтра

Сравните следующие ответы два выполнения профиля. Для простоты в этих примерах используется единый секционированного графа.

Этот первый запрос получает все вершины с меткой `tweet` и затем получает их соседних вершин:

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

Обратите внимание, что профиль, тот же запрос, но теперь с помощью дополнительного фильтра, `has('lang', 'en')`, прежде чем просмотреть соседних вершин:

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

Оба запроса достигнут тот же результат, однако первый из них потребуется больше единиц запроса, так как он требуется для выполнения итерации начального набора данных большего размера, перед выполнением запроса соседними элементами. Мы видим индикаторы такого поведения, при сравнении из обоих ответов на следующие параметры:
- `metrics[0].time` Значение больше, в первом ответе, который указывает, что это один шаг заняло больше времени, чтобы устранить.
- `metrics[0].counts.resultsCount` Значение больше, а также в первый ответ, который указывает, что исходный рабочий набор данных большего размера.

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о [поддерживаемые функции Gremlin](gremlin-support.md) в Azure Cosmos DB. 
* Дополнительные сведения о [API Gremlin в Azure Cosmos DB](graph-introduction.md).
