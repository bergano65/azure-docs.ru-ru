---
title: Мониторинг состояния индекса и результатов
titleSuffix: Azure Cognitive Search
description: Мониторинг состояния, прогресса и результатов индексов когнитивного поиска Azure на портале Azure с помощью REST API или .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 699b5a4e5a7f10c883667ca5030dd971855467f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112976"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Как контролировать состояние и результаты индекса когнитивного поиска Azure

Azure Cognitive Search предоставляет информацию о текущих и исторических запусках каждого индекса.

Мониторинг индекса полезен, когда вы хотите:

* Отслеживайте ход работы индекса во время текущего запуска.
* Просмотрите результаты текущего или предыдущего запуска индекса.
* Определите ошибки индекса верхнего уровня, ошибки или предупреждения об индексации отдельных документов.

## <a name="get-status-and-history"></a>Получить статус и историю

Вы можете получить доступ к информации мониторинга индекса различными способами, в том числе:

* На [портале Azure](#portal)
* Использование [aPI REST](#restapi)
* Использование [.NET SDK](#dotnetsdk)

Доступная информация мониторинга индекса включает в себя все следующие (хотя форматы данных различаются в зависимости от используемого метода доступа):

* Информация о состоянии самого индекса
* Информация о последнем запуске индекса, включая его статус, время начала и окончания, а также подробные ошибки и предупреждения.
* Список исторических индексаторов работает, и их статусы, результаты, ошибки и предупреждения.

Индексы, обрабатывающие большие объемы данных, могут заработать много времени. Например, индексы, обрабатывающие миллионы исходных документов, могут работать в течение 24 часов, а затем перезапускаться почти сразу. Статус для высокообъемных индексеров всегда может сказать **"В прогрессе"** на портале. Даже при запуске индекса доступны сведения о текущих ходе и предыдущих запусках.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Монитор с помощью портала

Текущее состояние всех индексеров можно увидеть **Indexers** на странице поиска службы «Обзор».

   ![Список индексаторов](media/search-monitor-indexers/indexers-list.png "Список индексаторов")

При выполнения индекса статус в списке отображается **in Progress,** а значение **Docs Succeeded** показывает количество документов, обработанных до сих пор. Для обновления значения статуса индекса и подсчета документов на портале может уйти несколько минут.

Индексер, чей последний запуск был успешным, показывает **Успех.** Запуск индекса может быть успешным, даже если отдельные документы имеют ошибки, если количество ошибок меньше, чем настройка **неудавшихся элементов индекса Max.**

Если последний запуск закончился ошибкой, статус отображает **failed.** Состояние **сбросить** означает, что состояние отслеживания изменения индексатора было сбылось.

Нажмите на индексатор в списке, чтобы увидеть более подробную информацию о текущих и последних запусках индексатора.

   ![История сводки индекса и выполнения](media/search-monitor-indexers/indexer-summary.png "История сводки индекса и выполнения")

**Сводная** диаграмма Indexer отображает график количества документов, обработанных в последних запусках.

Список **деталей выполнения** отображает до 50 последних результатов выполнения.

Нажмите на результат выполнения в списке, чтобы увидеть специфику этого запуска. Это включает в себя его время начала и окончания, а также любые ошибки и предупреждения, которые произошли.

   ![Детали исполнения индекса](media/search-monitor-indexers/indexer-execution.png "Детали исполнения индекса")

Если во время выполнения были проблемы, связанные с конкретными документами, они будут перечислены в полях Ошибок и Предупреждений.

   ![Детали индекса с ошибками](media/search-monitor-indexers/indexer-execution-error.png "Детали индекса с ошибками")

Предупреждения являются общими для некоторых типов индексеров, и не всегда указывают на проблему. Например, индексы, которые используют когнитивные услуги, могут сообщать о предупреждениях, когда файлы изображения или PDF не содержат текста для обработки.

Для получения дополнительной информации об исследовании [Troubleshooting common indexer issues in Azure Cognitive Search](search-indexer-troubleshooting.md)ошибок и предупреждений индекса см.

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Монитор с помощью REST AIS

Вы можете получить историю состояния и выполнения указателя с помощью [команды Get Indexer Status:](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)

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

История выполнения содержит до 50 последних запусков, которые сортируются в обратном хронологическом порядке (последний первый).

Обратите внимание, что существуют два разных значения статуса. Статус верхнего уровня предназначен для самого индекса. Состояние указателя **выполнения** означает, что индексер настроен правильно и доступен для запуска, но не то, чтобы он в настоящее время работает.

Каждый запуск индексатакжера также имеет свой собственный статус, который указывает, является ли это конкретное выполнение продолжается **(работает),** или уже завершено с **успехом,** **переходный отказ**, или постоянный статус **Отказа.** 

При сбросить индексер для обновления состояния отслеживания изменений добавляется отдельная запись истории выполнения со статусом **сбросить.**

Более подробную информацию о кодах состояния и данных мониторинга индекса можно получить на [getIndexerStatus.](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Мониторинг с помощью .NET SDK

Расписание для индекса можно определить с помощью Azure Cognitive Search .NET SDK. Для этого включите свойство **расписания** при создании или обновлении Indexer.

Следующий пример СЗ пишет информацию о состоянии индекса и результатах его последнего (или текущего) запуска на консоль.

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

Выход в консоли будет выглядеть примерно так:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Обратите внимание, что существуют два разных значения статуса. Статус верхнего уровня — это статус самого индекса. Состояние индекса **Running** означает, что индексер настроен правильно и доступен для выполнения, но не то, что он в настоящее время является выполнением.

Каждый запуск индексара также имеет свой собственный статус для того, является ли это конкретное выполнение продолжается **(Бег),** или уже был завершен с **статусом успеха** или **TransientError.** 

При сбросить индексер для обновления состояния отслеживания изменений добавляется отдельная запись истории со статусом **сбросить.**

Более подробную информацию о кодах состояния и информации о мониторинге индекса можно получить в [API](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) REST.

Подробная информация об ошибках или предупреждениях, конкретных для `IndexerExecutionResult.Errors` `IndexerExecutionResult.Warnings`конкретных документов, может быть получена путем перечисления списков и .

Для получения дополнительной информации о классах .NET SDK, используемых для мониторинга индексов, [см. IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) и [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
