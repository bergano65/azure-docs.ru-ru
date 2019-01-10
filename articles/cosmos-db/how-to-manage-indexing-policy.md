---
title: Сведения об управлении учетными записями базы данных в Azure Cosmos DB
description: Сведения об управлении учетными записями базы данных в Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: c27cee4842c0e65e1737f100a215cff82a0fd439
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033100"
---
# <a name="manage-indexing-in-azure-cosmos-db"></a>Управление индексированием в Azure Cosmos DB

В Azure Cosmos DB можно настроить, нужно ли использовать автоматическое индексирование всех элементов контейнера. По умолчанию все элементы контейнера Azure Cosmos автоматически индексируются, но вы можете отключить эту функцию. При выключенном индексировании элементы могут быть доступны через собственные ссылки или запросы, использующие идентификатор элемента, например идентификатор документа. Вы можете явно запросить обработку результатов без использования индекса, передав заголовок **x-ms-documentdb-enable-scan**  в REST API или параметр запроса **EnableScanInQuery** с помощью пакета SDK для .NET.

При выключенном автоматическом индексировании вы все равно можете выборочно добавлять в индекс некоторые элементы. И наоборот, вы можете оставить включенным автоматическое индексирование и выборочно указать, какие элементы необходимо исключить. Настройка включения или выключения индексирования полезна, когда у вас есть подмножество элементов, к которым требуется выполнить запрос.  

Пропускная способность операций записи и единицы запросов должны быть пропорциональны количеству значений, которые должны быть проиндексированы. Это количество определяется включенным набором в политике индексации. Если у вас есть четкое представление о шаблонах запросов, можно явно выбрать подмножество путей для включения или исключения, чтобы повысить пропускную способность операций записи.

## <a name="manage-indexing-using-azure-portal"></a>Управление индексированием с помощью портала Azure

1. Войдите на [портале Azure](https://portal.azure.com/).

2. Создайте новую учетную запись Azure Cosmos или выберите имеющуюся.

3. Откройте область **Data Explorer**.

4. Выберите существующий контейнер, разверните его и измените следующие значения:

   * Откройте окно **Scale & Settings** (Параметры масштабирования).
   * Измените значение параметра **indexingMode** с *consistent* на *none* либо включите определенные пути в индексирование или исключите их из него.
   * Нажмите кнопку **Сохранить** , чтобы сохранить изменения.

   ![Управление индексированием с помощью портала Azure](./media/how-to-manage-indexing/how-to-manage-indexing-portal.png)

## <a name="manage-indexing-using-azure-sdks"></a>Управление индексированием с помощью пакетов SDK для Azure

### <a id="dotnet"></a>Пакет SDK для .NET

Так, в следующем примере показано, как добавить элемент явным образом с помощью [пакета SDK для .NET для API SQL](sql-api-sdk-dotnet.md) и свойство [RequestOptions.IndexingDirective](/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective).

```csharp
// To override the default behavior to exclude (or include) a document in indexing,
// use the RequestOptions.IndexingDirective property.
client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new { id = "myDocumentId", isRegistered = true },
    new RequestOptions { IndexingDirective = IndexingDirective.Include });
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об индексировании см. по следующим ссылкам:

* [Обзор индексирования](index-overview.md)
* [Политика индексации](index-policy.md)
* [Типы индексов](index-types.md)
* [Пути индекса](index-paths.md)
