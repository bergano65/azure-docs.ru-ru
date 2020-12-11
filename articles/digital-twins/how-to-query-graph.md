---
title: Запрос данных о графе двойника
titleSuffix: Azure Digital Twins
description: Дополнительные сведения см. в статье запрос к графу двойников двойника в Azure Digital.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 966b87dfb3111d7a112ea99f37dee730495d491f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032836"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Запрос к графу Azure Digital двойников двойника

В этой статье приведены примеры запросов и подробные инструкции по использованию **языка запросов Azure Digital двойников** для запроса информации о [двойника Graph](concepts-twins-graph.md) . (Общие сведения о языке запросов и полный список его возможностей см. в разделе [*Основные понятия: язык запросов*](concepts-query-language.md).)

Эта статья начинается с примеров запросов, иллюстрирующих языковую структуру запросов и общие операции запросов для Digital двойников. Затем в нем описывается, как выполнять запросы после их записи с помощью [API запросов](/rest/api/digital-twins/dataplane/query) цифровых двойников Azure или [пакета SDK](how-to-use-apis-sdks.md#overview-data-plane-apis).

> [!TIP]
> Если вы выполняете примеры запросов, приведенные ниже с помощью вызова API или пакета SDK, необходимо сжать текст запроса в одну строку.

## <a name="show-all-digital-twins"></a>Показывать все цифровые двойников

Ниже приведен базовый запрос, который возвращает список всех цифровых двойников в экземпляре:

```sql
SELECT *
FROM DIGITALTWINS
```

## <a name="query-by-property"></a>Запрос по свойству

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
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

Это может помочь получить двойников по их свойствам *тегов* , как описано в разделе [Добавление тегов в Digital двойников](how-to-use-tags.md). Ниже приведен запрос, который получает все двойников, помеченные *красным цветом*:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(tags.red)
```

Можно также получить двойников на основе **типа свойства**. Ниже приведен запрос, который получает двойников, свойство *температуры* которого является числом:

```sql
SELECT * FROM DIGITALTWINS T WHERE IS_NUMBER(T.Temperature)
```

## <a name="query-by-model"></a>Запрос по модели

`IS_OF_MODEL`Оператор можно использовать для фильтрации на основе [**модели**](concepts-models.md)двойника.

Он учитывает [наследование](concepts-models.md#model-inheritance) и [Управление версиями](how-to-manage-model.md#update-models)модели и вычисляет **значение true** для заданного двойника, если двойника соответствует одному из следующих условий:

* Двойника напрямую реализует модель, предоставленную в `IS_OF_MODEL()` , а номер версии модели на двойника *больше или равен* номеру версии указанной модели.
* Двойника реализует модель, которая *расширяет* модель, предоставленную в `IS_OF_MODEL()` , а номер версии расширенной модели двойника *больше или равен* номеру версии указанной модели.

Например, при запросе двойников модели `dtmi:example:widget;4` запрос возвратит все двойников, основанные на **версии 4 или выше** модели **мини** -приложения, а также двойников на основе **моделей, наследующих от Widget**. **4 or greater**

`IS_OF_MODEL` может принимать несколько различных параметров, и остальная часть этого раздела будет отделена от различных вариантов перегрузки.

Простейший способ использования `IS_OF_MODEL` принимает только `twinTypeName` параметр: `IS_OF_MODEL(twinTypeName)` .
Ниже приведен пример запроса, который передает значение в этом параметре:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1')
```

Чтобы указать коллекцию двойника для поиска при наличии нескольких элементов (например, если `JOIN` используется), добавьте `twinCollection` параметр: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Ниже приведен пример запроса, который добавляет значение для этого параметра:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1')
```

Чтобы выполнить точное соответствие, добавьте `exact` параметр: `IS_OF_MODEL(twinTypeName, exact)` .
Ниже приведен пример запроса, который добавляет значение для этого параметра:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1', exact)
```

Можно также передать все три аргумента вместе: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Ниже приведен пример запроса, в котором указывается значение для всех трех параметров:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1', exact)
```

## <a name="query-by-relationship"></a>Запрос по связям

При выполнении запросов на основе **отношений** Digital двойников язык запросов Azure Digital двойников имеет специальный синтаксис.

Связи извлекаются в область запроса в `FROM` предложении. Важное отличие от "классических" языков SQL-типов заключается в том, что каждое выражение в этом `FROM` предложении не является таблицей; вместо этого `FROM` предложение выражает прохождение межсущностного отношения и записывается с помощью версии Azure Digital двойников `JOIN` .

Вспомним, что с возможностями [модели](concepts-models.md) цифровых двойников Azure отношения не существуют независимо от двойников. Это означает, что язык запросов Azure Digital двойников `JOIN` немного отличается от общего SQL `JOIN` , так как связи здесь нельзя запрашивать отдельно и должны быть привязаны к двойника.
Чтобы включить это различие, в `RELATED` предложении используется ключевое слово `JOIN` для ссылки на набор связей двойника.

В следующем разделе приведены несколько примеров того, как это выглядит.

> [!TIP]
> По сути, эта функция имитирует функции CosmosDB, ориентированные на документы, где `JOIN` их можно выполнять с дочерними объектами в документе. CosmosDB использует `IN` ключевое слово, чтобы указать, что `JOIN` предназначено для итерации элементов массива в текущем контексте документа.

### <a name="relationship-based-query-examples"></a>Примеры запросов на основе связей

Чтобы получить набор данных, включающий связи, используйте одну `FROM` инструкцию, за которой следует оператор N `JOIN` , где `JOIN` Операторы выражают связи с результатом предыдущей `FROM` `JOIN` инструкции или.

Ниже приведен пример запроса на основе связей. Этот фрагмент кода выбирает все цифровые двойников со свойством *ID* "ABC", а все цифровые двойников, связанные с этими цифровыми двойников через отношение *Contains* .

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC'
```

> [!NOTE]
> Разработчику не нужно сопоставлять этот `JOIN` объект со значением ключа в `WHERE` предложении (или указать значение ключа с `JOIN` определением). Эта корреляция автоматически вычисляются системой, так как сами свойства связи определяют целевую сущность.

### <a name="query-the-properties-of-a-relationship"></a>Запрос свойств связи

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

`JOIN`В одном запросе поддерживаются до пяти s. Это позволяет одновременно просматривать несколько уровней связей.

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

## <a name="count-items"></a>Количество элементов

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

## <a name="filter-results-select-top-items"></a>Фильтрация результатов: выбор верхних элементов

С помощью предложения можно выбрать несколько "верхних" элементов в запросе `Select TOP` .

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

## <a name="filter-results-specify-return-set-with-projections"></a>Фильтрация результатов: определение возвращаемого набора с проекциями

Используя проекции в `SELECT` инструкции, можно выбрать, какие столбцы будут возвращены запросом.

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

Следующий запрос выполняет те же операции, что и предыдущий пример, но присваивает имена свойств `consumerName` , `first` , `second` и `factoryArea` .

```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

Вот похожий запрос, который запрашивает тот же набор, что и приведенный выше, но проецирует только свойство *Consumer.Name* как и `consumerName` проецирует готовую *фабрику* как двойника.

```sql
SELECT Consumer.name AS consumerName, Factory
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name)
```

## <a name="build-efficient-queries-with-the-in-operator"></a>Создание эффективных запросов с помощью оператора IN

Можно значительно сократить количество необходимых запросов, создав массив двойников и выполнив запросы с помощью `IN` оператора. 

Например, рассмотрим ситуацию, в которой *здания* содержат *Пол* и *этаж* , содержащие *комнаты*. Для поиска комнат в рамках сборки, которые являются горячими, один из способов состоит в выполнении этих действий.

1. Поиск этажей в построении на основе `contains` связи.

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

## <a name="other-compound-query-examples"></a>Другие примеры составных запросов

Любой из приведенных выше типов запросов можно **объединить** с помощью сочетаний операторов, чтобы включить более подробные сведения в один запрос. Ниже приведены некоторые дополнительные примеры составных запросов, которые одновременно запрашивают более одного типа дескриптора двойника.

| Описание | Запрос |
| --- | --- |
| На устройствах, которые имеют *место 123* , возвращаются устройства MxChip, обслуживающие роль оператора. | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contoso:com:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| Получить двойников, имеющую связь с именем, которая *содержит* другой ДВОЙНИКА с идентификатором *id1* | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| Получение всех комнат этой модели комнаты, содержащихся в *floor11* | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contoso:com:DigitalTwins:Room;1')` |

## <a name="run-queries-with-the-api"></a>Выполнение запросов с помощью API

После выбора строки запроса ее необходимо выполнить, вызвав [**API запроса**](/rest/api/digital-twins/dataplane/query).

Вы можете напрямую вызывать API или использовать один из [пакетов SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) , доступных для Azure Digital двойников.

В следующем фрагменте кода показан вызов [пакета SDK .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) из клиентского приложения:

```csharp
    string adtInstanceEndpoint = "https://<your-instance-hostname>";

    var credential = new DefaultAzureCredential();
    DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceEndpoint), credential);

    // Run a query for all twins   
    string query = "SELECT * FROM DIGITALTWINS";
    AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);
```

Этот вызов возвращает результаты запроса в виде объекта [басикдигиталтвин](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin?view=azure-dotnet&preserve-view=true) .

Вызовы запросов поддерживают разбиение на страницы. Ниже приведен полный пример использования `BasicDigitalTwin` в качестве типа результата запроса с обработкой ошибок и разбиением на страницы.

```csharp
try
{
    await foreach(BasicDigitalTwin twin in result)
        {
            // You can include your own logic to print the result
            // The logic below prints the twin's ID and contents
            Console.WriteLine($"Twin ID: {twin.Id} \nTwin data");
            IDictionary<string, object> contents = twin.Contents;
            foreach (KeyValuePair<string, object> kvp in contents)
            {
                Console.WriteLine($"{kvp.Key}  {kvp.Value}");
            }
        }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"Error {e.Status}: {e.Message}");
    throw;
}
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [API-интерфейсах и пакетах SDK для цифровых двойников Azure](how-to-use-apis-sdks.md), включая API запросов, который используется для выполнения запросов из этой статьи.
