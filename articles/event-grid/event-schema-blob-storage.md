---
title: Схема событий службы "Сетка событий Azure" для хранилища больших двоичных объектов
description: Описание свойств событий хранилища больших двоичных объектов в службе "Сетка событий Azure"
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: bed6c3f1efcb2d0ef34e827ddb2b521f8c038940
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445764"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Схема событий службы "Сетка событий Azure" для хранилища BLOB-объектов

В этой статье описаны свойства и схема событий хранилища BLOB-объектов. Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).

Список примеров сценариев и руководств см. в статье [Источники событий в службе "Сетка событий Azure"](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>Список событий для REST API больших двоичных объектов

Эти события вызываются в том случае, если клиент создает, заменяет или удаляет большой двоичный объект путем вызова REST API больших двоичных объектов.

 |Имя события |Описание|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Запускается при создании или замене большого двоичного объекта. <br>В частности, это событие вызывается, когда клиенты используют `PutBlob`, `PutBlockList`, или `CopyBlob` операций, доступных в REST API службы BLOB-объектов.   |
 |**Microsoft.Storage.BlobDeleted** |Запускается при удалении большого двоичного объекта. <br>В частности, это событие активируется, когда клиенты вызывают `DeleteBlob` операцию, которая доступна в REST API службы BLOB-объектов. |

> [!NOTE]
> Если вы хотите убедиться, что **Microsoft.Storage.BlobCreated** событие запускается, только в том случае, когда большой двоичный объект блока прилагает все усилия, фильтрация событий для `CopyBlob`, `PutBlob`, и `PutBlockList` вызовов REST API. Триггер вызовы этих API **Microsoft.Storage.BlobCreated** событий только в том случае, после данных, полностью зафиксирована блочный большой двоичный объект. Узнайте, как создать фильтр, см. в разделе [фильтрация событий для события сетки](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Список событий для Data Lake Gen 2 REST API службы хранилища Azure

Эти события вызываются в том случае, если включить иерархическое пространство имен в учетной записи хранения, а клиенты вызывают API REST Gen2 хранилища Озера данных Azure.

> [!NOTE]
> Эти события являются в общедоступной предварительной версии, и они становятся доступны только **Западная часть США 2** и **Центрально-Западная** регионах.

 |Имя события|Описание|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** | Запускается при создании или замене большого двоичного объекта. <br>В частности, это событие вызывается, когда клиенты используют `CreateFile` и `FlushWithClose` операций, доступных в Azure Data Lake Gen2 REST API службы хранилища. |
 |**Microsoft.Storage.BlobDeleted** |Запускается при удалении большого двоичного объекта. <br>В частности, это событие также активируется, когда клиенты вызывают `DeleteFile` операция, которая доступна в Azure Data Lake Gen2 REST API службы хранилища. |
 |**Microsoft.Storage.BlobRenamed**|Запускается при переименовании большого двоичного объекта. <br>В частности, это событие вызывается, когда клиенты используют `RenameFile` операции, которая доступна в Azure Data Lake Gen2 REST API службы хранилища.|
 |**Microsoft.Storage.DirectoryCreated**|Активируется при создании каталога. <br>В частности, это событие вызывается, когда клиенты используют `CreateDirectory` операции, которая доступна в Azure Data Lake Gen2 REST API службы хранилища.|
 |**Microsoft.Storage.DirectoryRenamed**|Запускается при переименовании каталога. <br>В частности, это событие вызывается, когда клиенты используют `RenameDirectory` операции, которая доступна в Azure Data Lake Gen2 REST API службы хранилища.|
 |**Microsoft.Storage.DirectoryDeleted**|Запускается при удалении каталога. <br>В частности, это событие вызывается, когда клиенты используют `DeleteDirectory` операции, которая доступна в Azure Data Lake Gen2 REST API службы хранилища.|

> [!NOTE]
> Если вы хотите убедиться, что **Microsoft.Storage.BlobCreated** событие запускается, только в том случае, когда большой двоичный объект блока прилагает все усилия, фильтрация событий для `FlushWithClose` вызова REST API. Этот API вызов триггеров **Microsoft.Storage.BlobCreated** событий только в том случае, после данных, полностью зафиксирована блочный большой двоичный объект. Узнайте, как создать фильтр, см. в разделе [фильтрация событий для события сетки](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Содержимое ответа на событие

При появлении события службы "Сетка событий" отправляет данные об этом событии подписки конечной точки.

В этом разделе содержится пример, как будет выглядеть эти данные для каждого события хранилища BLOB-объектов.

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobCreated событий

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
    "eTag": "0x8D4BCC2E4835CD0",
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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobCreated событий (Gen2 хранилища Озера данных)

Если учетная запись хранения BLOB-объектов содержит иерархическое пространство имен, данных будет выглядеть в предыдущем примере, за исключением следующих изменений:

* `dataVersion` Ключа будет присвоено значение `2`.

* `data.api` Ключ присваивается строка `CreateFile` или `FlushWithClose`.

* `contentOffset` Ключ включается в наборе данных.

> [!NOTE]
> Если приложения используют `PutBlockList` операцию для передачи нового большого двоичного объекта в учетной записи, а данные не будут содержать эти изменения.

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
    "eTag": "0x8D4BCC2E4835CD0",
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

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted событий

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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted событий (Gen2 хранилища Озера данных)

Если учетная запись хранения BLOB-объектов содержит иерархическое пространство имен, данных будет выглядеть в предыдущем примере, за исключением следующих изменений:

* `dataVersion` Ключа будет присвоено значение `2`.

* `data.api` Ключ присваивается строка `DeleteFile`.

* `url` Ключ содержит путь `dfs.core.windows.net`.

> [!NOTE]
> Если приложения используют `DeleteBlob` операция удаления большого двоичного объекта из учетной записи, данные не будут содержать эти изменения.

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

### <a name="microsoftstorageblobrenamed-event"></a>Событие Microsoft.Storage.BlobRenamed

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

### <a name="microsoftstoragedirectorycreated-event"></a>Событие Microsoft.Storage.DirectoryCreated

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>Событие Microsoft.Storage.DirectoryRenamed

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

### <a name="microsoftstoragedirectorydeleted-event"></a>Событие Microsoft.Storage.DirectoryDeleted

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

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| topic | строка | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | строка | Определенный издателем путь к субъекту событий. |
| eventType | string | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | string | Время создания события с учетом времени поставщика в формате UTC. |
| id | string | Уникальный идентификатор события. |
| data | object | Данные события хранилища BLOB-объектов. |
| dataVersion | string | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | string | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | type | Описание |
| -------- | ---- | ----------- |
| api | строка | Операция, вызвавшая событие. |
| clientRequestId | string | Идентификатор запроса, предоставляемый клиентом для операции API хранилища. Этот идентификатор может использоваться для корреляции журналов диагностики службы хранилища Azure с помощью поля «client-request-id» в журналах, а также можно указать в запросах клиента с помощью заголовка «x-ms-client-request-id». Ознакомьтесь со статьей [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Формат журналов Аналитики Службы хранилища). |
| requestId | string | Создаваемый службой идентификатор запроса для операции API хранилища. Может использоваться для корреляции журналов диагностики службы хранилища Azure с помощью поля request-id-header в журналах. Возвращается при инициации вызова API в заголовке x-ms-request-id. Ознакомьтесь со статьей [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Формат журналов Аналитики Службы хранилища). |
| eTag | string | Значение, которое позволяет выполнять операции условно. |
| сontentType | string | Тип содержимого, указанный для BLOB-объекта. |
| contentLength | integer | Размер большого двоичного объекта в байтах. |
| blobType | string | Тип большого двоичного объекта. Допустимые значения: BlockBlob или PageBlob. |
| contentOffset | номер | Смещение в байтах, операции записи, сделанной в момент, где приложение запуском события завершения записи в файл. <br>Отображается только для событий, запускаемых в учетных записях хранения BLOB-объектов, имеющих иерархического пространства имен.|
| destinationUrl |string | URL-адрес файла, который будет существовать после завершения операции. Например, при переименовании файла `destinationUrl` свойство содержит URL-адрес нового имени файла. <br>Отображается только для событий, запускаемых в учетных записях хранения BLOB-объектов, имеющих иерархического пространства имен.|
| sourceUrl |string | URL-адрес файла, который существует перед операцией. Например, при переименовании файла `sourceUrl` содержит URL-адрес исходного имени файла до операции переименования. <br>Отображается только для событий, запускаемых в учетных записях хранения BLOB-объектов, имеющих иерархического пространства имен. |
| url | строка | Путь к BLOB-объекту. <br>Если клиент использует REST API больших двоичных объектов, то URL-адрес имеет такую структуру:  *\<storage-account-name\>.blob.core.windows.net/\<имя контейнера\>/\<имя файла \>* . <br>Если клиент использует REST API хранилища Озера данных, то URL-адрес имеет такую структуру:  *\<storage-account-name\>.dfs.core.windows.net/\<имя файловой системы\> / \<файла\>* .
|
| recursive| string| `True` для выполнения операции на всех вложенных каталогах; в противном случае `False`. <br>Отображается только для событий, запускаемых в учетных записях хранения BLOB-объектов, имеющих иерархического пространства имен. |
| sequencer | string | Значение непрозрачной строки, представляющее логическую последовательность событий для любого отдельного имени большого двоичного объекта.  Пользователи могут использовать стандартное сравнение строк для понимания относительной последовательности двух событий в одном имени большого двоичного объекта. |
| storageDiagnostics | object | Диагностические данные, которые иногда включаются службой хранилища Azure. Если они присутствуют, то должны игнорироваться потребителями события. |

|Свойство|type|Описание|
 |-------------------|------------------------|-----------------------------------------------------------------------|

## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
* Основные сведения о работе с событиями хранилища BLOB-объектов см. в статье [Перенаправление событий хранилища BLOB-объектов в пользовательскую конечную веб-точку (предварительная версия)](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
