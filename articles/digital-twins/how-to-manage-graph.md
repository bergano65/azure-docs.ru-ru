---
title: Управление графом двойника со связями
titleSuffix: Azure Digital Twins
description: Узнайте, как управлять графом цифровых двойников, подключив их к связям.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 28551cb201ab964a21461d6b3f97ce439e446011
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130295"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Управление графиком цифровых двойников с помощью связей

Основой Azure Digital двойников является [двойника граф](concepts-twins-graph.md) , представляющий всю среду. Граф двойника состоит из отдельных цифровых двойников, подключенных через **связи** . 

Получив рабочий [экземпляр Azure Digital двойников](how-to-set-up-instance-portal.md) и настроив код [проверки подлинности](how-to-authenticate-client.md) в клиентском приложении, вы можете использовать [**API дигиталтвинс**](/rest/api/digital-twins/dataplane/twins) для создания, изменения и удаления цифровых двойников и их отношений в экземпляре Azure Digital двойников. Вы также можете использовать [пакет SDK для .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)или [Azure Digital двойников CLI](how-to-use-cli.md).

Эта статья посвящена управлению связями и графу в целом. для работы с отдельными цифровыми двойниковми см. раздел [*как управлять цифровыми двойников*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]
    
[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Создавать связи

Связи описывают, как разные цифровые двойников соединяются друг с другом, который образует базу двойника графа.

Связи создаются с помощью `CreateRelationship()` вызова. 

Чтобы создать связь, необходимо указать:
* Идентификатор источника двойника ( `srcId` в образце кода ниже): идентификатор двойника, в которой создается связь.
* Целевой идентификатор двойника ( `targetId` в примере кода ниже): идентификатор двойника, в которой получено отношение.
* Имя связи (в приведенном `relName` ниже примере кода): универсальный тип связи, что подобно _Contains_ .
* Идентификатор отношения (в приведенном `relId` ниже примере кода): конкретное имя для этой связи, что вроде _Relationship1_ .

ИДЕНТИФИКАТОР связи должен быть уникальным в пределах заданного исходного двойника. Оно не обязательно должно быть глобально уникальным.
Например, для двойника *foo* каждый конкретный идентификатор связи должен быть уникальным. Однако другая двойниканая *черта* может иметь исходящее отношение, совпадающее с идентификатором связи *foo* .

В следующем примере кода показано, как создать связь в вашем экземпляре Digital двойников для Azure.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
            
        }
```
В методе Main теперь можно вызвать `CreateRelationship()` функцию, чтобы создать связь со следующим _contains_ объектом: 

```csharp
await CreateRelationship(client, srcId, targetId, "contains");
```
Если вы хотите создать несколько связей, можно повторить вызовы одного и того же метода, передав разные типы отношений в аргумент. 

Дополнительные сведения о вспомогательном классе `BasicRelationship` см. в разделе Практические руководства. [*Использование интерфейсов API и пакетов SDK для цифровых двойников Azure*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Создание нескольких связей между двойников

Отношения можно классифицировать следующим образом: 

* Исходящие связи: отношения, принадлежащие этой двойника, которые, в своюмся, должны соединяться с другими двойников. `GetRelationshipsAsync()`Метод используется для получения исходящих отношений двойника.
* Входящие отношения: отношения, принадлежащие другим двойников, которые указывают на этот двойника для создания ссылки "входящий". `GetIncomingRelationshipsAsync()`Метод используется для получения входящих отношений двойника.

На число связей, которые можно использовать между двумя двойниковами, не накладывается никаких ограничений. Вы можете иметь любое количество отношений между двойников. 

Это означает, что можно выразить несколько различных типов отношений между двумя двойников одновременно. Например, *двойника A* может иметь как *хранимую* связь, так и *производимую* связь с *двойника B* .

При необходимости можно даже создать несколько экземпляров одного типа связи между одними и теми же двумя двойников. В этом примере *двойника A* может иметь два разных *хранимых* отношения с *двойника B* , если связи имеют разные идентификаторы отношений.

## <a name="list-relationships"></a>Список связей

Чтобы получить доступ к списку **исходящих** связей для заданного двойника в графе, можно использовать `GetRelationships()` метод следующим образом:

```csharp
await client.GetRelationships()
```

Он возвращает `Azure.Pageable<T>` или `Azure.AsyncPageable<T>` , в зависимости от того, используется ли синхронная или асинхронная версия вызова.

Ниже приведен пример, в котором извлекается список связей:

```csharp
public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
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
                    Console.WriteLine(relJson);
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

```
Теперь можно вызвать этот метод, чтобы увидеть исходящие отношения двойников следующим образом:

```csharp
await FindOutgoingRelationshipsAsync(client, twin_Id);
```
Полученные связи можно использовать для перехода к другим двойников в графе. Для этого прочтите `target` поле из возвращаемой связи и используйте его в качестве идентификатора для следующего вызова `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Поиск входящих отношений для цифрового двойника

В Azure Digital двойников также есть API, который позволяет найти все *Входящие* * связи с заданным двойника. Это часто полезно для обратных переходов или при удалении двойника.

Предыдущий пример кода был сосредоточен на поиске исходящих связей от двойника. Следующий пример структурирован аналогично, но находит *Входящие* связи с двойника.

Обратите внимание, что `IncomingRelationship` вызовы не возвращают полный текст связи.

```csharp
public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine(incomingRel);

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }
```

Теперь этот метод можно вызвать для просмотра входящих отношений двойников следующим образом:

```csharp
await FindIncomingRelationshipsAsync(client, twin_Id);
```
### <a name="list-all-twin-properties-and-relationships"></a>Вывод списка всех свойств и отношений двойника

Используя описанные выше методы для перечисления входящих и исходящих отношений с двойника, можно создать метод, который выводит полные сведения двойника, включая свойства двойника и оба типа связей. Ниже приведен пример метода с именем `FetchAndPrintTwinAsync()` , демонстрирующий, как это сделать.

```csharp  
private static async Task FetchAndPrintTwinAsync(DigitalTwinsClient client, string twin_Id)
        {
            BasicDigitalTwin twin;
            Response<string> res = client.GetDigitalTwin(twin_Id);
            twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
            
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }
```

Теперь эту функцию можно вызвать в методе Main следующим образом: 

```csharp
await FetchAndPrintTwinAsync(client, targetId);
```
## <a name="delete-relationships"></a>Удаление связей

Первый параметр указывает исходный двойника (двойника, где исходит связь). Другой параметр — идентификатор связи. Вам потребуется идентификатор двойника и идентификатор связи, так как идентификаторы связей уникальны только в пределах области двойника.

```csharp
private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
```

Теперь можно вызвать этот метод, чтобы удалить связь следующим образом:

```csharp
await DeleteRelationship(client, srcId, relId);
```
## <a name="create-a-twin-graph"></a>Создание графа двойника 

Следующий готовый к запуску фрагмент кода использует операции связи из этой статьи для создания графа двойника из цифровых двойников и связей.

### <a name="set-up-the-runnable-sample"></a>Настройка готового к запуску примера

В фрагменте кода используется [*Room.jsна*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) определениях моделей и [*Floor.js*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) из [*учебника: изучение Azure Digital двойников с примером клиентского приложения*](tutorial-command-line-app.md). Вы можете использовать эти ссылки, чтобы перейти непосредственно к файлам или загрузить их как часть полного [примера проекта.](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) 

Перед запуском образца выполните следующие действия.
1. Скачайте файлы модели, поместите их в свой проект и замените `<path-to>` заполнители в приведенном ниже коде, чтобы сообщить программе, где их можно найти.
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
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload models");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            string dtdl1 = File.ReadAllText("<path-to>/Floor.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the models to the service
            await client.CreateModelsAsync(typeList);

            //Create new (Floor) digital twin
            BasicDigitalTwin floorTwin = new BasicDigitalTwin();
            string srcId = "myFloorID";
            floorTwin.Metadata = new DigitalTwinMetadata();
            floorTwin.Metadata.ModelId = "dtmi:example:Floor;1";
            //Floor twins have no properties, so nothing to initialize
            //Create the twin
            await client.CreateDigitalTwinAsync(srcId, JsonSerializer.Serialize<BasicDigitalTwin>(floorTwin));
            Console.WriteLine("Twin created successfully");

            //Create second (Room) digital twin
            BasicDigitalTwin roomTwin = new BasicDigitalTwin();
            string targetId = "myRoomID";
            roomTwin.Metadata = new DigitalTwinMetadata();
            roomTwin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            roomTwin.CustomProperties = props;
            //Create the twin
            await client.CreateDigitalTwinAsync(targetId, JsonSerializer.Serialize<BasicDigitalTwin>(roomTwin));
            
            //Create relationship between them
            await CreateRelationship(client, srcId, targetId, "contains");
            Console.WriteLine();

            //Print twins and their relationships
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete the relationship
            Console.WriteLine("Deleting the relationship");
            await DeleteRelationship(client, srcId, $"{srcId}-contains->{targetId}");
            Console.WriteLine();

            //Print twins and their relationships again
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        private async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }

        }

        private static async Task FetchAndPrintTwinAsync(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<string> res = client.GetDigitalTwin(twin_Id);
            twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        private static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
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
                    Console.WriteLine(relJson);
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine(incomingRel.RelationshipId);

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
    }
}
```

Ниже приведены выходные данные консоли для приведенной выше программы. 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Выходные данные консоли, отображающие сведения о двойника, входящие и исходящие связи двойников." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Граф двойника является концепцией создания связей между двойников. Если вы хотите просмотреть визуальное представление графа двойника, см. раздел [_Visualization *](how-to-manage-graph.md#visualization) этой статьи. 

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Создание графа двойника из электронной таблицы

В практических случаях двойника иерархии часто создаются на основе данных, хранящихся в другой базе данных или, возможно, в электронной таблице. В этом разделе показано, как можно проанализировать электронную таблицу.

Рассмотрим следующую таблицу данных, описывающую набор цифровых двойников и связей, которые будут созданы.

| Идентификатор модели| Идентификатор двойника (должен быть уникальным) | Имя связи | Идентификатор целевого двойника | Данные инициализации двойника |
| --- | --- | --- | --- | --- |
| дтми: пример: Floor; 1 | Floor1 |  содержит | Room1 |{"Температура": 80, "влажность": 60}
| дтми: пример: Floor; 1 | Floor0 |  has      | Room0 |{"Температура": 70, "влажность": 30}
| дтми: пример: комната; 1  | Room1 | 
| дтми: пример: комната; 1  | Room0 |

В следующем примере кода используется [API Microsoft Graph](/graph/overview) для чтения электронной таблицы и создания графа Двойникаов Azure Digital двойников на основе результатов.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string modelId = row[0].GetString();
    string sourceId = row[1].GetString();
    string relName = row[2].GetString();
    string targetId = row[3].GetString();
    string initData = row[4].GetString();
    
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (sourceId != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = sourceId,
            TargetId = targetId,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    twin.Metadata = new DigitalTwinMetadata() { ModelId = modelId };
    
    try
    {
        await client.CreateDigitalTwinAsync(sourceId, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
       Console.WriteLine($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            await client.CreateRelationshipAsync(rec.sourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine($"Error {e.Status}: {e.Message}");
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