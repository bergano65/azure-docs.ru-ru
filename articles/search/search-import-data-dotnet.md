---
title: Загрузка данных в индекс Поиска Azure в C# (пакет SDK для .NET) — Поиск Azure
description: Узнайте, как отправлять данные в индекс, поддерживающий полнотекстовый поиск, в службе Поиск Azure с помощью примера кода на C# и пакета SDK для .NET.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: d2d54d1425bbb67a3f5ba1b6081a9f74ff87f4d6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286913"
---
# <a name="quickstart-2---load-data-to-an-azure-search-index-using-c"></a>Краткое руководство. Часть 2. Загрузка данных в индекс Поиска Azure с помощью C#

В этой статье показано, как импортировать данные в [индекс Поиска Azure](search-what-is-an-index.md) с помощью C# и [пакета SDK для .NET](https://aka.ms/search-sdk). Отправка документов в индекс выполняется с помощью следующих задач:

> [!div class="checklist"]
> * Создайте объект [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet), который будет подключен к индексу поиска.
> * Создайте объект [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet), содержащий документы, которые должны быть добавлены, изменены или удалены.
> * Вызовите метод `Documents.Index` в `SearchIndexClient`, чтобы отправить документы в индекс.

## <a name="prerequisites"></a>Предварительные требования

[Создайте индекс Поиска Azure](search-create-index-dotnet.md) и объект `SearchServiceClient`, как показано в разделе [Создание экземпляра класса SearchServiceClient](search-create-index-dotnet.md#CreateSearchServiceClient).


## <a name="create-a-client"></a>Создание клиента
Чтобы импортировать данные, необходим экземпляр класса `SearchIndexClient`. Существует несколько способов создания этого класса, включая использование уже созданного экземпляра `SearchServiceClient`. 

Как показано в следующем примере, вы можете использовать экземпляр `SearchServiceClient` и вызвать его метод `Indexes.GetClient`. Этот фрагмент получает `SearchIndexClient` для индекса с именем hotels из `SearchServiceClient` с именем `serviceClient`.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

У класса `SearchIndexClient` есть свойство `Documents`. Это свойство предоставляет все методы, которые необходимы для добавления, изменения, удаления и запроса документов в индексе.

> [!NOTE]
> В типичном поисковом приложении выполнение запросов и индексирование обрабатываются отдельно. Хотя метод `Indexes.GetClient` удобен, так как вы можете повторно использовать такие объекты, как `SearchCredentials`, более надежный подход включает в себя создание `SearchIndexClient` напрямую, чтобы передать ключ запроса вместо ключа администратора. Такой подход согласуется с [принципом минимальных привилегий](https://en.wikipedia.org/wiki/Principle_of_least_privilege) и помогает сделать приложение более безопасным. Вы создадите `SearchIndexClient` в следующем упражнении. Дополнительные сведения о ключах см. в статье [Create and manage api-keys for an Azure Search service](search-security-api-keys.md) (Создание ключей API и управление ими для службы "Поиск Azure").
> 
> 

<a name="construct-indexbatch"></a>

## <a name="construct-indexbatch"></a>Создание класса IndexBatch

Чтобы импортировать данные с помощью пакета SDK для .NET, упакуйте данные в объект `IndexBatch`. `IndexBatch` инкапсулирует коллекцию объектов `IndexAction`, каждый из которых содержит документ и свойство. С помощью этого свойства Поиск Azure определяет, какое действие необходимо выполнить с соответствующим документом (отправка, объединение, удаление и mergeOrUpload). Дополнительные сведения см. в разделе [Индексирование действий: отправка, объединение, mergeOrUpload, удаление](search-what-is-data-import.md#indexing-actions).

При условии, что вы знаете, какие операции можно выполнять с документами, давайте создадим `IndexBatch`. В приведенном ниже примере показано, как создать пакет с несколькими разными операциями. В этом примере используется пользовательский класс с именем `Hotel`, сопоставленный с документом в индексе hotels.

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

В этом случае в качестве операций поиска мы используем `Upload`, `MergeOrUpload` и `Delete`, что соответствует методам, вызываемым для класса `IndexAction`.

Предположим, что пример индекса с именем hotels уже заполнен несколькими документами. Обратите внимание: нам не понадобилось указывать все поля документа при использовании операции `MergeOrUpload`, а при использовании `Delete` мы указали только ключ документа (`HotelId`).

Также помните о том, что в один запрос на индексирование можно включить не более 1000 документов.

> [!NOTE]
> В нашем примере к разным документами применяются разные действия. Если вы хотите выполнить одинаковые действия со всеми документами в пакете, вместо вызова метода `IndexBatch.New` следует использовать другие статические методы класса `IndexBatch`. Например, можно создать пакеты, вызвав метод `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` или `IndexBatch.Delete`. Эти методы вместо объектов `IndexAction` принимают коллекцию документов (в нашем примере это объекты типа `Hotel`).
> 
> 

## <a name="call-documentsindex"></a>Вызов Documents.Index
Теперь, когда у нас есть инициализированный объект `IndexBatch`, мы можем отправить его в индекс, вызвав метод `Documents.Index` для объекта `SearchIndexClient`. В следующем примере показано, как вызвать `Index`, а также выполнить несколько необходимых дополнительных действий.

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Обратите внимание на фрагмент `try`/`catch`, окружающий вызов метода `Index`. Блок catch обрабатывает важные ошибки, которые могут возникнуть во время индексирования. Если службе поиска Azure не удается индексировать некоторые документы в пакете, `Documents.Index` вызывает `IndexBatchException`. Это может произойти, если вы индексируете документы службы при интенсивной нагрузке. **Настоятельно рекомендуется явно обрабатывать этот случай в коде.**  Вы можете задержать и повторить попытку индексирования соответствующих документов либо занести ошибку в журнал и продолжить работу, как в нашем примере, а также выполнить другие действия в зависимости от требований вашего приложения к целостности данных.

В примере выше в коде указана двухсекундная задержка. Индексирование в службе поиска Azure происходит асинхронно, поэтому образец приложения должен подождать немного, пока документы не станут доступными для поиска. Такие задержки обычно необходимы только в демонстрациях, тестах и примерах приложений.

Дополнительные сведения об обработке документов см. в разделе [Обработка документов пакетом SDK для .NET](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).


## <a name="next-steps"></a>Дополнительная информация
Заполнив индекс Поиска Azure, далее следует отправить запросы на поиск документов. 

> [!div class="nextstepaction"]
> [Отправка запросов в индекс службы поиска Azure с помощью пакета SDK для .NET](search-query-dotnet.md)
