---
title: Оценка запросов с помощью функции профиля выполнения для Azure Cosmos DB API Gremlin
description: Узнайте, как устранять неполадки и улучшать запросы Gremlin с помощью шага профиля выполнения.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: ab5c55105eeb912281f35e3d6094c0c43a76f89a
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915880"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Использование шага "профиль выполнения" для вычисления запросов Gremlin

В этой статье приводятся общие сведения об использовании шага профиля выполнения для баз данных Azure Cosmos DB Gremlin API Graph. Этот шаг предоставляет важные сведения для устранения неполадок и оптимизации запросов, а также совместимость с любым запросом Gremlin, который можно выполнить для учетной записи Cosmos DB Gremlin API.

Чтобы использовать этот шаг, просто добавьте `executionProfile()` вызов функции в конец запроса Gremlin. **Запрос Gremlin будет выполнен** , и результат операции вернет объект ответа JSON с профилем выполнения запроса.

Пример:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

После вызова `executionProfile()` шага ответ будет объектом JSON, который включает в себя выполненный шаг Gremlin, общее время, затраченное на выполнение инструкции, а также массив операторов среды выполнения Cosmos DB, в которых она привела.

> [!NOTE]
> Эта реализация для профиля выполнения не определена в спецификации Apache Tinkerpop. Он относится только к реализации Azure Cosmos DB API Gremlin.


## <a name="response-example"></a>Пример ответа

Ниже приведен пример выходных данных, которые будут возвращены:

> [!NOTE]
> Этот пример снабжен комментариями, поясняющими общую структуру ответа. Фактический ответ Ексекутионпрофиле не будет содержать никаких комментариев.

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
> На шаге Ексекутионпрофиле будет выполнен запрос Gremlin. Сюда входят `addV` шаги или `addE`, что приведет к созданию и зафиксировать изменения, указанные в запросе. В результате будут также оплачены единицы запроса, созданные запросом Gremlin.

## <a name="execution-profile-response-objects"></a>Объекты ответа профиля выполнения

В ответе функции Ексекутионпрофиле () будет выдаваться иерархия объектов JSON со следующей структурой:
  - **Объект операции Gremlin**: Представляет всю выполненную операцию Gremlin. Содержит следующие свойства.
    - `gremlin`. Явная инструкция Gremlin, которая была выполнена.
    - `totalTime`. Время (в миллисекундах) выполнения шага, выполненного в. 
    - `metrics`. Массив, содержащий все операторы Cosmos DB среды выполнения, которые были выполнены для выполнения запроса. Этот список сортируется в порядке выполнения.
    
  - **Cosmos DB операторы среды выполнения**: Представляет каждый из компонентов всей операции Gremlin. Этот список сортируется в порядке выполнения. Каждый объект содержит следующие свойства:
    - `name`. Имя оператора. Это тип этапа, который был оценен и выполнен. Дополнительные сведения см. в таблице ниже.
    - `time`. Количество времени (в миллисекундах), затраченного на выполнение данного оператора.
    - `annotations`. Содержит дополнительные сведения, относящиеся к выполненному оператору.
    - `annotations.percentTime`. Процент общего времени, затраченного на выполнение конкретного оператора.
    - `counts`. Количество объектов, возвращенных этим оператором из уровня хранилища. Он содержится в `counts.resultCount` скалярном значении в.
    - `storeOps`. Представляет операцию с хранилищем, которая может охватывать одну или несколько секций.
    - `storeOps.fanoutFactor`. Представляет число секций, к которым обращается эта операция хранилища.
    - `storeOps.count`. Представляет число результатов, возвращенных этой операцией хранилища.
    - `storeOps.size`. Представляет размер в байтах результата данной операции хранилища.

Cosmos DB оператор среды выполнения Gremlin|Описание
---|---
`GetVertices`| Этот шаг получает заданный набор объектов из уровня сохраняемости. 
`GetEdges`| Этот шаг получает границы, расположенные рядом с набором вершин. Этот шаг может привести к одной или нескольким операциям с хранилищем.
`GetNeighborVertices`| Этот шаг получает вершины, подключенные к набору ребер. Края содержат ключи секций и ИДЕНТИФИКАТОРы их исходной и целевой вершин.
`Coalesce`| На этом шаге выполняется вычисление двух операций при каждом `coalesce()` выполнении шага Gremlin.
`CartesianProductOperator`| Этот шаг выполняет вычисление декартового продукта между двумя наборами данных. Обычно выполняется при каждом использовании `to()` предикатов `from()` или.
`ConstantSourceOperator`| Этот шаг выполняет вычисление выражения для получения значения константы в качестве результата.
`ProjectOperator`| Этот шаг подготавливает и сериализует ответ, используя результат предыдущих операций.
`ProjectAggregation`| Этот шаг подготавливает и сериализует ответ для статистической операции.

> [!NOTE]
> Этот список будет по прежнему обновляться по мере добавления новых операторов.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Примеры анализа ответа профиля выполнения

Ниже приведены примеры распространенных способов оптимизации, которые можно получить с помощью ответа профиля выполнения.
  - Запрос на незакрытый вентилятор.
  - Нефильтрованный запрос.

### <a name="blind-fan-out-query-patterns"></a>Шаблоны запросов слепых вентиляторов

Предположим, что следующий ответ профиля выполнения основан на **секционированном графе**:

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

Из него можно внести следующие выводы:
- Запрос представляет собой единый поиск идентификатора, так как оператор Gremlin соответствует шаблону `g.V('id')`.
- Судя от `time` метрики, задержка этого запроса кажется высокой, так как она [больше 10 мс для одной операции чтения точки](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Если взглянуть `storeOps` на объект, можно увидеть, что имеет `5`значение, `fanoutFactor` а это означает, что эта операция осуществляла доступ к [5 секциям](https://docs.microsoft.com/azure/cosmos-db/partition-data) .

В конце этого анализа мы можем определить, что первый запрос обращается к большему числу секций, чем необходимо. Это можно устранить, указав ключ секционирования в запросе в виде предиката. Это приведет к снижению задержки и снижению затрат на запрос. Узнайте больше о [секционировании графов](graph-partitioning.md). Более оптимальным является запрос `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>Шаблоны запросов без фильтрации

Сравните следующие два ответа профиля выполнения. Для простоты в этих примерах используется один секционированный граф.

Первый запрос получает все вершины с меткой `tweet` , а затем получает соседние вершины:

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

Обратите внимание на профиль того же запроса, но теперь с дополнительным фильтром `has('lang', 'en')`, прежде чем изучать смежные вершины:

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

Эти два запроса достигли одного и того же результата, однако первый из них потребует больше единиц запросов, так как требуется выполнить итерацию более крупного исходного набора данных перед запросом соседних элементов. Индикаторы такого поведения можно увидеть при сравнении следующих параметров из обоих ответов:
- `metrics[0].time` Значение выше в первом ответе, что означает, что этот один шаг занял больше времени для разрешения.
- `metrics[0].counts.resultsCount` Значение больше, чем в первом ответе, что означает, что начальный рабочий набор данных был больше.

## <a name="next-steps"></a>Следующие шаги
* Сведения о [поддерживаемых функциях Gremlin](gremlin-support.md) в Azure Cosmos DB. 
* Дополнительные сведения об [API Gremlin см. в Azure Cosmos DB](graph-introduction.md).
