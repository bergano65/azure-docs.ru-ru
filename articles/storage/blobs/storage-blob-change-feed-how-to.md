---
title: Обработка канала изменений в хранилище BLOB-объектов Azure (Предварительная версия) | Документация Майкрософт
description: Узнайте, как обрабатывать журналы веб-каналов изменений в клиентском приложении .NET.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 90b7d79cd2a0e215af17856796bcdda2fbabb43f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693635"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Обработка канала изменений в хранилище BLOB-объектов Azure (Предварительная версия)

Веб-канал изменений предоставляет журналы транзакций всех изменений, происходящих в больших двоичных объектах и метаданных больших двоичных объектов в вашей учетной записи хранения. В этой статье показано, как считывать записи веб-канала изменений с помощью библиотеки обработчика канала изменений больших двоичных объектов.

Дополнительные сведения о канале изменений см. [в статье канал изменений в хранилище BLOB-объектов Azure (Предварительная версия)](storage-blob-change-feed.md).

> [!NOTE]
> Веб-канал изменений находится в общедоступной предварительной версии и доступен в регионах **westcentralus** и **westus2** . Дополнительные сведения об этой функции вместе с известными проблемами и ограничениями см. [в статье поддержка веб-канала изменений в хранилище BLOB-объектов Azure](storage-blob-change-feed.md). Библиотека обработчика веб-канала изменений может быть изменена между сейчас и когда эта библиотека становится общедоступной.

## <a name="get-the-blob-change-feed-processor-library"></a>Получение библиотеки обработчика канала изменений больших двоичных объектов

1. В Visual Studio добавьте URL-адрес `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` в источники пакетов NuGet. 

   Дополнительные сведения см. в разделе [источники пакетов](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. В диспетчере пакетов NuGet найдите пакет **Microsoft. Azure. Storage. пр** и установите его в свой проект. 

   Дополнительные сведения см. в разделе [Поиск и установка пакета](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

## <a name="connect-to-the-storage-account"></a>Подключение к учетной записи хранения

Проанализируйте строку подключения, вызвав метод [CloudStorageAccount. TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse) . 

Затем создайте объект, который представляет хранилище BLOB-объектов в вашей учетной записи хранения, вызвав метод [CloudStorageAccount. креатеклаудблобклиент](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet) .

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>Инициализация веб-канала изменений

Добавьте следующие операторы using в начало файла кода. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Затем создайте экземпляр класса **пр** , вызвав метод **жетконтаинерреференце** . Передайте имя контейнера веб-канала изменений.

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>Чтение записей

> [!NOTE]
> Веб-канал изменений является неизменяемой сущностью и доступна только для чтения в вашей учетной записи хранения. Любое количество приложений может одновременно считывать и обрабатывать канал изменений, а также независимо от их удобства. Записи не удаляются из веб-канала изменений, когда приложение считывает их. Состояние чтения или итерации каждого потребляемого модуля чтения является независимым и поддерживается только приложением.

Самый простой способ считать записи — создать экземпляр класса **чанжефидреадер** . 

В этом примере выполняется перебор всех записей в веб-канале изменений, а затем на консоль выводятся несколько значений из каждой записи. 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>Возобновление чтения записей из сохраненной должности

Вы можете сохранить свое расположение чтения в веб-канале изменений и возобновить итерацию записей в будущем. Состояние итерации веб-канала изменений можно сохранить в любое время с помощью метода **чанжефидреадер. сериализестате ()** . Состояние представляет собой **строку** , и приложение может сохранить это состояние в зависимости от структуры приложения (например, в базе данных или файле).

```csharp
    string currentReadState = processor.SerializeState();
```

Можно продолжить итерацию записей из последнего состояния, создав **чанжефидреадер** с помощью метода **креатечанжефидреадерфромпоинтерасинк** .

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>Потоковая обработка записей

Вы можете обрабатывать записи веб-канала изменений по мере их поступления. См. [спецификации](storage-blob-change-feed.md#specifications).

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>Чтение записей в диапазоне времени

Канал изменений организован в почасовые сегменты на основе времени события изменения. См. [спецификации](storage-blob-change-feed.md#specifications). Вы можете читать записи из сегментов веб-канала изменений, которые попадают в указанный диапазон времени.

В этом примере получается время начала всех сегментов. Затем он проходит по этому списку, пока время начала не выходит за пределы времени последнего потребляемого сегмента или за время окончания требуемого диапазона. 

### <a name="selecting-segments-for-a-time-range"></a>Выбор сегментов для диапазона времени

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>Чтение записей в сегменте

Можно считывать записи из отдельных сегментов или диапазонов сегментов.

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>Чтение записей, начиная с времени

Можно считывать записи веб-канала изменений из начального сегмента до конца. Как и при чтении записей в диапазоне времени, можно перечислить сегменты и выбрать сегмент для начала итерации.

В этом примере получается значение [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) первого сегмента для обработки.

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

В этом примере выполняется обработка записей веб-канала изменений, начиная с [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) начального сегмента.

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> Сегмент может иметь журналы веб-канала изменений в одном или нескольких *чункфилепас*. В случае нескольких *чункфилепас* система внутренним образом разделяет записи на несколько сегментов, чтобы управлять пропускной способностью публикации. Гарантируется, что каждая секция сегмента будет содержать изменения взаимоисключающих больших двоичных объектов и может обрабатываться независимо, без нарушения порядка. Класс **чанжефидсегментшардреадер** можно использовать для перебора записей на уровне сегментов, если это наиболее эффективно для вашего сценария.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о журналах веб-канала изменений. См. [веб-канал изменений в хранилище BLOB-объектов Azure (Предварительная версия)](storage-blob-change-feed.md)
