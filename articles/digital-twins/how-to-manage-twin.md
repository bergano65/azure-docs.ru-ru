---
title: Управление цифровыми двойниками
titleSuffix: Azure Digital Twins
description: Узнайте, как извлекать, обновлять и удалять отдельные двойников и связи.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e37c680f6bf9e296230232c0d4e0fab5f50ad3cd
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142376"
---
# <a name="manage-digital-twins"></a>Управление цифровыми двойниками

Сущности в вашей среде представлены [цифровым двойников](concepts-twins-graph.md). Управление цифровым двойников может включать создание, изменение и удаление. Для выполнения этих операций можно использовать [**API дигиталтвинс**](how-to-use-apis-sdks.md), [пакет SDK для .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)или [Azure Digital двойников CLI](how-to-use-cli.md).

Эта статья посвящена управлению цифровыми двойников; сведения о работе со связями и [графом двойника](concepts-twins-graph.md) в целом см. в разделе [руководство. Управление диаграммой двойника с помощью связей](how-to-manage-graph.md).

> [!TIP]
> Все функции пакета SDK имеют синхронные и асинхронные версии.

## <a name="create-a-digital-twin"></a>Создание цифрового двойника

Чтобы создать двойника, используйте `CreateDigitalTwin` метод в клиенте службы следующим образом:

```csharp
await client.CreateDigitalTwinAsync("myNewTwinID", initData);
```

Чтобы создать цифровой двойника, необходимо предоставить:
* Требуемый идентификатор для цифрового двойника
* [Модель](concepts-models.md) , которую вы хотите использовать 

При необходимости можно указать начальные значения для всех свойств цифрового двойника. 

> [!TIP]
> При получении двойника с помощью Жетдигиталтвин возвращаются только те свойства, которые были установлены хотя бы один раз.  

Значения модели и начальных свойств предоставляются с помощью `initData` параметра, который представляет собой строку JSON, содержащую соответствующие данные.

### <a name="initialize-properties"></a>Инициализация свойств

API создания двойника принимает объект, который можно сериализовать в допустимое описание JSON свойств двойника. Описание формата JSON для двойника см. [в статье основные понятия: Digital двойников и двойника Graph](concepts-twins-graph.md) .

Вы можете создать объект параметра либо вручную, либо с помощью предоставленного вспомогательного класса. Ниже приведен пример каждого из них.

#### <a name="create-twins-using-manually-created-data"></a>Создание двойников с помощью данных, созданных вручную

```csharp
// Define the model type for the twin to be created
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:com:contoso:Room;1" }
};
// Initialize the twin properties
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", temperature},
    { "Humidity", humidity},
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

#### <a name="create-twins-with-the-helper-class"></a>Создание двойников с помощью вспомогательного класса

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

## <a name="get-data-for-a-digital-twin"></a>Получение данных для цифрового двойника

Вы можете получить доступ ко всем данным любого цифрового двойника, вызвав:

```csharp
object result = await client.GetDigitalTwin(id);
```

Этот вызов возвращает двойника данные в виде строки JSON. 

Чтобы получить несколько двойников с помощью одного вызова API, ознакомьтесь с примерами API запросов в разделе [инструкции. запрос к графу двойника](how-to-query-graph.md).

Рассмотрим следующую модель (написанную на [языке определения цифровых двойников (дтдл)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)), которая определяет *Луна*:

```json
{
    "@id": " dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```

Результат вызова `object result = await client.DigitalTwins.GetByIdAsync("my-moon");` для типа *Луны*двойника может выглядеть следующим образом:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:com:contoso:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

Определенные свойства цифровых двойника возвращаются в виде свойств верхнего уровня в цифровом двойника. Метаданные или системные сведения, не входящие в определение ДТДЛ, возвращаются с `$` префиксом. Свойства метаданных включают:
* Идентификатор цифрового двойника в этом экземпляре Azure Digital двойников, например `$dtId` .
* `$etag`, стандартное поле HTTP, назначенное веб-сервером
* Другие свойства в `$metadata` разделе. Это могут быть:
    - ДТМИ модели цифрового двойника.
    - Состояние синхронизации для каждого записываемого свойства. Это наиболее полезно для устройств, где служба и устройство могут иметь состояние рассогласования (например, если устройство находится в автономном режиме). В настоящее время это свойство применяется только к физическим устройствам, подключенным к центру Интернета вещей. С данными в разделе метаданных можно понять полное состояние свойства, а также отметку времени последнего изменения. Дополнительные сведения о состоянии синхронизации см. в [этом руководстве центра Интернета вещей](../iot-hub/tutorial-device-twins.md) по синхронизации состояния устройства.
    - Зависящие от службы метаданные, например из центра Интернета вещей или Azure Digital двойников. 

Вы можете проанализировать возвращенный JSON для двойника, используя библиотеку анализа JSON по своему усмотрению, например `System.Text.Json` .

Можно также использовать вспомогательный класс сериализации `BasicDigitalTwin` , включенный в пакет SDK, который возвращает основные метаданные и свойства двойника в предварительно проанализированной форме. Вот пример:

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

Дополнительные сведения о вспомогательных классах сериализации см. в статье [Использование интерфейсов API и пакетов SDK для цифровых двойников Azure](how-to-use-apis-sdks.md).

## <a name="update-a-digital-twin"></a>Обновление цифрового двойника

Чтобы обновить свойства цифровой двойника, необходимо записать сведения, которые необходимо заменить в формате [исправления JSON](http://jsonpatch.com/) . Таким образом можно заменить несколько свойств одновременно. Затем вы передаете документ исправления JSON в `Update` метод:

`await client.UpdateDigitalTwin(id, patch);`.

Ниже приведен пример кода исправления JSON. Этот документ заменяет значения свойств *масса* и *радиус* цифрового двойника, к которому он применяется.

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```

Вы можете создавать исправления вручную или с помощью вспомогательного класса сериализации в [пакете SDK](how-to-use-apis-sdks.md). Ниже приведен пример каждого из них.

#### <a name="create-patches-manually"></a>Создание исправлений вручную
```csharp
List<object> twinData = new List<object>();
twinData.Add(new Dictionary<string, object>() {
    { "op", "add"},
    { "path", "/Temperature"},
    { "value", 25.0}
});

await client.UpdateDigitalTwinAsync(twinId, JsonConvert.SerializeObject(twinData));
```

#### <a name="create-patches-using-the-helper-class"></a>Создание исправлений с помощью вспомогательного класса

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>Обновление свойств в компонентах Digital двойника

Помните, что модель может содержать компоненты, что позволяет состоять из других моделей. 

Чтобы исправить свойства в компонентах Digital двойника, вы будете использовать синтаксис пути в исправлении JSON:

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>Обновление модели цифрового двойника

`Update`Функция также может использоваться для переноса цифрового двойника в другую модель. 

Например, рассмотрим следующий документ с исправлением JSON, который заменяет поле метаданных Digital двойника `$model` :

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:contoso:foo;1"
  }
]
```

Эта операция будет выполнена только в том случае, если двойника, измененный исправлением, соответствует новой модели. 

Рассмотрим следующий пример.
1. Представьте себе цифровой двойника с моделью *foo_old*. *foo_old* определяет требуемое свойство *массы*.
2. Новая модель *foo_new* определяет масса свойств и добавляет новую *температуру*требуемого свойства.
3. После установки исправления цифровое двойника должно иметь свойство масса и температура. 

Исправление для этой ситуации должно обновить свойство двойника и Model, следующим образом:

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:com:contoso:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

## <a name="delete-a-digital-twin"></a>Удаление цифрового двойника

Двойников можно удалить с помощью `DeleteDigitalTwin(ID)` . Однако можно удалить только двойника, если он не имеет больше связей. Сначала необходимо удалить все связи. 

Ниже приведен пример кода для этого:

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);

        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipssAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

### <a name="delete-all-digital-twins"></a>Удалить все цифровые двойников

Пример того, как удалить все двойников одновременно, можно скачать с помощью примера приложения, используемого в [учебнике. Изучите основные сведения с примером клиентского приложения](tutorial-command-line-app.md). Файл *CommandLoop.CS* делает это в `CommandDeleteAllTwins` функции.

## <a name="manage-twins-with-cli"></a>Управление двойников с помощью интерфейса командной строки

Двойников также можно управлять с помощью цифрового интерфейса командной строки Azure двойников. Команды можно найти в [этом пошаговом окне. Используйте интерфейс командной строки Azure Digital двойников](how-to-use-cli.md).

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как создавать связи между цифровым двойников и управлять ими:
* [Руководство. Управление графом двойника с помощью связей](how-to-manage-graph.md)