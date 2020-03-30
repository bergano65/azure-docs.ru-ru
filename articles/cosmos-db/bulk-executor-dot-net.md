---
title: Использование библиотеки -NET с объемом перевозок в Azure Cosmos DB для операций массового импорта и обновления
description: Массовый импорт и обновление документов Azure Cosmos DB с помощью библиотеки -NET с помощью основного исполнителя.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: d7600267dcd196a9a5c06c29774ea21d582cd7ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246881"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Используйте библиотеку исполнителя .NET для выполнения оптовых операций в Azure Cosmos DB

В этом уроке содержатся инструкции по использованию библиотеки -NET для импорта и обновления документов в контейнер Azure Cosmos. Чтобы узнать о библиотеке исполнителей навалом и о том, как она помогает использовать массивную пропускную емкость и хранение, смотрите статью [обзора библиотеки исполнителей-исполнителей.](bulk-executor-overview.md) В этом уроке вы увидите образец приложения .NET, которое навалом импортирует случайно генерируемые документы в контейнер Azure Cosmos. Затем в нем показано, как можно массово обновить импортированные данные, указав исправления как операции для выполнения в определенных полях документа.

В настоящее время библиотека исполнителей навалом поддерживается только aPI Azure Cosmos DB S'L и API Gremlin. В этой статье описывается, как использовать библиотеку исполнителя .NET с учетными записями API S'L. Чтобы узнать об использовании библиотеки -NET с помощью aPI-аккаунтов Gremlin API, смотрите, [выполняйте оптовые операции в API Azure Cosmos DB Gremlin API.](bulk-executor-graph-dotnet.md)

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет установленной Visual Studio 2019, вы можете скачать и использовать [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Убедитесь, что вы включить "Azure развития" во время установки Visual Studio.

* Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.

* [Бесплатную пробную версию Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) можно использовать без подписки Azure, без оплаты и каких-либо обязательств. Или можно использовать [эмулятор Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) с конечным пунктом. `https://localhost:8081` Первичный ключ предоставляется в разделе [Выполнение проверки подлинности запросов](local-emulator.md#authenticating-requests).

* Создайте учетную запись API SQL Azure Cosmos DB, используя шаги, описанные в разделе [Создание учетной записи базы данных](create-sql-api-dotnet.md#create-account) в статье о начале работы с .NET.

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь перейдем к работе с кодом, загрузив образец приложения .NET с GitHub. Это приложение выполняет объемные операции на данных, хранящихся в вашей учетной записи Azure Cosmos. Чтобы клонировать приложение, откройте запрос команды, перейдите в каталог, где вы хотите скопировать его и запустить следующую команду:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Клонированный репозиторий содержит два образца "BulkImportSample" и "BulkUpdateSample". Вы можете открыть один из примеров приложений, обновить строки подключения в файле App.config, добавив строки подключения учетной записи Azure Cosmos DB, создать решение и запустить его.

Приложение "BulkImportSample" генерирует случайные документы и массово импортирует их на счет Azure Cosmos. Приложение BulkUpdateSample массово обновляет импортированные документы, указывая исправления как операции, выполняемые над определенными полями документа. В следующих разделах вы рассмотрите код в каждом из этих примеров приложений.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Объем данных об импорте в учетную запись Azure Cosmos

1. Перейдите в папку BulkImportSample и откройте файл BulkImportSample.sln.  

2. Строки подключения Azure Cosmos DB извлечены из файла App.config, как показано в следующем коде:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Импортер навалом создает новую базу данных и контейнер с именем базы данных, именем контейнера и значениями пропускной связи, указанными в файле App.config.

3. Затем объект DocumentClient инициализируется в режиме прямого подключения TCP:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. Объект BulkExcutor инициализирован с высоким значением повторной попытки для времени ожидания и задушены запросами. Далее для них устанавливается значение 0, чтобы передать управление перегрузкой в объект BulkExecutor на время его существования.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. Приложение вызывает API BulkImportAsync. Библиотека .NET предоставляет две перегрузки API массового импорта - один, который принимает список серийных документов JSON, а другой, который принимает список десериализованных документов POCO. Чтобы узнать больше об определениях каждого из этих перегруженных методов, обратитесь к [документации API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **Метод BulkImportAsync принимает следующие параметры:**
   
   |**Параметр**  |**Описание** |
   |---------|---------|
   |enableUpsert    |   Флаг, позволяющий upsert операции на документах. Если документ с данным идентификатором уже существует, он обновляется. По умолчанию установлено значение false.      |
   |disableAutomaticIdGeneration    |    Параметр, отключающий автоматическое создание идентификатора. По умолчанию установлено значение true.     |
   |maxConcurrencyPerPartitionKeyRange    | Максимальная степень параллелизма для диапазона ключей разделов. Значение null приведет к использованию значения по умолчанию (20). |
   |maxInMemorySortingBatchSize     |  Максимальное количество документов, которые извлекаются из перечисленного документа, который передается на вызов API на каждом этапе. Для фазы сортировки в памяти, которая происходит до массового импорта, установление этого параметра является нулевым, приведет к тому, что библиотека будет использовать минимальное значение по умолчанию (documents.count, 1000000).       |
   |cancellationToken    |    Токен отмены изящно выйти из операции массового импорта.     |

   **Определение объекта ответа массового импорта**. Результат вызова API массового импорта содержит следующие атрибуты:

   |**Параметр**  |**Описание**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  Общее количество документов, которые были успешно импортированы из общего числа документов, предоставленных на колл API массового импорта.       |
   |TotalRequestUnitsConsumed (double)   |   Общее число единиц запросов (ЕЗ), потребляемых вызовом API массового импорта.      |
   |TotalTimeTaken (TimeSpan)    |   Общее время, занесено вызовом API массового импорта для завершения выполнения.      |
   |BadInputDocuments (список\<объектов>)   |     Список документов с неверным форматом, которые не были успешно импортированы при вызове API массового импорта. Исправить возвращенные документы и повторить импорт. Документы с неверным форматом включают в себя документы, значение идентификаторов которых не является строкой (NULL или любой другой тип данных считается недействительным).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Массовые обновления данных в учетной записи Azure Cosmos

С помощью API BulkUpdateAsync можно обновить имеющиеся документы. В этом примере вы `Name` установите поле на `Description` новое значение и удалите поле из существующих документов. Для получения полного набора поддерживаемых операций обновления обратитесь к [документации API.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)

1. Перейдите в папку BulkUpdateSample и откройте файл BulkUpdateSample.sln.  

2. Определите элементы обновления вместе с соответствующими операциями обновления поля. В этом примере `SetUpdateOperation` вы будете `Name` использовать `UnsetUpdateOperation` для `Description` обновления поля и удаления поля из всех документов. Вы также можете выполнять другие операции, такие как увеличение значения в поле документа по определенному значению, передача определенных значений в поле массива или удаление определенного значения из него. Дополнительные сведения о различных методах, предоставляемых API массового обновления, см. в [документации по API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. Приложение вызывает API BulkUpdateAsync. Чтобы узнать об определении метода BulkUpdateAsync, обратитесь к [документации API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **Метод BulkUpdateAsync принимает следующие параметры:**

   |**Параметр**  |**Описание** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Максимальная степень параллелизма на диапазонключа ключа раздела, установив этот параметр на нуле, сделает библиотеку для использования значения по умолчанию (20).   |
   |maxInMemorySortingBatchSize    |    Максимальное количество элементов обновления, извлеченных из перечисления элементов обновления, передается вызову API на каждом этапе. Для фазы сортировки в памяти, которая происходит до массового обновления, установление этого параметра для снуления приведет к тому, что библиотека будет использовать минимальное значение по умолчанию (updateItems.count, 1000000).     |
   | cancellationToken|Токен отмены изящно выйти из операции массового обновления. |

   **Определение объекта ответа массового обновления**. Результат вызова API массового обновления содержит следующие атрибуты:

   |**Параметр**  |**Описание** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   Количество документов, которые были успешно обновлены из общего числа документов, предоставленных на вызов API массового обновления.      |
   |TotalRequestUnitsConsumed (double)   |    Общие единицы запроса (RUs), потребляемые вызовом API массового обновления.    |
   |TotalTimeTaken (TimeSpan)   | Общее время, занесено на вызов API массового обновления для завершения выполнения. |
    
## <a name="performance-tips"></a>Советы по улучшению производительности 

Рассмотрим следующие моменты для повышения производительности при использовании библиотеки исполнителей навалом:

* Для достижения наилучшей производительности запустите приложение с виртуальной машины Azure, которая находится в том же регионе, что и область записи учетной записи Azure Cosmos.  

* Рекомендуется мгновенно использовать один `BulkExecutor` объект для всего приложения в рамках одной виртуальной машины, которая соответствует определенному контейнеру Azure Cosmos.  

* Так как одно объемное выполнение API потребляет большую часть процессора и итогового итоговой системы клиентской машины (это происходит путем нереста нескольких задач внутри компании). Избегайте нереста нескольких одновременных задач в процессе приложения, выполняющих вызовы API навалочных операций. Если один объем вызова API, выполняемый на одной виртуальной машине, не может потреблять всю пропускную емкость контейнера (если пропускная емкость контейнера > 1 млн руб./с), предпочтительнее создать отдельные виртуальные машины для одновременного выполнения вызовов API навалом.  

* Убедитесь, `InitializeAsync()` что метод вызывается после мгновенного получения объекта BulkExecutor для получения карты раздела контейнера «Космос» целевого контейнера.  

* Убедитесь, что в файле App.Config приложения включен параметр **gcServer** для обеспечения лучшей производительности.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Библиотека создает трассировки, которые могут записываться в файл журнала, либо в консоль. Чтобы включить оба кода, добавьте следующий код в файл App.Config вашего приложения.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать о деталях пакета Nuget и [bulk executor SDK details](sql-api-sdk-bulk-executor-dot-net.md)примечаниях к выпуску, см.
