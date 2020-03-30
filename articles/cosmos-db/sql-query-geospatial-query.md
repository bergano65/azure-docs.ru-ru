---
title: Запрос геопространственных данных с помощью Azure Cosmos DB
description: Запрос пространственных данных с помощью Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 08b12bd9d35aaa61c79d35a55068983cdc0f1b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77566326"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Запрос геопространственных данных с помощью Azure Cosmos DB

В этой статье будет освещаться, как задаваемые данные по геопространственным данным в Azure Cosmos DB с помощью S'L и LIN. В настоящее время хранение и доступ к геопространственным данным поддерживается только учетными записями API Azure Cosmos DB S'L API. Azure Cosmos DB поддерживает следующие встроенные функции Открытого геопространственного консорциума (OGC) для выполнения запросов к геопространственным данным. Для получения более подробной информации о полном наборе [Query System Functions in Azure Cosmos DB](sql-query-system-functions.md)встроенных функций на языке S'L см.

## <a name="spatial-sql-built-in-functions"></a>Встроенные функции для обработки пространственных данных в SQL

Вот список функций геопространственной системы, полезных для запросов в Azure Cosmos DB:

|**Использования**|**Описание**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Возвращает расстояние между двумя выражениями точек GeoJSON, многоугольников или объектов LineString.|
|ST_WITHIN (spatial_expr, spatial_expr) | Возвращает логическое выражение, указывающее, располагается ли первый объект GeoJSON (точка, многоугольник или LineString) внутри второго объекта GeoJSON (точка, многоугольник или LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Возвращает логическое выражение, указывающее, пересекаются ли два объекта GeoJSON (точка, многоугольник или LineString).|
|ST_ISVALID| Возвращает логическое значение, указывающее, является ли действительным выражение GeoJSON (точка, многоугольник или LineString).|
| ST_ISVALIDDETAILED| Возвращает значение JSON, содержащее значение Boolean, если указанное выражение GeoJSON Point, Polygon или LineString является действительным. Если он недействителен, он возвращает причину в качестве значения строки.|

Пространственные функции могут использоваться для выполнения запросов близости к пространственным данным. Например, вот запрос, который возвращает все семейные документы, которые находятся `ST_DISTANCE` в пределах 30 км от указанного местоположения с помощью встроенной функции.

**Запроса**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Результаты**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

При включении пространственного индексирования в политику индексирования "запросы для определения расстояния" будут эффективно обрабатываться с помощью индекса. Для получения дополнительной информации о пространственной [индексации](sql-query-geospatial-index.md)см. Если у вас нет пространственного индекса для указанных путей, запрос сделает сканирование контейнера.

`ST_WITHIN`может быть использован для проверки, если точка находится в полигоне. Многоугольники обычно используются для представления границ, например почтовых зон, границ штатов или границ природных образований. При включении пространственного индексирования в политику индексирования "запросы нахождения внутри" будут эффективно обрабатываться с помощью индекса.

Полигон аргументы в `ST_WITHIN` может содержать только одно кольцо, то есть, полигоны не должны содержать отверстия в них.

**Запроса**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon',
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Результаты**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> По аналогии с несоответствием типов в запросе Azure Cosmos DB: если значение местоположения, указанное в любом из аргументов, имеет неправильный формат или является недопустимым, то оно приравнивается к значению **не определено** и соответствующий документ исключается из результатов запроса. Если запрос не возвращает результатов, запустите, `ST_ISVALIDDETAILED` чтобы отладить, почему пространственный тип недействителен.
>
>

Azure Cosmos DB поддерживает также выполнение обратных запросов. Это означает, что вы можете индексировать многоугольники или линии в Azure Cosmos DB, а затем выполнять запросы к областям, которые содержат указанную точку. Этот шаблон часто используется в логистике, например, чтобы определить, когда грузовик прибывает в указанную область или выезжает из нее.

**Запроса**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)
```

**Результаты**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID`и `ST_ISVALIDDETAILED` может быть использован для проверки того, является ли пространственный объект допустимом. Например, следующий запрос проверяет действительность точки, значение широты для которой выходит за пределы допустимого диапазона (-132.8). `ST_ISVALID`возвращает только значение Boolean, и `ST_ISVALIDDETAILED` возвращает Boolean и строку, содержащую причину, почему он считается недействительным.

**Запроса**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Результаты**

```json
    [{
      "$1": false
    }]
```

Эти функции также могут использоваться для проверки многоугольников. Например, здесь `ST_ISVALIDDETAILED` мы используем для проверки не закрытого полигона.

**Запроса**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Результаты**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>Запрос НА ВОПРОС lin's в .NET SDK

В пакете SDK для .NET SQL также содержатся заглушки методов `Distance()` и `Within()` для использования с помощью выражений LINQ. Поставщик LINQ SQL преобразует эти вызовы методов в эквивалентные вызовы встроенных функций SQL (ST_DISTANCE и ST_WITHIN соответственно).

Вот пример запроса LIN'а, который находит все документы в `location` контейнере Azure Cosmos, значение которого находится в радиусе 30 км от указанной точки с помощью LIN's.

**Запросы LINQ для определения расстояния**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Аналогичным образом, вот запрос для поиска всех `location` документов, которые находятся в пределах указанного окна / полигона.

**Запросы LINQ для определения принадлежности**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы ознакомились с предварительными сведениями о поддержке геопространственных данных в Azure Cosmos DB, вы можете сделать следующее:

* Дополнительные сведения о запросах Azure Cosmos DB см. [здесь](sql-query-getting-started.md).
* Узнайте больше о [геопространственных данных и данных о местоположении GeoJSON в Azure Cosmos DB](sql-query-geospatial-intro.md)
* Подробнее о [пространственных данных индекса с помощью Azure Cosmos DB](sql-query-geospatial-index.md)
