---
title: Написание кода для клиентского приложения
titleSuffix: Azure Digital Twins
description: Руководство по написанию минимального объема кода для клиентского приложения с помощью пакета SDK для .NET (C# ).
author: cschormann
ms.author: cschorm
ms.date: 05/05/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: deb69f6ca8f1499f43c12d606434719571a1f400
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027879"
---
# <a name="coding-with-the-azure-digital-twins-apis"></a>Написание кода с помощью API Azure Digital Twins

Часто разработчики, работающие с Azure Digital Twins, пишут клиентское приложение для взаимодействия со своим экземпляром службы Azure Digital Twins. Этот учебник предназначен для разработчиков и содержит вводные сведения о программировании для службы Azure Digital Twins с помощью [клиентской библиотеки Digital Twins Интернета вещей Azure для .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). В нем пошагово описывается процесс написания консольного клиентского приложения на C# с самого начала.

## <a name="prerequisites"></a>Предварительные требования

В этом учебнике настройка и работа над проектом выполняется в командной строке. Таким образом, для выполнения упражнений вы можете использовать любой редактор кода.

Для начала работы вам потребуется следующее.
* Любой редактор кода.
* **.NET Core 3.1** на вашем компьютере разработчика. Вы можете скачать эту версию пакета SDK .NET Core для нескольких платформ по этой ссылке: [Загрузка .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

[!INCLUDE [Azure Digital Twins tutorials: instance prereq](../../includes/digital-twins-tutorial-prereq-instance.md)]

## <a name="set-up-project"></a>Настройка проекта

Когда вы будете готовы к работе со своим экземпляром Azure Digital Twins, начните с настройки проекта клиентского приложения. 

Откройте на компьютере командную строку или другое окно консоли и создайте пустой каталог проекта, в котором будете сохранять свою работу в процессе изучения данного учебника. Дайте этому каталогу любое имя (например, *DigitalTwinsCodeTutorial*).

Перейдите в новый каталог.

В этом каталоге проекта создайте пустой проект консольного приложения .NET. В командном окне выполните следующую команду, чтобы создать минимальный проект C# для консоли:

```cmd/sh
dotnet new console
```

В вашем каталоге будет создано несколько файлов, в том числе один с именем *Program.cs*, где вы будете писать большую часть своего кода.

Затем добавьте две необходимые зависимости для работы с Azure Digital Twins.

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.2
dotnet add package Azure.identity
```

Первая зависимость — [клиентская библиотека Digital Twins Интернета вещей Azure для .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Вторая зависимость предоставляет инструменты для проверки подлинности в Azure.

Оставьте окно командной строки открытым, так как вы будете продолжать использовать его на протяжении всей работы с учебником.

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

Для выполнения проверки подлинности вам потребуются следующие сведения:
* *идентификатор каталога (арендатора)* для вашей подписки;
* *идентификатор приложения (клиента)* , созданный при настройке экземпляра Azure Digital Twins, которая была выполнена ранее;
* *имя узла* вашего экземпляра Azure Digital Twins.

>[!TIP]
> Если вы не знаете свой *идентификатор каталога (арендатора)* , его можно получить, выполнив в [Azure Cloud Shell](https://shell.azure.com) следующую команду:
> 
> ```azurecli-interactive
> az account show --query tenantId
> ```

В файле *Program.cs* вставьте следующий код под строкой для вывода на печать "Hello, World!" в методе `Main`. Задайте следующие значения: `adtInstanceUrl` для *hostName* вашего экземпляра Azure Digital Twins, `clientId` для *идентификатора приложения* и `tenantId` для *идентификатора каталога*.

```csharp
string clientId = "<your-application-ID>";
string tenantId = "<your-directory-ID>";
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
Console.WriteLine($"Service client created – ready to go");
```

Сохраните файл. 

Обратите внимание, что в этом примере используются интерактивные учетные данные браузера:
```csharp
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
```

При использовании такого типа учетных данных открывается окно браузера с запросом учетных данных Azure. 

>[!NOTE]
> Сведения о других типах учетных данных см. в документации для [библиотек проверки подлинности платформы удостоверений Майкрософт](../active-directory/develop/reference-v2-libraries.md).

В командном окне запустите этот код с помощью следующей команды: 

```cmd/sh
dotnet run
```

При первом запуске будут восстановлены зависимости, а затем выполнена программа. 
* Если никакие ошибки не возникли, программа напечатает, что *клиент службы создан и готов к работе*.
* В этом проекте пока отсутствует обработка ошибок, и, если возникнут какие-либо ошибки, вы увидите исключение, порожденное кодом.

### <a name="upload-a-model"></a>Отправка модели

В Azure Digital Twins отсутствует встроенный словарь домена. Типы элементов в вашей среде, которые вы можете представить в Azure Digital Twins, определяются вами с помощью **моделей**. [Модели](concepts-models.md) похожи на классы в объектно-ориентированных языках программирования. Они предоставляют определяемые пользователем шаблоны для [цифровых двойников](concepts-twins-graph.md), экземпляры которых будут затем создаваться на основе этих шаблонов. Они написаны на JSON-подобном языке, называемом **языком определения цифровых двойников (DTDL)** .

При создании решения Azure Digital Twins в первую очередь необходимо определить хотя бы одну модель в файле DTDL.

В каталоге, где вы создали свой проект, создайте новый файл *JSON* с именем *SampleModel.json*. Вставьте в этот файл следующий код: 

```json
{
  "@id": "dtmi:com:contoso:SampleModel;1",
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
> Существует не зависящий от языка [пример проверяющего элемента управления DTDL](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator), который можно использовать для проверки документов модели, чтобы убедиться в правильности DTDL. Он построен на библиотеке средства синтаксического анализа DTDL, о которой вы можете подробнее узнать в разделе [Практическое руководство. Анализ и проверка моделей](how-to-use-parser.md).

Теперь добавьте еще немного кода в *Program.cs*, чтобы отправить только что созданную модель в свой экземпляр Azure Digital Twins.

Сначала добавьте несколько инструкций `using` в начало файла:

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Models;
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
В выходных данных появится текст "Upload a model" (Отправить модель). Однако отсутствуют выходные данные, в которых сообщается об успешном или неуспешном выполнении отправки.

Чтобы добавить вывод сообщения об успешности или неуспешности отправки моделей, добавьте следующий код сразу после предыдущего раздела:

```csharp
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Прежде чем снова запустить программу для проверки этого нового кода, вспомните, что в последний раз, когда программа запускалась, вы уже отправили свою модель. Azure Digital Twins не позволяет отправлять одну и ту же модель дважды, поэтому при повторном запуске программы должно возникнуть исключение.

Теперь запустите программу еще раз с помощью следующей команды в вашем командном окне:

```cmd/sh
dotnet run
```

Программа должна выдать исключение. Когда вы пытаетесь отправить уже отправленную модель, служба возвращает через REST API ошибку "неверный запрос". В результате пакет SDK клиента Azure Digital Twins, в свою очередь, генерирует исключение для каждого кода возврата службы, кроме успешного. 

В следующем разделе говорится об исключениях, подобных этому, и о том, как обрабатывать их в вашем коде.

### <a name="catch-errors"></a>Перехват ошибок

Чтобы предотвратить сбой программы, можно добавить код исключения вокруг кода отправки модели. Заключите существующий клиентский вызов `client.CreateModelsAsync` в обработчик try/catch, например так:

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

Если вы сейчас запустите программу с помощью `dotnet run` в командном окне, то увидите возвращенный код ошибки. Выходные данные выглядят примерно так:

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Load model: 409:Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"DocumentAlreadyExists","message":"A document with same identifier already exists.","details":[]}}

Headers:
api-supported-versions: REDACTED
Date: Tue, 05 May 2020 01:57:51 GMT
Content-Length: 115
Content-Type: application/json; charset=utf-8

Type name: : dtmi:com:contoso:SampleModel;1
```

Начиная с этого момента все вызовы методов службы в этом учебнике будут заключаться в обработчики try/catch.

### <a name="create-digital-twins"></a>Создание цифровых двойников

Теперь, когда модель отправлена в Azure Digital Twins, вы можете использовать это определение модели для создания **цифровых двойников**. [Цифровые двойники](concepts-twins-graph.md) являются экземплярами модели и представляют сущности в вашей бизнес-среде — такие как датчики на ферме, комнаты в здании или фары автомобиля. В этом разделе создается несколько цифровых двойников на основе модели, которую вы отправили ранее.

Добавьте вверху новую инструкцию `using`, так как вам потребуется встроенный сериализатор .NET Json в `System.Text.Json`:

```csharp
using System.Text.Json;
using Azure.DigitalTwins.Core.Serialization;
```

Затем добавьте следующий код в конец метода `Main`, чтобы создать и инициализировать три цифровых двойника на основе этой модели.

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

В командном окне запустите программу с помощью команды `dotnet run`. Затем повторите эту команду, чтобы запустить программу еще раз. 

Обратите внимание, что при повторном создании двойников ошибки не возникают, даже если двойники уже существуют после первого запуска. В отличие от создания модели, создание двойников на уровне REST — это вызов *PUT* с семантикой *upsert*. Это означает, что если двойник уже существует, при попытке создать его снова он будет заменен. Никакая обработка ошибок не требуется.

### <a name="create-relationships"></a>Создавать связи

Далее вы можете создать **связи** между этими двойниками, чтобы подключить их к **графу двойников**. [Графы двойников](concepts-twins-graph.md) используются для представления всей вашей среды.

Чтобы можно было создавать связи, добавьте инструкцию `using` для базового типа связи в пакете SDK. Пропустите этот шаг, если инструкция была добавлена ранее.
```csharp
using Azure.DigitalTwins.Core.Serialization;
```

Теперь добавьте новый статический метод в класс `Program` под методом `Main`:
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

Затем добавьте следующий код в конец метода `Main` для вызова кода `CreateRelationship`:
```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

В командном окне запустите программу с помощью команды `dotnet run`.

Обратите внимание, что Azure Digital Twins не позволит вам создать связь, если связь с таким идентификатором уже существует, поэтому если вы запустите программу несколько раз, то увидите исключения при создании связи. Этот код перехватывает исключения и игнорирует их. 

### <a name="list-relationships"></a>Список связей

Следующий код, который вы добавите, позволит увидеть список созданных связей.

Добавьте в класс `Program` следующий новый метод.

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (string rel in results)
        {
            var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
            Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
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

В командном окне запустите программу с помощью команды `dotnet run`. Должен появиться список всех созданных связей.

### <a name="query-digital-twins"></a>Запрашивание цифровых двойников

Главной особенностью Azure Digital Twins является возможность просто и эффективно [запрашивать](concepts-query-language.md) граф двойников, чтобы получать ответы о вашей среде.

В последнем разделе кода, который вы добавите в этом учебнике, выполняется запрос к экземпляру Azure Digital Twins. Запрос, используемый в этом примере, возвращает все цифровые двойники в экземпляре.

Добавьте следующий код в конец метода `Main`:

```csharp
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
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
using Azure.DigitalTwins.Core.Models;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string clientId = "<your-application-ID>";
            string tenantId = "<your-directory-ID>";
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
            var credentials = new InteractiveBrowserCredential(tenantId, clientId);
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
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
            AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
            await foreach (ModelData md in modelDataList)
            {
                Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
            twinData.CustomProperties.Add("data", $"Hello World!");
    
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
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

            // Run a query    
            AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
            await foreach (string twin in result)
            {
                object jsonObj = JsonSerializer.Deserialize<object>(twin);
                string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
                Console.WriteLine(prettyTwin);
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
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (string rel in results)
                {
                    var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
                    Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>Очистка ресурсов
 
Экземпляр из этого учебника можно использовать в следующем [учебнике "Изучение основ с помощью примера клиентского приложения"](tutorial-command-line-app.md). Если вы планируете продолжить работу со следующим учебником, то можете сохранить настроенный здесь экземпляр Azure Digital Twins.
 
Если ресурсы, созданные для этого учебника, вам больше не нужны, можете удалить их.

В [Azure Cloud Shell](https://shell.azure.com) можно удалить все ресурсы Azure в группе ресурсов с помощью команды [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). При этом удаляется группа ресурсов и экземпляр Azure Digital Twins.

> [!IMPORTANT]
> Удаление группы ресурсов — процесс необратимый. Группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. 

Откройте Azure Cloud Shell и выполните следующую команду, чтобы удалить группу ресурсов и все ее содержимое.

```azurecli-interactive
az group delete --name <your-resource-group>
```

Затем удалите регистрацию приложения Azure Active Directory, которую вы создали для своего клиентского приложения, с помощью следующей команды:

```azurecli
az ad app delete --id <your-application-ID>
```

Наконец, удалите папку проекта, созданную на локальном компьютере.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы с нуля создали консольное клиентское приложение консоли .NET. Вы написали код для этого клиентского приложения, чтобы выполнять базовые действия в экземпляре Azure Digital Twins.

Перейдите к следующему учебнику, чтобы изучить действия, которые можно выполнять с помощью такого примера клиентского приложения: 

> [!div class="nextstepaction"]
> [Руководство. Изучение основ с помощью примера клиентского приложения](tutorial-command-line-app.md)

Вы также можете расширить знания, полученные при написании кода для этого учебника, изучив дополнительные операции управления в статьях с инструкциями или ознакомившись с документацией по концепциям, чтобы узнать больше об элементах, с которыми вы работали в этом учебнике.
* [Практическое руководство. Управление настраиваемыми моделями](how-to-manage-model.md)
* [Основные понятия. Настраиваемые модели](concepts-models.md)
