---
title: Схема событий службы "Сетка событий Azure" для хранилища больших двоичных объектов
description: Описание свойств событий хранилища больших двоичных объектов в службе "Сетка событий Azure"
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 71aa937536f35c9af44adb5822ce7a2bb8f3a9eb
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756004"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Схема событий службы "Сетка событий Azure" для хранилища BLOB-объектов

В этой статье описаны свойства и схема событий хранилища BLOB-объектов.Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).

Список примеров сценариев и руководств см. в статье [Источники событий в службе "Сетка событий Azure"](event-sources.md#storage).

>[!NOTE]
> Только учетные записи хранения рода **StorageV2 (общая цель v2)**, **BlockBlobStorage**, и **BlobStorage** поддержки событий интеграции. **Хранилище (версия 1, общего назначения)***не* поддерживает интеграцию со службой "Сетка событий".

## <a name="list-of-events-for-blob-rest-apis"></a>Список мероприятий для APIs Blob REST

Эти события срабатывают, когда клиент создает, заменяет или удаляет blob, вызывая APIs Blob REST.

 |Имя события |Описание|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Срабатывает при создании или замене капли. <br>В частности, это событие срабатывает, когда клиенты используют `PutBlob` `PutBlockList`, или `CopyBlob` операции, которые доступны в Blob REST API.   |
 |**Microsoft.Storage.BlobDeleted** |Срабатывает при удалении капли. <br>В частности, это событие срабатывает, когда клиенты называют операцию, `DeleteBlob` которая доступна в Blob REST API. |

> [!NOTE]
> Если вы хотите убедиться, что событие **Microsoft.Storage.BlobCreated** срабатывает только тогда, когда `CopyBlob` `PutBlob`Block `PutBlockList` Blob полностью зафиксирован, отфильтруйте событие для вызовов , и REST API. Эти вызовы API вызывают событие **Microsoft.Storage.BlobCreated** только после того, как данные полностью зафиксированы в Block Blob. Чтобы узнать, как создать фильтр, [см.](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Список событий для Azure Data Lake Storage Gen 2 REST AIS

Эти события срабатывают, если вы включите иерархическое пространство имен в учетной записи хранилища, а клиенты вызывают ApIs Azure Data Lake Storage Gen2 REST. Для получения дополнительной информации о бое Azure Data Lake Storage Gen2 [см.](../storage/blobs/data-lake-storage-introduction.md)

|Имя события|Описание|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | Срабатывает при создании или замене капли. <br>В частности, это событие срабатывает, когда клиенты используют `CreateFile` и `FlushWithClose` операции, доступные в Azure Data Lake Storage Gen2 REST API. |
|**Microsoft.Storage.BlobDeleted** |Срабатывает при удалении капли. <br>В частности, это событие также `DeleteFile` запускается, когда клиенты вызывают операцию, которая доступна в Azure Data Lake Storage Gen2 REST API. |
|**Microsoft.Storage.BlobRenamed**|Срабатывает при переименовании капли. <br>В частности, это событие срабатывает, когда клиенты используют операцию, `RenameFile` доступную в Azure Data Lake Storage Gen2 REST API.|
|**Microsoft.Storage.DirectoryСоздано**|Срабатывает при создании каталога. <br>В частности, это событие срабатывает, когда клиенты используют операцию, `CreateDirectory` доступную в Azure Data Lake Storage Gen2 REST API.|
|**Microsoft.Storage.DirectoryRenamed**|Срабатывает при переименовании каталога. <br>В частности, это событие срабатывает, когда клиенты используют операцию, `RenameDirectory` доступную в Azure Data Lake Storage Gen2 REST API.|
|**Microsoft.Storage.DirectoryУдален**|Срабатывает при удалении каталога. <br>В частности, это событие срабатывает, когда клиенты используют операцию, `DeleteDirectory` доступную в Azure Data Lake Storage Gen2 REST API.|

> [!NOTE]
> Если вы хотите убедиться, что событие **Microsoft.Storage.BlobCreated** срабатывает только тогда, когда `FlushWithClose` Block Blob полностью зафиксирован, отфильтруйте событие для вызова REST API. Этот вызов API запускает событие **Microsoft.Storage.BlobCreated** только после того, как данные полностью зафиксированы в Block Blob. Чтобы узнать, как создать фильтр, [см.](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Содержание ответа на события

При срабатывании события служба Event Grid отправляет данные об этом событии в конечную точку.

В этом разделе содержится пример того, как будут выглядеть эти данные для каждого события хранения капли.

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobCreated событие

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobCreated событие (Data Lake Storage Gen2)

Если учетная запись хранилища blob имеет иерархическое пространство имен, данные похожи на предыдущий пример, за исключением этих изменений:

* Ключ `dataVersion` установлен на значение `2`.

* Ключ `data.api` установлен на `CreateFile` строку `FlushWithClose`или .

* Ключ `contentOffset` включен в набор данных.

> [!NOTE]
> Если приложения `PutBlockList` используют операцию для загрузки новой капли в учетную запись, данные не будут содержать эти изменения.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted событие

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted событие (Данные озерного хранения Gen2)

Если учетная запись хранилища blob имеет иерархическое пространство имен, данные похожи на предыдущий пример, за исключением этих изменений:

* Ключ `dataVersion` установлен на значение `2`.

* Ключ `data.api` установлен на строку. `DeleteFile`

* Ключ `url` содержит путь `dfs.core.windows.net`.

> [!NOTE]
> Если приложения `DeleteBlob` используют операцию для удаления капли из учетной записи, данные не будут содержать эти изменения.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobRenamed событие

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.DirectoryСоздание события

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryRenamed event

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.DirectoryDeleted событие

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Свойства события

Событие содержит следующие высокоуровневые данные:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| Раздел | строка | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | строка | Определенный издателем путь к субъекту событий. |
| eventType | строка | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | строка | Время создания события с учетом времени поставщика в формате UTC. |
| идентификатор | строка | Уникальный идентификатор события. |
| . | объект | Данные события хранилища BLOB-объектов. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| api | строка | Операция, вызвавшая событие. |
| clientRequestId | строка | идентификатор запроса, предоставленного клиентом, для работы API хранилища. Этот идентификатор может быть использован для соотношения с диагностическими журналами Azure Storage с помощью поля "клиент-запрос-id" в журналах, и может быть предоставлен в запросах клиентов с помощью заголовка "x-ms-client-request-id". Ознакомьтесь со статьей [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Формат журналов Аналитики Службы хранилища). |
| requestId | строка | Создаваемый службой идентификатор запроса для операции API хранилища. Может использоваться для корреляции журналов диагностики службы хранилища Azure с помощью поля request-id-header в журналах. Возвращается при инициации вызова API в заголовке x-ms-request-id. Ознакомьтесь со статьей [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Формат журналов Аналитики Службы хранилища). |
| eTag | строка | Значение, которое позволяет выполнять операции условно. |
| сontentType | строка | Тип содержимого, указанный для BLOB-объекта. |
| contentLength | Целое число | Размер большого двоичного объекта в байтах. |
| blobType | строка | Тип большого двоичного объекта. Допустимые значения: BlockBlob или PageBlob. |
| contentOffset | number | Смещение байтов операции записи, сделанной в момент, когда приложение, запускающее события, завершило запись в файл. <br>Отображается только для событий, срабатывающих на учетных записях хранения blob, которые имеют иерархическое пространство имен.|
| destinationUrl |строка | URL файла, который будет существовать после завершения операции. Например, если файл переименован, `destinationUrl` свойство содержит URL нового имени файла. <br>Отображается только для событий, срабатывающих на учетных записях хранения blob, которые имеют иерархическое пространство имен.|
| sourceUrl |строка | URL-адрес файла, который существует до операции. Например, если файл переименован, `sourceUrl` url-адрес исходного имени файла до операции переименования. <br>Отображается только для событий, срабатывающих на учетных записях хранения blob, которые имеют иерархическое пространство имен. |
| url | строка | Путь к BLOB-объекту. <br>Если клиент использует Blob REST API, то URL имеет эту структуру: * \<имя\>/\<хранилища.blob.core.windows.net/\>\>\<контейнерное имя файла.* <br>Если клиент использует Data Lake Storage REST API, то URL имеет эту структуру: * \<имя\>/\<хранилища-счета\>\>.dfs.core.windows.net/\<файл-имя-имя-* |
| recursive | строка | `True`для выполнения операции во всех каталогах детей; в `False`противном случае . <br>Отображается только для событий, срабатывающих на учетных записях хранения blob, которые имеют иерархическое пространство имен. |
| sequencer | строка | Значение непрозрачной строки, представляющее логическую последовательность событий для любого отдельного имени большого двоичного объекта.  Пользователи могут использовать стандартное сравнение строк для понимания относительной последовательности двух событий в одном имени большого двоичного объекта. |
| storageDiagnostics | объект | Диагностические данные, которые иногда включаются службой хранилища Azure. Если они присутствуют, то должны игнорироваться потребителями события. |

## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
* Основные сведения о работе с событиями хранилища BLOB-объектов см. в статье [Перенаправление событий хранилища BLOB-объектов в пользовательскую конечную веб-точку (предварительная версия)](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
