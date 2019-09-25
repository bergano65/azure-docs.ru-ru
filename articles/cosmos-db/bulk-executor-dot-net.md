---
title: Использование библиотеки NET выполнителя .NET для выполнения операций импорта и обновления в Azure Cosmos DB
description: Массовый импорт и обновление Azure Cosmos DB документов с помощью библиотеки .NET для NET выполнителя.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: d76426e738d78391b92b008e821672017520b7d2
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218404"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Используйте библиотеку .NET для NET выполнителя, чтобы выполнять групповые операции в Azure Cosmos DB

В этом учебнике приводятся инструкции по использованию библиотеки NET выполнителя .NET для импорта и обновления документов в контейнере Azure Cosmos. Сведения о библиотеке массового выполнителя и о том, как она помогает использовать большие пропускную способность и хранилище, см. в статье [Обзор библиотеки массового выполнителя](bulk-executor-overview.md) . В этом руководстве вы увидите пример приложения .NET, которое выполняет массовый импорт документов, созданных случайным образом, в контейнер Azure Cosmos. Затем в нем показано, как можно массово обновить импортированные данные, указав исправления как операции для выполнения в определенных полях документа.

В настоящее время Библиотека, исполняющий исполнитель, поддерживает только учетные записи API Azure Cosmos DB SQL и API Gremlin. В этой статье описывается, как использовать библиотеку NET выполнителя .NET с учетными записями API SQL. Дополнительные сведения об использовании библиотеки NET выполнителя .NET с учетными записями API Gremlin см. в разделе [Выполнение массовых операций в api Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Предварительные требования

* Если вы еще не установили Visual Studio 2019, вы можете скачать и использовать [Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Убедитесь, что во время установки Visual Studio включен параметр "Разработка Azure".

* Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.

* [Бесплатную пробную версию Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) можно использовать без подписки Azure, без оплаты и каких-либо обязательств. Также можно использовать [эмулятор Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) с `https://localhost:8081` конечной точкой. Первичный ключ предоставляется в разделе [Выполнение проверки подлинности запросов](local-emulator.md#authenticating-requests).

* Создайте учетную запись API SQL Azure Cosmos DB, используя шаги, описанные в разделе [Создание учетной записи базы данных](create-sql-api-dotnet.md#create-account) в статье о начале работы с .NET.

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь перейдем к работе с кодом, загрузив пример приложения .NET из GitHub. Это приложение выполняет групповые операции с данными, хранящимися в вашей учетной записи Azure Cosmos. Чтобы клонировать приложение, откройте командную строку, перейдите в каталог, в который нужно скопировать его, и выполните следующую команду:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Клонированный репозиторий содержит два примера: "Булкимпортсампле" и "Булкупдатесампле". Вы можете открыть один из примеров приложений, обновить строки подключения в файле App.config, добавив строки подключения учетной записи Azure Cosmos DB, создать решение и запустить его.

Приложение "Булкимпортсампле" создает случайные документы и выполняет их массовый импорт в учетную запись Azure Cosmos. Приложение BulkUpdateSample массово обновляет импортированные документы, указывая исправления как операции, выполняемые над определенными полями документа. В следующих разделах вы рассмотрите код в каждом из этих примеров приложений.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Массовый импорт данных в учетную запись Azure Cosmos

1. Перейдите в папку BulkImportSample и откройте файл BulkImportSample.sln.  

2. Строки подключения Azure Cosmos DB извлечены из файла App.config, как показано в следующем коде:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Средство "групповое средство импорта" создает новую базу данных и контейнер с именем базы данных, именем контейнера и значениями пропускной способности, указанными в файле App. config.

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

4. Объект BulkExecutor инициализируется с высоким значением повтора для времени ожидания и регулируемыми запросами. Далее для них устанавливается значение 0, чтобы передать управление перегрузкой в объект BulkExecutor на время его существования.  

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

5. Приложение вызывает API BulkImportAsync. Библиотека .NET предоставляет две перегрузки API-интерфейса для импорта, который принимает список сериализованных документов JSON, а другой принимает список десериализованных документов POCO. Дополнительные сведения об определениях каждого из перегруженных методов см. в [документации по API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

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
   |enableUpsert    |   Флаг для включения операций Upsert в документах. Если документ с указанным ИДЕНТИФИКАТОРом уже существует, он обновляется. По умолчанию установлено значение false.      |
   |disableAutomaticIdGeneration    |    Параметр, отключающий автоматическое создание идентификатора. По умолчанию установлено значение true.     |
   |maxConcurrencyPerPartitionKeyRange    | Максимальная степень параллелизма для диапазона ключей разделов. Значение null приведет к использованию значения по умолчанию (20). |
   |maxInMemorySortingBatchSize     |  Максимальное число документов, извлеченных из перечислителя документов, которые передаются в вызов API на каждом этапе. Для фазы сортировки в памяти, которая происходит перед массовым импортом, установка этого параметра в значение null приведет к тому, что библиотека будет использовать минимальное значение по умолчанию (Documents. Count, 1000000).       |
   |cancellationToken    |    Токен отмены для корректного выхода из операции полного импорта.     |

   **Определение объекта ответа массового импорта**. Результат вызова API массового импорта содержит следующие атрибуты:

   |**Параметр**  |**Описание**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  Общее число документов, успешно импортированных из общего числа документов, переданных в вызов API-интерфейса для группового импорта.       |
   |TotalRequestUnitsConsumed (double)   |   Общее число единиц запросов (ЕЗ), потребляемых вызовом API массового импорта.      |
   |TotalTimeTaken (TimeSpan)    |   Общее время, затраченное на вызов API-интерфейса полного импорта для завершения выполнения.      |
   |Бадинпутдокументс (объект\<List >)   |     Список документов с неверным форматом, которые не были успешно импортированы при вызове API массового импорта. Исправьте возвращенные документы и повторите попытку импорта. Документы с неверным форматом включают в себя документы, значение идентификаторов которых не является строкой (NULL или любой другой тип данных считается недействительным).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Групповое обновление данных в учетной записи Azure Cosmos

С помощью API BulkUpdateAsync можно обновить имеющиеся документы. В этом примере в `Name` поле будет присвоено новое значение и `Description` удалено поле из существующих документов. Полный набор поддерживаемых операций обновления см. в [документации по API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

1. Перейдите в папку BulkUpdateSample и откройте файл BulkUpdateSample.sln.  

2. Определите элементы обновления вместе с соответствующими операциями обновления полей. В этом примере будет `SetUpdateOperation` использоваться для `Name` обновления `Description` поля и `UnsetUpdateOperation` удаления поля из всех документов. Вы также можете выполнять другие операции, такие как увеличение значения в поле документа по определенному значению, передача определенных значений в поле массива или удаление определенного значения из него. Дополнительные сведения о различных методах, предоставляемых API массового обновления, см. в [документации по API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. Приложение вызывает API BulkUpdateAsync. Дополнительные сведения об определении метода Булкупдатеасинк см. в [документации по API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

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
   |maxConcurrencyPerPartitionKeyRange    |   Максимальная степень параллелизма на диапазон ключей секций. при установке этого параметра в качестве значения NULL библиотека будет использовать значение по умолчанию (20).   |
   |maxInMemorySortingBatchSize    |    Максимальное число элементов обновления, извлеченных из перечислителя элементов обновления, переданного в вызов API на каждом этапе. Для фазы сортировки в памяти, которая происходит перед выполнением обновления, установка этого параметра в значение null приведет к тому, что библиотека будет использовать минимальное значение по умолчанию (Упдатеитемс. Count, 1000000).     |
   | cancellationToken|Токен отмены для корректного выхода из операции полного обновления. |

   **Определение объекта ответа массового обновления**. Результат вызова API массового обновления содержит следующие атрибуты:

   |**Параметр**  |**Описание** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   Число документов, которые были успешно обновлены из общего числа документов, переданных в вызове API-интерфейса для обновления.      |
   |TotalRequestUnitsConsumed (double)   |    Общее количество единиц запросов (RUs), потребляемых вызовом API для выполнения операций обновления.    |
   |TotalTimeTaken (TimeSpan)   | Общее время, затраченное на вызов API-интерфейса для полного обновления для завершения выполнения. |
    
## <a name="performance-tips"></a>Советы по улучшению производительности 

При использовании библиотеки неполного выполнителя необходимо учитывать следующие моменты.

* Для достижения оптимальной производительности запустите приложение из виртуальной машины Azure, расположенной в том же регионе, что и ваш регион записи учетной записи Azure Cosmos.  

* Рекомендуется создать экземпляр одного `BulkExecutor` объекта для всего приложения на одной виртуальной машине, которая соответствует определенному контейнеру Azure Cosmos.  

* Поскольку одно выполнение API групповой операции потребляет большой объем ресурсов процессора и сети клиентского компьютера (это происходит путем создания нескольких задач внутренне). Не рекомендуется порождать несколько параллельных задач в рамках процесса приложения, выполняющих вызовы API для выполнения операций с массовыми операциями. Если один вызов API групповой операции, выполняющийся на одной виртуальной машине, не может использовать пропускную способность контейнера (если пропускная способность контейнера > 1 000 000 единиц запросов в секунду), рекомендуется создать отдельные виртуальные машины для параллельного выполнения. вызовы API групповой операции.  

* Убедитесь, `InitializeAsync()` что метод вызывается после создания экземпляра объекта BulkExecutor для выборки сопоставлений секций целевого Cosmos контейнера.  

* Убедитесь, что в файле App.Config приложения включен параметр **gcServer** для обеспечения лучшей производительности.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Библиотека создает трассировки, которые могут записываться в файл журнала, либо в консоль. Чтобы включить оба варианта, добавьте следующий код в файл App. config приложения.

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

## <a name="next-steps"></a>Следующие шаги

* Сведения о пакете NuGet и заметках о выпуске [см. в](sql-api-sdk-bulk-executor-dot-net.md)этой статье.
