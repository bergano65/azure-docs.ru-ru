---
title: Управление графом двойника со связями
titleSuffix: Azure Digital Twins
description: Узнайте, как управлять графом цифровых двойников, подключив их к связям.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a0ab8f8ff3f2134c205338dfe8e6f2e887a5a053
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949621"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Управление графиком цифровых двойников с помощью связей

Основой Azure Digital двойников является [двойника граф](concepts-twins-graph.md) , представляющий всю среду. Граф двойника состоит из отдельных цифровых двойников, подключенных через **связи**.

Получив рабочий [экземпляр Azure Digital двойников](how-to-set-up-instance-portal.md) и настроив код [проверки подлинности](how-to-authenticate-client.md) в клиентском приложении, вы можете использовать [**API дигиталтвинс**](how-to-use-apis-sdks.md) для создания, изменения и удаления цифровых двойников и их отношений в экземпляре Azure Digital двойников. Вы также можете использовать [пакет SDK для .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)или [Azure Digital двойников CLI](how-to-use-cli.md).

Эта статья посвящена управлению связями и графу в целом. для работы с отдельными цифровыми двойниковми см. раздел [*как управлять цифровыми двойников*](how-to-manage-twin.md).

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Создавать связи

Связи описывают, как разные цифровые двойников соединяются друг с другом, который образует базу двойника графа.

Связи создаются с помощью `CreateRelationship` вызова. 

Чтобы создать связь, необходимо указать:
* Идентификатор источника двойника (двойника, где исходит связь)
* Целевой идентификатор двойника (двойника, где поступила связь)
* Имя связи
* Идентификатор отношения

ИДЕНТИФИКАТОР связи должен быть уникальным в пределах заданного исходного двойника. Он не обязательно должен быть глобально уникальным.
Например, для двойника *foo*каждый конкретный идентификатор связи должен быть уникальным. Однако другая двойниканая *черта* может иметь исходящее отношение, совпадающее с идентификатором связи *foo* . 

В следующем примере кода показано, как добавить связь в свой экземпляр Digital двойников для Azure.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Дополнительные сведения о вспомогательном классе `BasicRelationship` см. в разделе Практические руководства. [*Использование интерфейсов API и пакетов SDK для цифровых двойников Azure*](how-to-use-apis-sdks.md).

### <a name="create-multiple-relationships-between-twins"></a>Создание нескольких связей между двойников

На число связей, которые можно использовать между двумя двойниковами, не накладывается никаких ограничений. Вы можете иметь любое количество отношений между двойников. 

Это означает, что можно выразить несколько различных типов отношений между двумя двойников одновременно. Например, *двойника A* может иметь как *хранимую* связь, так и *производимую* связь с *двойника B*.

При необходимости можно даже создать несколько экземпляров одного типа связи между одними и теми же двумя двойников. В этом примере это означает, что *двойника A* может иметь две разные *хранимые* связи с *двойника B*.

## <a name="list-relationships"></a>Список связей

Для доступа к списку **исходящих** связей, поступающих от заданного двойника на графе, можно использовать:

```csharp
await client.GetRelationshipsAsync(id);
```

Он возвращает `Azure.Pageable<T>` или `Azure.AsyncPageable<T>` , в зависимости от того, используется ли синхронная или асинхронная версия вызова.

Ниже приведен полный пример, в котором извлекается список связей:

```csharp
public async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw if an error occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);
        List<BasicRelationship> results = new List<BasicRelationship>();
        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            results.Add(rel);
        }
        return results;
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
        return null;
    }
}
```

Полученные связи можно использовать для перехода к другим двойников в графе. Для этого прочтите `target` поле из возвращаемой связи и используйте его в качестве идентификатора для следующего вызова `GetDigitalTwin` . 

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Поиск входящих отношений для цифрового двойника

В Azure Digital двойников также есть API для поиска всех **входящих** отношений с заданным двойника. Это часто полезно для обратных переходов или при удалении двойника.

Предыдущий пример кода посвящен поиску исходящих связей от двойника. Следующий пример структурирован аналогично, но находит *Входящие* связи с двойника.

Обратите внимание, что `IncomingRelationship` вызовы не возвращают полный текст связи.

```csharp
async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        List<IncomingRelationship> results = new List<IncomingRelationship>();
        await foreach (IncomingRelationship incomingRel in incomingRels)
            results.Add(incomingRel);
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

## <a name="delete-relationships"></a>Удаление связей

Связи можно удалять с помощью `DeleteRelationship(source, relId);` .

Первый параметр указывает исходный двойника (двойника, где исходит связь). Другой параметр — идентификатор связи. Вам потребуется идентификатор двойника и идентификатор связи, так как идентификаторы связей уникальны только в пределах области двойника.

## <a name="create-a-twin-graph"></a>Создание графа двойника 

В следующем фрагменте кода используются операции связи из этой статьи для создания графа двойника из цифровых двойников и связей.

```csharp
static async Task CreateTwins()
{
    // Create twins - see utility functions below 
    await CreateRoom("Room01", 68, 50, false, "");
    await CreateRoom("Room02", 70, 66, true, "EId-00124");
    await CreateFloorOrBuilding("Floor01", makeFloor:true);

    // Create relationships
    await AddRelationship("Floor01", "contains", "Floor-to-Room01", "Room01");
    await AddRelationship("Floor01", "contains", "Floor-to-Room02", "Room02");
}

static async Task<bool> AddRelationship(string source, string relationship, string id, string target)
{
    var relationship = new BasicRelationship
    {
        TargetId = target,
        Name = relationship
    };

    try
    {
        string relId = $"{source}-contains->{target}";
        await client.CreateRelationshipAsync(source, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
        return true;
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
        return false;
    }
}

static async Task<bool> CreateRoom(string id, double temperature, double humidity)
{
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = "dtmi:com:contoso:Room;2";
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("Temperature", temperature);
    props.Add("Humidity", humidity);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin)); 
        return true;       
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}

static async Task<bool> CreateFloorOrBuilding(string id, bool makeFloor=true)
{
    string type = "dtmi:com:contoso:Building;3";
    if (makeFloor==true)
        type = "dtmi:com:contoso:Floor;2";
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = type;
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("AverageTemperature", 0);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));  
        return true;      
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}
```

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Создание графа двойника из электронной таблицы

В практических случаях двойника иерархии часто создаются на основе данных, хранящихся в другой базе данных или, возможно, в электронной таблице. В этом разделе показано, как можно проанализировать электронную таблицу.

Рассмотрим следующую таблицу данных, описывающую набор цифровых двойников и связей, которые будут созданы.

| Моделирование    | ID | Parent | Имя связи | Другие данные |
| --- | --- | --- | --- | --- |
| floor    | Floor01 | | | … |
| комната    | Room10 | Floor01 | содержит | … |
| комната    | Room11 | Floor01 | содержит | … |
| комната    | Room12 | Floor01 | содержит | … |
| floor    | Floor02 | | | … |
| комната    | Room21 | Floor02 | содержит | … |
| комната    | Room22 | Floor02 | содержит | … |

В следующем примере кода используется [API Microsoft Graph](https://docs.microsoft.com/graph/overview) для чтения электронной таблицы и создания графа Двойникаов Azure Digital двойников на основе результатов.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string type = row[0].GetString();
    string id = row[1].GetString();
    string relSource = row[2].GetString();
    string relName = row[3].GetString();
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (relSource != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = relSource,
            TargetId = id,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    switch (type)
    {
        case "room":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Room;2" };
            break;
        case "floor":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Floor;2" };
            break;
        ... handle additional types
    }
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
        Log.Error($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            client.CreateRelationship(rec.SourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Log.Error($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>Управление связями с помощью интерфейса командной строки

Двойников и их отношения также можно управлять с помощью цифрового двойников Azure CLI. Команды можно найти в [*этом пошаговом окне. Используйте интерфейс командной строки Azure Digital двойников*](how-to-use-cli.md).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о запросах к графу двойников для Azure Digital двойника:
* [*Основные понятия: язык запросов*](concepts-query-language.md)
* [*Пошаговое руководство. запрос графа двойника*](how-to-query-graph.md)