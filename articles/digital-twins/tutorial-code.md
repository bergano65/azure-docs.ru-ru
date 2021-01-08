---
title: Руководство. Написание кода для клиентского приложения
titleSuffix: Azure Digital Twins
description: Руководство по написанию минимального объема кода для клиентского приложения с помощью пакета SDK для .NET (C# ).
author: baanders
ms.author: baanders
ms.date: 11/02/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 22600f6519a44e71ae5c4b59b0165401b54e55fd
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964547"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Руководство по Написание кода с помощью API Azure Digital Twins

Часто разработчики, работающие с Azure Digital Twins, пишут клиентское приложение для взаимодействия со своим экземпляром службы Azure Digital Twins. Этот учебник предназначен для разработчиков и содержит вводные сведения о программировании для службы Azure Digital Twins с помощью [пакета SDK Azure Digital Twins для .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). В нем пошагово описывается процесс написания консольного клиентского приложения на C# с самого начала.

> [!div class="checklist"]
> * Настройка проекта
> * Начало создания кода проекта   
> * Полный пример кода
> * Очистка ресурсов
> * Дальнейшие действия

## <a name="prerequisites"></a>Предварительные требования

В этом учебнике настройка и работа над проектом выполняется в командной строке. Таким образом, для выполнения упражнений вы можете использовать любой редактор кода.

Для начала работы вам потребуется следующее.
* Любой редактор кода.
* **.NET Core 3.1** на вашем компьютере разработчика. Вы можете скачать эту версию пакета SDK .NET Core для нескольких платформ по этой ссылке: [Загрузка .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

### <a name="prepare-an-azure-digital-twins-instance"></a>Подготовка экземпляра Azure Digital Twins

[!INCLUDE [Azure Digital Twins: instance prereq](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

## <a name="set-up-project"></a>Настройка проекта

Когда вы будете готовы к работе со своим экземпляром Azure Digital Twins, начните с настройки проекта клиентского приложения. 

Откройте на компьютере командную строку или другое окно консоли и создайте пустой каталог проекта, в котором будете сохранять свою работу в процессе изучения данного учебника. Дайте этому каталогу любое имя (например, *DigitalTwinsCodeTutorial*).

Перейдите в новый каталог.

В этом каталоге проекта **создайте пустой проект консольного приложения .NET**. В командном окне можно выполнить следующую команду, чтобы создать минимальный проект C# для консоли:

```cmd/sh
dotnet new console
```

В вашем каталоге будет создано несколько файлов, в том числе один с именем *Program.cs*, где вы будете писать большую часть своего кода.

Оставьте окно командной строки открытым, так как вы будете продолжать использовать его на протяжении всей работы с учебником.

Затем **добавьте в проект две зависимости**, которые понадобятся для работы с Azure Digital Twins. По приведенным ниже ссылкам можно перейти к пакетам в NuGet, в котором находятся команды консоли (в том числе для .NET CLI), и добавить в проект версию каждой из них.
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Это пакет для [пакета SDK Azure Digital Twins для .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). Добавьте последнюю версию.
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). Эта библиотека предоставляет инструменты для проверки подлинности в Azure. Добавьте версию 1.2.2.

## <a name="get-started-with-project-code"></a>Начало создания кода проекта

В этом разделе вы начнете писать код своего нового проекта приложения для работы с Azure Digital Twins. Вы будете писать код для следующих действий:
* проверки подлинности в службе;
* отправки модели;
* выявления ошибок;
* создания цифровых двойников;
* создания связей;
* выполнения запросов цифровых двойников.

В конце урока есть раздел, в котором приведен полный код. Вы можете использовать его в качестве справочного материала для проверки вашей программы по мере ее создания.

Для начала откройте файл *Program.cs* в любом редакторе кода. Вы увидите минимальный шаблон кода, который выглядит следующим образом:

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

В первую очередь добавьте несколько строк `using` в верхней части кода, чтобы включить необходимые зависимости.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

Далее вы будете добавлять в этот файл код для некоторых функций. 

### <a name="authenticate-against-the-service"></a>Проверка подлинности в службе

Первое, что должно будет делать ваше приложение, — это проходить проверку подлинности в службе Azure Digital Twins. Затем можно создать класс клиента службы для доступа к функциям пакета SDK.

Для проверки подлинности требуется *имя узла* экземпляра Azure Digital Twins.

В файле *Program.cs* вставьте следующий код под строкой для вывода на печать "Hello, World!" в методе `Main`. Задайте значение `adtInstanceUrl` в качестве *имя узла* для экземпляра Azure Digital Twins.

```csharp
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>"; 
var credential = new DefaultAzureCredential();
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
Console.WriteLine($"Service client created – ready to go");
```

Сохраните файл. 

В командном окне запустите этот код с помощью следующей команды: 

```cmd/sh
dotnet run
```

При первом запуске будут восстановлены зависимости, а затем выполнена программа. 
* Если никакие ошибки не возникли, программа напечатает, что *клиент службы создан и готов к работе*.
* В этом проекте пока отсутствует обработка ошибок, и, если возникнут какие-либо ошибки, вы увидите исключение, сгенерированное кодом.

### <a name="upload-a-model"></a>Отправка модели

В Azure Digital Twins отсутствует встроенный словарь домена. Типы элементов в вашей среде, которые вы можете представить в Azure Digital Twins, определяются вами с помощью **моделей**. [Модели](concepts-models.md) похожи на классы в объектно-ориентированных языках программирования. Они предоставляют определяемые пользователем шаблоны для [цифровых двойников](concepts-twins-graph.md), экземпляры которых будут затем создаваться на основе этих шаблонов. Они написаны на JSON-подобном языке, называемом **языком определения цифровых двойников (DTDL)** .

При создании решения Azure Digital Twins в первую очередь необходимо определить хотя бы одну модель в файле DTDL.

В каталоге, где вы создали свой проект, создайте новый файл *JSON* с именем *SampleModel.json*. Вставьте в этот файл следующий код: 

```json
{
  "@id": "dtmi:example:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> Если для работы с этим учебником вы используете Visual Studio, можно выбрать только что созданный файл JSON и установить для свойства *Копировать в выходной каталог* в инспекторе свойств значение *Копировать более позднюю версию* или *Копировать всегда*. Это позволит Visual Studio найти файл JSON с путем по умолчанию, когда вы будете запускать программу клавишей **F5** при работе с оставшейся частью учебника.

> [!TIP] 
> Существует не зависящий от языка [пример проверяющего элемента управления DTDL](/samples/azure-samples/dtdl-validator/dtdl-validator), который можно использовать для проверки документов модели, чтобы убедиться в правильности DTDL. Он создан на основе библиотеки средства синтаксического анализа DTDL, о которой вы можете подробнее узнать в разделе [*Практическое руководство. Анализ и проверка моделей*](how-to-parse-models.md).

Теперь добавьте еще немного кода в *Program.cs*, чтобы отправить только что созданную модель в свой экземпляр Azure Digital Twins.

Сначала добавьте несколько инструкций `using` в начало файла:

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
```

Затем подготовьте использование асинхронных методов в пакете SDK службы C#, изменив сигнатуру метода `Main`, чтобы разрешить асинхронное выполнение. 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> Использование `async` не является строго обязательным, так как пакет SDK предоставляет также и синхронные версии всех вызовов. В упражнениях этого учебника используется `async`.

Далее идет первый фрагмент кода, который взаимодействует со службой Azure Digital Twins. Этот код загружает с диска созданный вами файл DTDL и отправляет его в ваш экземпляр службы Azure Digital Twins. 

Вставьте следующий код под кодом проверки подлинности, который вы добавили ранее.

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

В командном окне запустите программу с помощью следующей команды: 

```cmd/sh
dotnet run
```
В выходных данных появится текст "Upload a model" (Отправить модель), указывающий на выполнение этого фрагмента кода. Но выходные данные, в которых сообщается об успешности отправки, пока отсутствуют.

Чтобы добавить вывод сообщения об успешности отправки всех моделей в экземпляр, добавьте следующий код сразу после предыдущего раздела:

```csharp
// Read a list of models back from the service
Console.WriteLine("Models uploaded to the instance:");
AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
await foreach (DigitalTwinsModelData md in modelDataList)
{
    Console.WriteLine($"{md.Id}");
}
```

**Прежде чем снова запустить программу для проверки этого нового кода**, вспомните, что в последний раз, когда программа запускалась, вы уже отправили свою модель. В службе Azure Digital Twins нельзя отправить одну и ту же модель дважды. Поэтому когда вы попытаетесь сделать это, программа выдаст исключение.

Учитывая это, запустите программу еще раз с помощью следующей команды в своем командном окне:

```cmd/sh
dotnet run
```

Программа должна выдать исключение. Когда вы пытаетесь отправить уже отправленную модель, служба возвращает через REST API ошибку "неверный запрос". В результате пакет SDK клиента Azure Digital Twins, в свою очередь, генерирует исключение для каждого кода возврата службы, кроме успешного. 

В следующем разделе говорится об исключениях, подобных этому, и о том, как обрабатывать их в вашем коде.

### <a name="catch-errors"></a>Перехват ошибок

Чтобы предотвратить сбой программы, можно добавить код исключения вокруг кода отправки модели. Заключите существующий клиентский вызов `await client.CreateModelsAsync(typeList)` в обработчик try/catch, например так:

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

Если вы сейчас запустите программу с помощью `dotnet run` в командном окне, то будет возвращен код ошибки. В выходных данных кода для создания модели отобразится такая ошибка:

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="Выходные данные программы с сообщением &quot;409:Service request failed. Состояние: 409 (Conflict)&quot; (409: ошибка запроса службы. 409 (Конфликт)), после которого идет расшифровка с указанием того, что модель dtmi:example:SampleModel;1 уже существует":::

Начиная с этого момента все вызовы методов службы в этом учебнике будут заключаться в обработчики try/catch.

### <a name="create-digital-twins"></a>Создание цифровых двойников

Теперь, когда модель отправлена в Azure Digital Twins, вы можете использовать это определение модели для создания **цифровых двойников**. [Цифровые двойники](concepts-twins-graph.md) являются экземплярами модели и представляют сущности в вашей бизнес-среде — такие как датчики на ферме, комнаты в здании или фары автомобиля. В этом разделе создается несколько цифровых двойников на основе модели, которую вы отправили ранее.

Затем добавьте следующий код в конец метода `Main`, чтобы создать и инициализировать три цифровых двойника на основе этой модели.

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:example:SampleModel;1";
twinData.Contents.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twinData.Id, twinData);
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

В командном окне запустите программу с помощью команды `dotnet run`. В выходных данных найдите сообщения о том, что *sampleTwin-0*, *sampleTwin-1* и *sampleTwin-2* созданы. 

Затем снова выполните программу. 

Обратите внимание, что при повторном создании двойников ошибки не возникают, даже если двойники уже существуют после первого запуска. В отличие от создания модели, создание двойников на уровне REST — это вызов *PUT* с семантикой *upsert*. Это означает, что попытка повторного создания уже имеющегося двойника приведет к замене исходного двойника. Сообщение об ошибке при этом не выводится.

### <a name="create-relationships"></a>Создавать связи

Далее вы можете создать **связи** между этими двойниками, чтобы подключить их к **графу двойников**. [Графы двойников](concepts-twins-graph.md) используются для представления всей вашей среды.

Добавьте **новый статический метод** в класс `Program` под методом `Main` (код теперь включает два метода):

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
        await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Затем добавьте следующий код в конец метода `Main`, чтобы вызвать метод `CreateRelationship` и использовать только что написанный код:

```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

В командном окне запустите программу с помощью команды `dotnet run`. В выходных данных найдите сведения об успешном создании двух связей.

Обратите внимание, что Azure Digital Twins не позволит вам создать связь, если связь с таким идентификатором уже существует. Поэтому если вы запустите программу несколько раз, при создании связи возникнут исключения. Этот код перехватывает исключения и игнорирует их. 

### <a name="list-relationships"></a>Список связей

Следующий код, который вы добавите, позволит увидеть список созданных связей.

Добавьте следующий **новый метод** в класс `Program`:

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<BasicRelationship> results = client.GetRelationshipsAsync<BasicRelationship>(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (BasicRelationship rel in results)
        {
            Console.WriteLine($" -{rel.Name}->{rel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

Затем добавьте следующий код в конец метода `Main` для вызова кода `ListRelationships`:

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

В командном окне запустите программу с помощью команды `dotnet run`. Отобразится список всех созданных вами связей в выходных данных. Он будет выглядеть следующим образом:

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="Выходные данные программы с сообщением &quot;Twin sampleTwin-0 is connected to: contains->sampleTwin-1, -contains->sampleTwin-2&quot; (Двойник sampleTwin-0 подключен к: содержит->sampleTwin-1, -содержит->sampleTwin-2)":::

### <a name="query-digital-twins"></a>Запрашивание цифровых двойников

Главной особенностью Azure Digital Twins является возможность просто и эффективно [запрашивать](concepts-query-language.md) граф двойников, чтобы получать ответы о вашей среде.

В последнем разделе кода, который вы добавите в этом учебнике, выполняется запрос к экземпляру Azure Digital Twins. Запрос, используемый в этом примере, возвращает все цифровые двойники в экземпляре.

Добавьте этот оператор `using`, чтобы обеспечить использование класса `JsonSerializer` для представления сведений о цифровом двойнике:

```csharp
using System.Text.Json;
```

Затем добавьте следующий код в конец метода `Main`:

```csharp
// Run a query for all twins   
string query = "SELECT * FROM digitaltwins";
AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);

await foreach (BasicDigitalTwin twin in result)
{
    Console.WriteLine(JsonSerializer.Serialize(twin));
    Console.WriteLine("---------------");
}
```

В командном окне запустите программу с помощью команды `dotnet run`. В выходных данных должны быть указаны все цифровые двойники в этом экземпляре.

## <a name="complete-code-example"></a>Полный пример кода

На этом этапе у вас есть готовое клиентское приложение, способное выполнять базовые действия в Azure Digital Twins. Для справки ниже приведен полный код программы в файле *Program.cs*.

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>"; 
            
            var credential = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);

            // Upload the model to the service
            try {
                await client.CreateModelsAsync(typeList);
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
            }
            // Read a list of models back from the service
            Console.WriteLine("Models uploaded to the instance:");
            AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
            await foreach (DigitalTwinsModelData md in modelDataList)
            {
                Console.WriteLine($"{md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:example:SampleModel;1";
            twinData.Contents.Add("data", $"Hello World!");
            
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twinData.Id, twinData);
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query for all twins   
            string query = "SELECT * FROM digitaltwins";
            AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);
            
            await foreach (BasicDigitalTwin twin in result)
            {
                Console.WriteLine(JsonSerializer.Serialize(twin));
                Console.WriteLine("---------------");
            }
        }

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
                await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<BasicRelationship> results = client.GetRelationshipsAsync<BasicRelationship>(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (BasicRelationship rel in results)
                {
                    Console.WriteLine($" -{rel.Name}->{rel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>Очистка ресурсов
 
Экземпляр из этого руководства можно использовать при работе со следующим руководством [ *Изучение Azure Digital Twins с помощью примера клиентского приложения*](tutorial-command-line-app.md). Если вы планируете продолжить работу со следующим учебником, то можете сохранить настроенный здесь экземпляр Azure Digital Twins.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Наконец, удалите папку проекта, созданную на локальном компьютере.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы с нуля создали консольное клиентское приложение консоли .NET. Вы написали код для этого клиентского приложения, чтобы выполнять базовые действия в экземпляре Azure Digital Twins.

Перейдите к следующему учебнику, чтобы изучить действия, которые можно выполнять с помощью такого примера клиентского приложения: 

> [!div class="nextstepaction"]
> [*Руководство. Изучение Azure Digital Twins с помощью примера клиентского приложения*](tutorial-command-line-app.md)
