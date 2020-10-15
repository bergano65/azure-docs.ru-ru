---
title: Запрос данных о графе двойника
titleSuffix: Azure Digital Twins
description: Дополнительные сведения см. в статье запрос к графу двойников двойника в Azure Digital.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 127fd9a9e47a85479018524998e33f44b0a65ba8
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078482"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Запрос к графу Azure Digital двойников двойника

В этой статье приведены примеры и дополнительные сведения об использовании [языка запросов Azure Digital двойников](concepts-query-language.md) для запроса информации о [двойника графе](concepts-twins-graph.md) . Запросы выполняются на графе с помощью [**API-интерфейсов запросов**](how-to-use-apis-sdks.md)двойников Azure Digital.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

В оставшейся части этой статьи приведены примеры использования этих операций.

## <a name="query-syntax"></a>Синтаксис запросов

В этом разделе содержатся примеры запросов, иллюстрирующие структуру языка запросов и выполняющие возможные операции с [цифровыми двойников](concepts-twins-graph.md).

### <a name="show-all-existing-digital-twins"></a>Показывать все существующие цифровые двойников

Ниже приведен базовый запрос, который возвращает список всех цифровых двойников в экземпляре:

```sql
SELECT *
FROM DIGITALTWINS
```

### <a name="select-top-items"></a>Выбрать верхние элементы

С помощью предложения можно выбрать несколько "верхних" элементов в запросе `Select TOP` .

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

### <a name="count-items"></a>Количество элементов

Количество элементов в результирующем наборе можно подсчитать с помощью `Select COUNT` предложения:

```sql
SELECT COUNT() 
FROM DIGITALTWINS
``` 

Добавьте `WHERE` предложение для подсчета количества элементов, соответствующих определенным критериям. Ниже приведены некоторые примеры инвентаризации с примененным фильтром на основе типа модели двойника (Дополнительные сведения об этом синтаксисе см. в разделе [*запрос по модели*](#query-by-model) ниже).

```sql
SELECT COUNT() 
FROM DIGITALTWINS 
WHERE IS_OF_MODEL('dtmi:sample:Room;1') 
SELECT COUNT() 
FROM DIGITALTWINS c 
WHERE IS_OF_MODEL('dtmi:sample:Room;1') AND c.Capacity > 20
```

Также можно использовать `COUNT` вместе с `JOIN` предложением. Ниже приведен запрос, который подсчитывает все лампочки, содержащиеся в светлых панелях комнат 1 и 2:

```sql
SELECT COUNT()  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2'] 
```

### <a name="specify-return-set-with-projections"></a>Указание возвращаемого набора с проекциями

С помощью проекций можно выбрать, какие столбцы будут возвращены запросом. 

>[!NOTE]
>В настоящее время сложные свойства не поддерживаются. Чтобы обеспечить допустимость свойств проекции, объедините проекции с помощью `IS_PRIMITIVE` проверки. 

Ниже приведен пример запроса, который использует проекцию для возврата двойников и связей. Следующий запрос проецирует *потребитель*, *фабрику* и *ребро* из сценария, в котором *фабрика* с идентификатором *ABC* связана с *потребителем* через связь *фабрики. Customer*, и эта связь представлена в качестве *границы*.

```sql
SELECT Consumer, Factory, Edge 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
```

Можно также использовать проекцию для возвращения свойства двойника. Следующий запрос проецирует свойство *Name* объектов- *получателей* , связанных с *фабрикой* , с идентификатором *ABC* с помощью связи *фабрики. Customer*. 

```sql
SELECT Consumer.name 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
AND IS_PRIMITIVE(Consumer.name)
```

Можно также использовать проекцию для возврата свойства связи. Как и в предыдущем примере, следующий запрос проецирует свойство *Name* *потребителей* , связанных с *фабрикой* , с идентификатором *ABC* через связь *Factory. Customer*; но теперь он также возвращает два свойства этой связи: *Prop1* и *Prop2*. Это достигается путем именования *границы* связи и сбора ее свойств.  

```sql
SELECT Consumer.name, Edge.prop1, Edge.prop2, Factory.area 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

Можно также использовать псевдонимы для упрощения запросов с помощью проекции.

Следующий запрос выполняет те же операции, что и предыдущий пример, но присваивает имена свойств `consumerName` , `first` `second` и `factoryArea` . 
 
```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)" 
```

Вот похожий запрос, который запрашивает тот же набор, что и приведенный выше, но проецирует только свойство *Consumer.Name* как и `consumerName` проецирует готовую *фабрику* как двойника. 

```sql
SELECT Consumer.name AS consumerName, Factory 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) 
```

### <a name="query-by-property"></a>Запрос по свойству

Получение цифровых двойников по **свойствам** (включая идентификатор и метаданные):
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> Идентификатор цифрового двойника запрашивается с помощью поля метаданных `$dtId` .

Вы также можете получить двойников в зависимости от **того, определено ли определенное свойство**. Ниже приведен запрос, который получает двойников с определенным свойством *Location* :

```sql
SELECT *
FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

Это может помочь получить двойников по их свойствам *тегов* , как описано в разделе [Добавление тегов в Digital двойников](how-to-use-tags.md). Ниже приведен запрос, который получает все двойников, помеченные *красным цветом*:

```sql
select * from digitaltwins where is_defined(tags.red) 
```

Можно также получить двойников на основе **типа свойства**. Ниже приведен запрос, который получает двойников, свойство *температуры* которого является числом:

```sql
SELECT * FROM DIGITALTWINS T
WHERE IS_NUMBER(T.Temperature)
```

### <a name="query-by-model"></a>Запрос по модели

`IS_OF_MODEL`Оператор можно использовать для фильтрации на основе [**модели**](concepts-models.md)двойника. Он поддерживает наследование и имеет несколько параметров перегрузки.

Простейший способ использования `IS_OF_MODEL` принимает только `twinTypeName` параметр: `IS_OF_MODEL(twinTypeName)` .
Ниже приведен пример запроса, который передает значение в этом параметре:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1')
```

Чтобы указать коллекцию двойника для поиска при наличии нескольких элементов (например, если `JOIN` используется), добавьте `twinCollection` параметр: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Ниже приведен пример запроса, который добавляет значение для этого параметра:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1')
```

Чтобы выполнить точное соответствие, добавьте `exact` параметр: `IS_OF_MODEL(twinTypeName, exact)` .
Ниже приведен пример запроса, который добавляет значение для этого параметра:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1', exact)
```

Можно также передать все три аргумента вместе: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Ниже приведен пример запроса, в котором указывается значение для всех трех параметров:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1', exact)
```

### <a name="query-based-on-relationships"></a>Запрос на основе связей

При выполнении запросов на основе **отношений**Digital двойников язык запросов Azure Digital двойников имеет специальный синтаксис.

Связи извлекаются в область запроса в `FROM` предложении. Важное отличие от "классических" языков SQL-типов заключается в том, что каждое выражение в этом `FROM` предложении не является таблицей; вместо этого `FROM` предложение выражает прохождение межсущностного отношения и записывается с помощью версии Azure Digital двойников `JOIN` . 

Вспомним, что с возможностями [модели](concepts-models.md) цифровых двойников Azure отношения не существуют независимо от двойников. Это означает, что язык запросов Azure Digital двойников `JOIN` немного отличается от общего SQL `JOIN` , так как связи здесь нельзя запрашивать отдельно и должны быть привязаны к двойника.
Чтобы включить это различие, в `RELATED` предложении используется ключевое слово `JOIN` для ссылки на набор связей двойника. 

В следующем разделе приведены несколько примеров того, как это выглядит.

> [!TIP]
> По сути, эта функция имитирует функции CosmosDB, ориентированные на документы, где `JOIN` их можно выполнять с дочерними объектами в документе. CosmosDB использует `IN` ключевое слово, чтобы указать, что `JOIN` предназначено для итерации элементов массива в текущем контексте документа.

#### <a name="relationship-based-query-examples"></a>Примеры запросов на основе связей

Чтобы получить набор данных, включающий связи, используйте одну `FROM` инструкцию, за которой следует оператор N `JOIN` , где `JOIN` Операторы выражают связи с результатом предыдущей `FROM` `JOIN` инструкции или.

Ниже приведен пример запроса на основе связей. Этот фрагмент кода выбирает все цифровые двойников со свойством *ID* "ABC", а все цифровые двойников, связанные с этими цифровыми двойников через отношение *Contains* . 

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC' 
```

>[!NOTE] 
> Разработчику не нужно сопоставлять этот `JOIN` объект со значением ключа в `WHERE` предложении (или указать значение ключа с `JOIN` определением). Эта корреляция автоматически вычисляются системой, так как сами свойства связи определяют целевую сущность.

#### <a name="query-the-properties-of-a-relationship"></a>Запрос свойств связи

Аналогично тому, как цифровые двойников имеют свойства, описанные в ДТДЛ, связи могут также иметь свойства. Вы можете запросить двойников **в зависимости от свойств их отношений**.
Язык запросов Azure Digital двойников позволяет фильтровать и проекцию связей путем назначения псевдонима связи в `JOIN` предложении. 

В качестве примера рассмотрим *сервицедби* связь со свойством *репортедкондитион* . В следующем запросе этой связи присваивается псевдоним "R" для ссылки на его свойство.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

В приведенном выше примере обратите внимание на то, как *репортедкондитион* является свойством отношения *сервицедби* (не некоторых цифровых двойника с отношением *сервицедби* ).

### <a name="query-with-multiple-joins"></a>Запрос с несколькими ОБЪЕДИНЕНИЯми

В настоящее время в одном запросе поддерживается Предварительная версия, а до пяти `JOIN` . Это позволяет одновременно просматривать несколько уровней связей.

Ниже приведен пример запроса с несколькими объединениями, который получает все лампочки, содержащиеся в светлых панелях 1 и 2.

```sql
SELECT LightBulb 
FROM DIGITALTWINS Room 
JOIN LightPanel RELATED Room.contains 
JOIN LightBulb RELATED LightPanel.contains 
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1') 
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1') 
AND Room.$dtId IN ['room1', 'room2'] 
```

### <a name="other-compound-query-examples"></a>Другие примеры составных запросов

Любой из приведенных выше типов запросов можно **объединить** с помощью сочетаний операторов, чтобы включить более подробные сведения в один запрос. Ниже приведены некоторые дополнительные примеры составных запросов, которые одновременно запрашивают более одного типа дескриптора двойника.

| Описание | Запрос |
| --- | --- |
| На устройствах, которые имеют *место 123* , возвращаются устройства MxChip, обслуживающие роль оператора. | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contosocom:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| Получить двойников, имеющую связь с именем, которая *содержит* другой ДВОЙНИКА с идентификатором *id1* | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| Получение всех комнат этой модели комнаты, содержащихся в *floor11* | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contosocom:DigitalTwins:Room;1')` |

## <a name="reference-expressions-and-conditions"></a>Справочник: выражения и условия

Этот раздел содержит справочник по операторам и функциям, доступным при написании запросов к цифровым двойников Azure.

### <a name="operators"></a>Операторы

Поддерживаются следующие операторы:

| Семейство | Операторы |
| --- | --- |
| Логические |AND, OR, NOT |
| Сравнение |=,! =, <, >, <=, >= |
| Содержит | В, NIN |

### <a name="functions"></a>Функции

Поддерживаются следующие функции проверки и приведения типов:

| Функция | Описание |
| -------- | ----------- |
| IS_DEFINED | Возвращает логическое значение, указывающее, назначено ли свойству значение. Это поддерживается только в том случае, если значение является типом-примитивом. Типы-примитивы включают строку, логическое значение, число или `null` . DateTime, типы объектов и массивы не поддерживаются. |
| IS_OF_MODEL | Возвращает логическое значение, указывающее, соответствует ли указанный двойника указанному типу модели. |
| IS_BOOL | Возвращает логическое значение, указывающее, является ли указанное выражение логическим значением. |
| IS_NUMBER | Возвращает логическое значение, указывающее, является ли указанное выражение числовым значением. |
| IS_STRING | Возвращает логическое значение, указывающее, является ли указанное выражение строковым значением. |
| IS_NULL | Возвращает логическое значение, указывающее, является ли указанное выражение значением Null. |
| IS_PRIMITIVE | Возвращает логическое значение, указывающее, является ли указанное выражение примитивом (строкой, логическим значением, числовым значением или `null`). |
| IS_OBJECT | Возвращает логическое значение, указывающее, является ли указанное выражение объектом JSON. |

Поддерживаются следующие строковые функции:

| Функция | Описание |
| -------- | ----------- |
| STARTSWITH (x, y) | Возвращает значение логического типа, указывающее, начинается ли первое строковое выражение вторым. |
| ENDSWITH (x, y) | Возвращает значение логического типа, указывающее, заканчивается ли первое строковое выражение вторым. |

## <a name="run-queries-with-an-api-call"></a>Выполнение запросов с помощью вызова API

После выбора строки запроса ее необходимо выполнить, вызвав **API запроса**.
В следующем фрагменте кода показан этот вызов из клиентского приложения:

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

Этот вызов возвращает результаты запроса в виде объекта Куериресулт. 

Вызовы запросов поддерживают разбиение на страницы. Ниже приведен полный пример обработки ошибок и разбиения на страницы.

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-limitations"></a>Ограничения запросов

Возможно, задержка составляет до 10 секунд, прежде чем изменения в экземпляре будут отражены в запросах. Например, если выполнить операцию, например создание или удаление двойников с помощью API Дигиталтвинс, результат может быть немедленно отражен в запросах API запросов. Для разрешения этой проблемы достаточно ожидать короткий период.

Существуют дополнительные ограничения на использование `JOIN` во время предварительной версии.
* В инструкции не поддерживаются вложенные запросы `FROM` .
* `OUTER JOIN` семантика не поддерживается, то есть если связь имеет нулевой ранг, то вся строка удаляется из выходного результирующего набора.
* Во время предварительной версии глубина обхода графа может быть ограничена пятью `JOIN` уровнями на запрос.
* Источник для `JOIN` операций ограничен: запрос должен объявлять двойников, с которого начинается запрос.

## <a name="query-best-practices"></a>Рекомендации по запросам

Ниже приведены некоторые советы по выполнению запросов в Azure Digital двойников.

* Рассмотрим шаблон запроса на этапе проектирования модели. Постарайтесь убедиться, что связи, на которые необходимо ответить в одном запросе, моделируются как одноуровневые связи.
* Свойства проектирования таким образом, чтобы избежать больших результирующих наборов из обхода графа.
* Можно значительно сократить количество необходимых запросов, создав массив двойников и выполнив запросы с помощью `IN` оператора. Например, рассмотрим ситуацию, в которой *здания* содержат *Пол* и *этаж* , содержащие *комнаты*. Для поиска комнат в рамках сборки, которые являются горячими, можно:

    1. Поиск этажей в построении на основе `contains` связи
        ```sql
        SELECT Floor
        FROM DIGITALTWINS Building
        JOIN Floor RELATED Building.contains
        WHERE Building.$dtId = @buildingId
        ``` 
    2. Чтобы найти комнаты, вместо того чтобы рассматривать пол по одному и выполнить `JOIN` запрос для поиска комнат для каждого из них, можно выполнить запрос с помощью коллекции этажей в здании (именованное *основание* в запросе ниже).

        В клиентском приложении:
        ```csharp
        var floors = "['floor1','floor2', ..'floorn']"; 
        ```
        В запросе:
        ```sql
        SELECT Room
        FROM DIGITALTWINS Floor
        JOIN Room RELATED Floor.contains
        WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
        AND Room. Temperature > 72
        AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
        ```
* В именах и значениях свойств учитывается регистр, поэтому следует использовать точные имена, определенные в моделях. Если имена свойств написаны с ошибками или имеют неправильный регистр, результирующий набор будет пустым без возвращаемых ошибок.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [API-интерфейсах и пакетах SDK для цифровых двойников Azure](how-to-use-apis-sdks.md), включая API запросов, который используется для выполнения запросов из этой статьи.
