---
title: Управление цифровыми двойниками
titleSuffix: Azure Digital Twins
description: Узнайте, как извлекать, обновлять и удалять отдельные двойников и связи.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f8eae6381a438f6820f525a4d66cb5dc388eefb0
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280383"
---
# <a name="manage-digital-twins"></a>Управление цифровыми двойниками

Сущности в вашей среде представлены [цифровым двойников](concepts-twins-graph.md). Управление цифровым двойников может включать создание, изменение и удаление. Для выполнения этих операций можно использовать [**API дигиталтвинс**](/rest/api/digital-twins/dataplane/twins), [пакет SDK для .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)или [Azure Digital двойников CLI](how-to-use-cli.md).

Эта статья посвящена управлению цифровыми двойников; сведения о работе со связями и [графом двойника](concepts-twins-graph.md) в целом см. в разделе [*руководство. Управление диаграммой двойника с помощью связей*](how-to-manage-graph.md).

> [!TIP]
> Все функции пакета SDK имеют синхронные и асинхронные версии.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-digital-twin"></a>Создание цифрового двойника

Чтобы создать двойника, используйте `CreateOrReplaceDigitalTwinAsync()` метод в клиенте службы следующим образом:

```csharp
await client.CreateOrReplaceDigitalTwinAsync("myTwinId", initData);
```

Чтобы создать цифровой двойника, необходимо предоставить:
* Требуемый идентификатор для цифрового двойника
* [Модель](concepts-models.md) , которую вы хотите использовать

При необходимости можно указать начальные значения для всех свойств цифрового двойника. Свойства рассматриваются как необязательные и могут быть заданы позже, но **они не будут отображаться как часть двойника, пока они не будут заданы.**

>[!NOTE]
>Хотя свойства **двойника не должны** быть инициализированы, при создании двойника необходимо задать любые [компоненты](concepts-models.md#elements-of-a-model) двойника. Они могут быть пустыми объектами, но сами компоненты должны существовать.

Модель и все начальные значения свойств предоставляются с помощью `initData` параметра, который представляет собой строку JSON, содержащую соответствующие данные. Чтобы получить дополнительные сведения о структурировании этого объекта, перейдите к следующему разделу.

> [!TIP]
> После создания или обновления двойника может возникнуть задержка до 10 секунд, прежде чем изменения будут отражены в [запросах](how-to-query-graph.md). `GetDigitalTwin`Эта задержка не возникает в API (описанном [Далее в этой статье](#get-data-for-a-digital-twin)), поэтому если вам нужен мгновенный ответ, используйте вызов API вместо запроса, чтобы увидеть недавно созданный двойников. 

### <a name="initialize-model-and-properties"></a>Инициализация модели и свойств

Можно инициализировать свойства объекта двойника во время создания двойника. 

API создания двойника принимает объект, который сериализуется в допустимое описание JSON свойств двойника. Описание формата JSON для двойника см. [*в статье основные понятия: Digital двойников и двойника Graph*](concepts-twins-graph.md) . 

Во-первых, можно создать объект данных для представления двойника и данных его свойств следующим образом:

```csharp
await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(srcId, twin);
```
Вы можете создать объект параметра либо вручную, либо с помощью предоставленного вспомогательного класса. Ниже приведен пример каждого из них.

#### <a name="create-twins-using-manually-created-data"></a>Создание двойников с использованием данных, созданных вручную

Без использования каких-либо настраиваемых вспомогательных классов можно представить свойства двойника в `Dictionary<string, object>` , где `string` — это имя свойства, а `object` — объект, представляющий свойство и его значение.

[!INCLUDE [Azure Digital Twins code: create twin](../../includes/digital-twins-code-create-twin.md)]

#### <a name="create-twins-with-the-helper-class"></a>Создание двойников с помощью вспомогательного класса

Вспомогательный класс служб `BasicDigitalTwin` позволяет хранить поля свойств непосредственно в объекте "двойника". Вы по-прежнему можете создать список свойств с помощью `Dictionary<string, object>` , который затем можно добавить в объект двойника в качестве `CustomProperties` непосредственного объекта.

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.Contents = props;

client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myRoomId", twin);
Console.WriteLine("The twin is created successfully");
```

>[!NOTE]
> `BasicDigitalTwin` объекты поступают с `Id` полем. Это поле можно оставить пустым, но если добавить значение идентификатора, оно должно соответствовать параметру идентификатора, переданному в `CreateOrReplaceDigitalTwinAsync()` вызов. Например:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Получение данных для цифрового двойника

Вы можете получить доступ к сведениям о любом цифровом двойника, вызвав `GetDigitalTwin()` метод следующим образом:

```csharp
object result = await client.GetDigitalTwin(id);
```
Этот вызов возвращает двойника данные в виде строго типизированного типа объекта, такого как `BasicDigitalTwin` . Ниже приведен пример использования этого параметра для просмотра сведений о двойника:

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin("myRoomId");
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.Contents.Keys)
{
  if (twin.Contents.TryGetValue(prop, out object value))
  Console.WriteLine($"Property '{prop}': {value}");
}
```
При получении двойника с помощью метода возвращаются только те свойства, которые были установлены хотя бы один раз `GetDigitalTwin()` .

>[!TIP]
>`displayName`Для двойника является частью метаданных модели, поэтому она не будет отображаться при получении данных для экземпляра двойника. Чтобы увидеть это значение, можно [извлечь его из модели](how-to-manage-model.md#retrieve-models).

Чтобы получить несколько двойников с помощью одного вызова API, ознакомьтесь с примерами API запросов в разделе [*инструкции. запрос к графу двойника*](how-to-query-graph.md).

Рассмотрим следующую модель (написанную на [языке определения цифровых двойников (дтдл)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)), которая определяет *Луна* :

```json
{
    "@id": "dtmi:example:Moon;1",
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
Результат вызова `object result = await client.GetDigitalTwinAsync("my-moon");` для типа *Луны* двойника может выглядеть следующим образом:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
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
* `$etag`— стандартное поле HTTP, назначенное веб-сервером.
* Другие свойства в `$metadata` разделе. Сюда входит следующее.
    - ДТМИ модели цифрового двойника.
    - Состояние синхронизации для каждого записываемого свойства. Это наиболее полезно для устройств, где служба и устройство могут иметь состояние рассогласования (например, если устройство находится в автономном режиме). В настоящее время это свойство применяется только к физическим устройствам, подключенным к центру Интернета вещей. С данными в разделе метаданных можно понять полное состояние свойства, а также отметку времени последнего изменения. Дополнительные сведения о состоянии синхронизации см. в [этом руководстве центра Интернета вещей](../iot-hub/tutorial-device-twins.md) по синхронизации состояния устройства.
    - Зависящие от службы метаданные, например из центра Интернета вещей или Azure Digital двойников. 

Вы можете проанализировать возвращенный JSON для двойника, используя библиотеку анализа JSON по своему усмотрению, например `System.Text.Json` .

Можно также использовать вспомогательный класс сериализации `BasicDigitalTwin` , включенный в пакет SDK, который возвращает основные метаданные и свойства двойника в предварительно проанализированной форме. Например:

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_Id);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.Contents.Keys)
{
    if (twin.Contents.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

Дополнительные сведения о вспомогательных классах сериализации см. в статье [*Использование интерфейсов API и пакетов SDK для цифровых двойников Azure*](how-to-use-apis-sdks.md).

## <a name="update-a-digital-twin"></a>Обновление цифрового двойника

Чтобы обновить свойства цифрового двойника, необходимо записать сведения, которые необходимо заменить в формате [исправления JSON](http://jsonpatch.com/) . Таким образом можно заменить несколько свойств одновременно. Затем вы передаете документ исправления JSON в `UpdateDigitalTwin()` метод:

```csharp
await client.UpdateDigitalTwin(id, patch);
```

Вызов Patch может обновлять столько свойств одного двойника, сколько необходимо (даже все они). Если необходимо обновить свойства в нескольких двойников, потребуется отдельный вызов Update для каждого двойника.

> [!TIP]
> После создания или обновления двойника может возникнуть задержка до 10 секунд, прежде чем изменения будут отражены в [запросах](how-to-query-graph.md). В `GetDigitalTwin` API (описанном [ранее в этой статье](#get-data-for-a-digital-twin)) Эта задержка не возникает, поэтому используйте вызов API вместо запроса, чтобы увидеть обновленный двойников, если требуется мгновенный ответ. 

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
Вы можете создавать исправления с помощью `JsonPatchDocument` в [пакете SDK](how-to-use-apis-sdks.md). Ниже приведен пример.

```csharp
var updateTwinData = new JsonPatchDocument();
updateTwinData.AppendAddOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(twin_Id, updateTwinData);
```

### <a name="update-properties-in-digital-twin-components"></a>Обновление свойств в компонентах Digital двойника

Помните, что модель может содержать компоненты, что позволяет состоять из других моделей. 

Чтобы исправить свойства в компонентах Digital двойника, можно использовать синтаксис пути в исправлении JSON:

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

`UpdateDigitalTwin()`Функция также может использоваться для переноса цифрового двойника в другую модель. 

Например, рассмотрим следующий документ с исправлением JSON, который заменяет поле метаданных Digital двойника `$model` :

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:example:foo;1"
  }
]
```

Эта операция будет выполнена только в том случае, если двойника, измененный исправлением, соответствует новой модели. 

Рассмотрим следующий пример:
1. Представьте себе цифровой двойника с моделью *foo_old*. *foo_old* определяет требуемое свойство *массы*.
2. Новая модель *foo_new* определяет масса свойств и добавляет новую *температуру* требуемого свойства.
3. После установки исправления цифровое двойника должно иметь свойство масса и температура. 

Исправление для этой ситуации должно обновить свойство двойника и Model, следующим образом:

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:example:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

### <a name="handle-conflicting-update-calls"></a>Обработку конфликтующих вызовов обновления

Azure Digital двойников гарантирует, что все входящие запросы обрабатываются один за другим. Это означает, что даже если несколько функций пытаются одновременно обновить одно и то же свойство в двойника, **нет необходимости** писать явный код блокировки для обработки конфликта.

Это поведение применяется для каждого двойника. 

В качестве примера представьте себе ситуацию, в которой эти три вызова поступают в одно и то же время: 
*   Запись свойства A в *Twin1*
*   Запись свойства B на *Twin1*
*   Запись свойства A в *Twin2*

Два вызова, которые изменяют *Twin1* , выполняются один за другим, а сообщения об изменениях формируются для каждого изменения. Вызов функции Modify *Twin2* может выполняться параллельно без конфликта, как только он поступает.

## <a name="delete-a-digital-twin"></a>Удаление цифрового двойника

Вы можете удалить двойников с помощью `DeleteDigitalTwin()` метода. Однако можно удалить только двойника, если он не имеет больше связей. Поэтому сначала удалите входящие и исходящие связи двойника.

Ниже приведен пример кода для удаления двойников и их связей:

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
        AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);

        await foreach (BasicRelationship rel in rels)
        {
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```
### <a name="delete-all-digital-twins"></a>Удалить все цифровые двойников

Пример того, как удалить все двойников одновременно, можно скачать с помощью примера приложения, используемого в [_Tutorial. Изучите основные сведения с примером клиентского приложения *](tutorial-command-line-app.md). Файл *CommandLoop.CS* делает это в `CommandDeleteAllTwins()` функции.

## <a name="manage-twins-using-runnable-code-sample"></a>Управление двойников с помощью примера готового к запуску кода

Вы можете использовать приведенный ниже пример готового к запуску кода, чтобы создать двойника, обновить сведения о нем и удалить двойника. 

### <a name="set-up-the-runnable-sample"></a>Настройка готового к запуску примера

В фрагменте кода используется [Room.jsв](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) определении модели из [*учебника: изучение Azure Digital двойников с примером клиентского приложения*](tutorial-command-line-app.md). С помощью этой ссылки можно перейти непосредственно к файлу или загрузить его в составе полного [примера проекта.](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)

Перед запуском образца выполните следующие действия.
1. Скачайте файл модели, поместите его в свой проект и замените `<path-to>` заполнитель в приведенном ниже коде, чтобы сообщить программе, где его найти.
2. Замените заполнитель `<your-instance-hostname>` именем узла своего экземпляра цифрового двойников Azure.
3. Добавьте эти пакеты в проект:
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Кроме того, необходимо настроить локальные учетные данные, если вы хотите выполнить пример напрямую. В следующем разделе приведено пошаговое руководство.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Запуск примера

После выполнения описанных выше действий можно запустить следующий пример кода.

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload a model");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            // Upload the model to the service
            await client.CreateModelsAsync(typeList);

            //Create new digital twin
            BasicDigitalTwin twin = new BasicDigitalTwin();
            string twin_Id = "myRoomId";
            twin.Metadata = new DigitalTwinMetadata();
            twin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            twin.Contents = props;
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twin_Id, twin);
            Console.WriteLine("Twin created successfully");
            Console.WriteLine();

            //Print twin
            Console.WriteLine("--- Printing twin details:");
            twin = FetchAndPrintTwin(twin_Id, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Update twin data
            var updateTwinData = new JsonPatchDocument();
            updateTwinData.AppendAdd("/Temperature", 25.0);
            await client.UpdateDigitalTwinAsync(twin_Id, updateTwinData);
            Console.WriteLine("Twin properties updated");
            Console.WriteLine();

            //Print twin again
            Console.WriteLine("--- Printing twin details (after update):");
            FetchAndPrintTwin(twin_Id, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete twin
            await DeleteTwin(client, twin_Id);
        }

        private static BasicDigitalTwin FetchAndPrintTwin(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_Id);
            Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
            foreach (string prop in twin.Contents.Keys)
            {
                if (twin.Contents.TryGetValue(prop, out object value))
                    Console.WriteLine($"Property '{prop}': {value}");
            }

            return twin;
        }
        private static async Task DeleteTwin(DigitalTwinsClient client, string id)
        {
            await FindAndDeleteOutgoingRelationshipsAsync(client, id);
            await FindAndDeleteIncomingRelationshipsAsync(client, id);
            try
            {
                await client.DeleteDigitalTwinAsync(id);
                Console.WriteLine("Twin deleted successfully");
            }
            catch (RequestFailedException exc)
            {
                Console.WriteLine($"*** Error:{exc.Message}");
            }
        }

        private static async Task FindAndDeleteOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);

                await foreach (BasicRelationship rel in rels)
                {
                    await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
                    Console.WriteLine($"Deleted relationship {rel.Id} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
            }
        }

       private static async Task FindAndDeleteIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
                    Console.WriteLine($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
            }
        }

    }
}

```
Ниже приведены выходные данные консоли для приведенной выше программы. 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="Вывод на консоль, показывающий, что двойника создан, обновлен и удален." lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="manage-twins-with-cli"></a>Управление двойников с помощью интерфейса командной строки

Двойников также можно управлять с помощью цифрового интерфейса командной строки Azure двойников. Команды можно найти в [_How: используйте интерфейс командной строки Azure Digital двойников](how-to-use-cli.md).

## <a name="view-all-digital-twins"></a>Просмотреть все цифровые двойников

Чтобы просмотреть все цифровые двойников в вашем экземпляре, используйте [запрос](how-to-query-graph.md). Можно выполнить запрос с помощью [API запросов](/rest/api/digital-twins/dataplane/query) или [команд интерфейса командной строки](how-to-use-cli.md).

Ниже приведен основной текст запроса, который вернет список всех цифровых двойников в экземпляре:

```sql
SELECT *
FROM DIGITALTWINS
``` 

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как создавать связи между цифровым двойников и управлять ими:
* [*Руководство. Управление графом двойника с помощью связей*](how-to-manage-graph.md)