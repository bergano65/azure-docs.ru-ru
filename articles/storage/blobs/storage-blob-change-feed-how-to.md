---
title: Подача изменения процесса в хранилище Azure Blob (Предварительный просмотр) Документы Майкрософт
description: Узнайте, как обрабатывать журналы изменений в клиентском приложении .NET
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74111857"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Подача изменения процесса в хранилище Azure Blob (Предварительный просмотр)

Лента изменений предоставляет журналы транзакций всех изменений, которые происходят с каплями и метаданными капли в вашей учетной записи хранения. В этой статье показано, как читать записи изврапроментов изменений с помощью библиотеки процессоров для переставок из blob.

Подробнее об ленте изменений читайте [в ленте Change Feed в azure Blob Storage (Preview)](storage-blob-change-feed.md).

> [!NOTE]
> Канал изменений находится в открытом предварительном просмотре и доступен в **регионах westcentralus** и **westus2.** Чтобы узнать больше об этой функции вместе [Change feed support in Azure Blob Storage](storage-blob-change-feed.md)с известными проблемами и ограничениями, см. Библиотека процессоров изменяемых каналов может изменяться в период до момента, когда эта библиотека становится общедоступной.

## <a name="get-the-blob-change-feed-processor-library"></a>Получите библиотеку процессора компоновки подачи по каблу

1. В Visual Studio добавьте URL-адрес `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` в свои источники пакетов NuGet. 

   Чтобы узнать, как, [см.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)

2. В наборе NuGet Manager найдите пакет **Microsoft.Azure.Storage.Changefeed** и установите его в свой проект. 

   Чтобы узнать, как, см [Найти и установить пакет](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

## <a name="connect-to-the-storage-account"></a>Подключение к учетной записи хранения

Пропаривать строку соединения, позвонив по методу [CloudStorageAccount.TryParse.](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) 

Затем создайте объект, представляющий Blob Storage в вашей учетной записи хранения, позвонив по методу [CloudStorageAccount.CreateCloudBlobClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)

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

## <a name="initialize-the-change-feed"></a>Инициализация канала изменения

Добавьте следующие операторы с помощью в верхней части файла кода. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Затем создайте экземпляр класса **ChangeFeed,** позвонив в метод **GetContainerReference.** Передайте во имя контейнера подачи изменений.

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
> Лента изменений — это неизменяемая сущность, основанная только на чтении, в вашей учетной записи хранения данных. Любое количество приложений может читать и обрабатывать канал изменения одновременно и самостоятельно по своему усмотрению. Записи не удаляются из ленты изменений, когда приложение читает их. Состояние чтения или итерации каждого потребляющего читателя является независимым и поддерживается только вашим приложением.

Самый простой способ чтения записей — создать экземпляр класса **ChangeFeedReader.** 

Этот пример пронизан всеми записями в ленте изменений, а затем печатает на консоли несколько значений из каждой записи. 
 
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

## <a name="resuming-reading-records-from-a-saved-position"></a>Возобновление записей чтения из сохраненной позиции

Вы можете сохранить свое чтение позиции в ленте изменений и возобновить итерирование записей в будущем. Вы можете сохранить состояние итерации канала изменения в любое время с помощью метода **ChangeFeedReader.SerializeState()** . Состояние — это **строка,** и приложение может сохранить это состояние на основе дизайна приложения (например, в базу данных или файл).

```csharp
    string currentReadState = processor.SerializeState();
```

Вы можете продолжить итерацию записей из последнего состояния, создав **ChangeFeedReader** с помощью метода **CreateChangeFeedReaderFromPointerAsync.**

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

## <a name="stream-processing-of-records"></a>Обработка потоков записей

Вы можете выбрать обработку записей изменения корма по мере их поступления. Посмотреть [спецификации](storage-blob-change-feed.md#specifications).

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

## <a name="reading-records-within-a-time-range"></a>Чтение записей в пределах временного диапазона

Канал изменения организован в почасовые сегменты на основе времени события изменения. Посмотреть [спецификации](storage-blob-change-feed.md#specifications). Вы можете читать записи из сегментов каналов изменений, которые подпадают под определенный временной диапазон.

Этот пример получает время начала всех сегментов. Затем он итерирует этот список до начала времени либо за пределами времени последнего расходного сегмента или за пределами окончания желаемого диапазона. 

### <a name="selecting-segments-for-a-time-range"></a>Выбор сегментов для временного диапазона

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

Вы можете читать записи из отдельных сегментов или диапазонов сегментов.

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

Вы можете прочитать записи канала изменения от стартового сегмента до конца. Подобно записи чтения в пределах временного диапазона, вы можете перечислить сегменты и выбрать сегмент, чтобы начать итерации с.

Этот пример получает [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) первого сегмента для обработки.

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

Этот пример процессов изменяет записи каналов, начиная с [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) стартового сегмента.

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
> Сегмент может иметь журналы подачизменения изменений в одном или нескольких *chunkFilePath.* В случае нескольких *chunkFilePath* система внутренне разделена записей на несколько осколков для управления пропускной стоимостью публикации. Гарантируется, что каждый раздел сегмента будет содержать изменения для взаимоисключающих капли и может быть обработан самостоятельно, не нарушая порядок. Вы можете использовать класс **ChangeFeedSegmentShardReader** для итератировать записи на уровне осколка, если это наиболее эффективно для вашего сценария.

## <a name="next-steps"></a>Дальнейшие действия

Подробнее об изменениях журналов каналов. Смотрите [ленту изменений в хранилище Azure Blob (Предварительный просмотр)](storage-blob-change-feed.md)
