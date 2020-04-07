---
title: Миграция из библиотеки исполнителей навалом в объемную поддержку в Azure Cosmos DB .NET V3 SDK
description: Узнайте, как перенести приложение с использования библиотеки исполнителей на массовую поддержку в Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: maquaran
ms.openlocfilehash: 820a5398d84122659b1676b7d5722bce08b1837d
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755974"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Миграция из библиотеки исполнителей навалом в объемную поддержку в Azure Cosmos DB .NET V3 SDK

В этой статье описаны необходимые шаги для переноса кода существующего приложения, использующее библиотеку исполнителей [.NET навалом](bulk-executor-dot-net.md) в функцию [поддержки навалом](tutorial-sql-api-dotnet-bulk-import.md) в последней версии .NET SDK.

## <a name="enable-bulk-support"></a>Включить объемную поддержку

Включить объемную `CosmosClient` поддержку на экземпляре через конфигурацию [AllowBulkExecution:](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution)

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Создание задач для каждой операции

Массовая поддержка в .NET SDK работает за счет одновременного использования [параллельной библиотеки задач](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) и операций по группировке. 

Не существует единого метода, который будет принимать список документов или операций в качестве параметра ввода, а, скорее, необходимо создать задачу для каждой операции, которую вы хотите выполнить оптом.

Например, если начальный ввод — это список элементов, в котором каждый элемент имеет следующую схему:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Если вы хотите сделать объем импорта (по аналогии с использованием BulkExecutor.BulkImportAsync), вам нужно иметь одновременные звонки `CreateItemAsync` с каждой стоимости элемента. Пример:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Если вы хотите сделать массовое *обновление* (по аналогии с использованием [BulkExecutor.BulkUpdateAsync),](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)вы должны иметь одновременные звонки к методу `ReplaceItemAsync` после обновления значения элемента. Пример:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

И если вы хотите сделать *объемное удаление* (по аналогии с использованием [BulkExecutor.BulkDeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)), вы должны иметь одновременные `DeleteItemAsync`звонки, с `id` и ключ раздела каждого элемента. Пример:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Состояние результата задачи захвата

В предыдущих примерах кода вы создали параллельный список `CaptureOperationResponse` задач и назвали метод по каждой из этих задач. Этот метод является расширением, которое позволяет нам поддерживать *аналогичную схему ответа,* как BulkExecutor, путем захвата любых ошибок и отслеживания [использования единиц запроса.](request-units.md)

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Где `OperationResponse` объявлено:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Выполнение операций одновременно

После определения списка задач подождите, пока все они будут завершены. Вы можете отслеживать выполнение задач, определяя область объема операции, как показано в следующем фрагменте кода:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Статистика захвата

Предыдущий код ждет завершения всех операций и вычисляет требуемую статистику. Эти статистические данные аналогичны статистике, которую можно сделать в [библиотеке BulkImportResponse.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

Содержит: `BulkOperationResponse`

1. Общее время, заработавальное для обработки списка операций с помощью массовой поддержки.
1. Количество успешных операций.
1. Общее количество потребляемых единиц запросов.
1. При сбоях отображается список подставных элементов, содержащих исключение и связанный с ним элемент для целей регистрации и идентификации.

## <a name="retry-configuration"></a>Конфигурация повтора

Массовая библиотека исполнителя имела [указания,](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) в `MaxRetryWaitTimeInSeconds` `MaxRetryAttemptsOnThrottledRequests` которых упоминалось `0` установить и [RetryOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) делегировать управление библиотеке.

Для массовой поддержки в .NET SDK нет скрытого поведения. Вы можете настроить варианты повтора непосредственно через [CosmosClientOptions.MaxRetryAttemptsOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) и [CosmosClientOptions.MaxRetryWaitTimeOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests).

> [!NOTE]
> В тех случаях, когда единицы подготовленных запросов значительно ниже ожидаемых на основе объема данных, можно рассмотреть возможность установления их на высокие значения. Основная операция займет больше времени, но она имеет более высокие шансы на полное успех из-за более высоких повторов.

## <a name="performance-improvements"></a>Повышение производительности

Как и в случае с другими операциями с .NET SDK, использование Stream AIS приводит к повышению производительности и позволяет избежать ненужной сериализации. 

Использование AIS потока возможно только в том случае, если характер данных, которые вы используете, совпадает с потоком байтов (например, потоки файлов). В таких случаях, `CreateItemStreamAsync` `ReplaceItemStreamAsync`используя `DeleteItemStreamAsync` , или методы и работать с `ResponseMessage` (вместо `ItemResponse`) увеличивает пропускную стоимость, которая может быть достигнута.

## <a name="next-steps"></a>Дальнейшие действия

* Подробнее о выпусках .NET SDK можно узнать в статье [Azure Cosmos DB SDK.](sql-api-sdk-dotnet.md)
* Получите полный [исходный код миграции](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) от GitHub.
* [Дополнительные объемные образцы на GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
