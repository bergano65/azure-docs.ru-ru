---
title: Как отслеживать состояние индексатора и результаты - поиска Azure
description: Отслеживайте состояние, ход выполнения и результаты индексаторы поиска Azure на портале Azure, с помощью REST API или пакет SDK для .NET.
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 07b4fe2ef830c3ce09b655cf4b433d14923229a9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486289"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>Как отслеживать состояние индексатора поиска Azure и результаты

Служба поиска Azure предоставляет состояние и сведения об текущие и прошлые запусках каждого индексатора.

Отслеживание индексатор может быть полезно, если вы хотите:

* Отслеживать ход выполнения индексатора во время текущего выполнения.
* Просмотрите результаты из текущей или предыдущей которого запуск индексатора.
* Определение индексатора верхнего уровня ошибки и ошибки или предупреждения об отдельных документов, которые будут индексироваться.

## <a name="find-indexer-status-and-history-details"></a>Найти сведения о состоянии и журналы индексатора

Доступны данные мониторинга индексатора различными способами, в том числе:

* На [портале Azure](#portal):
* С помощью [REST API](#restapi)
* С помощью [пакета SDK для .NET](#dotnetsdk)

Доступные индексатора мониторинга сведения предоставляются все следующие ресурсы (хотя данные, которые различаются форматы на основе доступа метода используется):

* Сведения о состоянии о и сам индексатор
* Сведения о самого последнего выполнения индексатора, включая состояние, начала и окончания и подробные сведения об ошибках и предупреждения.
* Список выполнений индексатора исторических и их состояния, результаты, ошибки и предупреждения.

Индексаторы, которые обрабатывают большие объемы данных может занять много времени для запуска. Например индексаторы, которые обрабатывают миллионы исходных документов можно запустить на 24 часа и перезапустите почти сразу же. Состояние для индексаторов больших всегда может сказать **In Progress** на портале. Даже в том случае, если индексатор уже работает, доступны сведения о текущем ходе работ и предыдущих выполнений.

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>Монитор индексаторов на портале

Отобразится текущее состояние всех индексаторы в **индексаторы** списка на странице Общие сведения о службе поиска.

   ![Список индексаторов](media/search-monitor-indexers/indexers-list.png "список индексаторов")

Когда индексатор, который выполняет, состояние содержится список **In Progress**и **документы успешно** значение показывает количество документов, обработанных в данный момент. Может занять несколько минут на портал, чтобы обновить состояние индексатора и число документов.

Индексатор, самое последнее выполнение было успешно показано **успех**. Запуск индексатора могут быть успешно даже если отдельные документы с ошибками, если количество ошибок меньше, чем индексатора **максимальное Кол-во элементов** параметр.

Если предыдущего запуска завершается с ошибкой, то отображается состояние **Failed**. Состояние **Сброс** означает, что состояние отслеживания изменений, индексатор был сброшен.

Щелкните для индексатора, в списке, чтобы узнать больше о индексатора текущих и недавних выполняется.

   ![Журнал сводки и выполнения индексатора](media/search-monitor-indexers/indexer-summary.png "индексатора Сводка и журнал выполнения")

**Сводка индексатора** диаграммы отображается график количество документов, обработанных в его последних запусков.

**Сведения о выполнении** список 50 последних результатов выполнения.

Щелкните результат выполнения в списке, чтобы просмотреть подробные сведения о выполнения. Это включает в себя его запуска и окончания и ошибки и предупреждения, возникшие.

   ![Сведения о выполнении индексатора](media/search-monitor-indexers/indexer-execution.png "сведения о выполнении индексатора")

Если возникли проблемы конкретного документа во время выполнения, они отображаются в полях ошибки и предупреждения.

   ![Сведения о индексатора с ошибками](media/search-monitor-indexers/indexer-execution-error.png "сведения индексатора с ошибками")

Предупреждения являются общими с некоторых типов, индексаторов и не всегда указывает на проблему. Например индексаторы, использование служб cognitive services могут сообщать об ошибках, когда изображения или PDF-файлы не содержат текст, который будет обрабатывать.

Дополнительные сведения об исследовании индексатора ошибок и предупреждений см. в разделе [часто возникающих проблем индексатора в поиске Azure](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>Мониторинг с помощью REST API индексаторов

Можно получить состояние и журнал выполнений индексатора с помощью [команду получения состояния индексатора](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Ответ содержит сведения об общем состоянии индексатора, последнем (или текущем) вызове индексатора, а также журнал последних вызовов индексатора.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Журнал выполнения включает не более 50 последних запусков, которые сортируются в обратном хронологическом порядке (самых новых).

Обратите внимание, что два разных состояния значения. Находится в состоянии верхнего уровня для и сам индексатор. Состояние индексатора **под управлением** означает, что индексатор настроено правильно и доступен для запуска, но не на что он в настоящее время выполнения.

Каждый запуск индексатора также имеет собственное состояние, указывающее, является ли текущее этого конкретного запуска (**под управлением**), или уже завершен, **успех**, **transientFailure**, или **persistentFailure** состояния. 

При сбросе индексатор для обновления его состояние отслеживания изменений в отдельного выполнения журнал добавляется запись с **Сброс** состояния.

Дополнительные сведения о кодах состояния и индексатор, данные мониторинга, см. в разделе [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>Монитор индексаторов, с помощью пакета SDK для .NET

Можно определить расписание для индексатора, с помощью пакета SDK для .NET для поиска Azure. Чтобы сделать это, включите **расписание** свойства при создании или обновлении индексатора.

Следующие C# пример записывает сведения о состоянии индексатора и результаты его самую последнюю (или текущие) запустите на консоль.

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

Выходные данные в консоль будет выглядеть следующим образом:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Обратите внимание, что два разных состояния значения. Состояние и сам индексатор находится в состоянии верхнего уровня. Состояние индексатора **под управлением** означает, что индексатор настроено правильно и доступны для выполнения, но не на что он в данный момент.

Каждый запуск индексатора также имеет собственное состояние для этого конкретного запуска выполняется (**под управлением**), или уже была завершена с **успех** или **TransientError** состояние. 

При сбросе индексатор для обновления его состояние отслеживания изменений в отдельный журнал добавляется запись с **Сброс** состояния.

Дополнительные сведения о кодах состояния и индексатор мониторинга сведения см. в разделе [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) в REST API.

Сведения о конкретного документа ошибки или предупреждения, можно получить посредством перечисления в списках `IndexerExecutionResult.Errors` и `IndexerExecutionResult.Warnings`.

Дополнительные сведения о классах пакета SDK для .NET, используемых для отслеживания индексаторов см. в разделе [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) и [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
