---
title: Схема событий службы "Сетка событий Azure" для хранилища больших двоичных объектов
description: Описание свойств событий хранилища больших двоичных объектов в службе "Сетка событий Azure"
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: bd85353aa37cf182a807d99cdc9fb63ead00edeb
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232430"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Схема событий службы "Сетка событий Azure" для хранилища BLOB-объектов

В этой статье описаны свойства и схема событий хранилища BLOB-объектов. Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).

Список примеров сценариев и руководств см. в статье [Источники событий в службе "Сетка событий Azure"](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>Список событий для API-интерфейсов RESTFUL для BLOB-объектов

Эти события активируются, когда клиент создает, заменяет или удаляет большой двоичный объект, вызывая интерфейсы API-интерфейсов RESTFUL для больших двоичных объектов.

 |Имя события |Описание|
 |----------|-----------|
 |**Microsoft. Storage. BlobCreated** |Активируется при создании или замене большого двоичного объекта. <br>В частности, это событие активируется, когда клиенты используют `PutBlob`операции `PutBlockList`, или `CopyBlob` , доступные в REST API больших двоичных объектов.   |
 |**Microsoft. Storage. BlobDeleted** |Активируется при удалении большого двоичного объекта. <br>В частности, это событие активируется, когда клиенты вызывают `DeleteBlob` операцию, доступную в REST API больших двоичных объектов. |

> [!NOTE]
> Если необходимо, чтобы событие **Microsoft. Storage. BlobCreated** вызывалось только при полной фиксации блочного BLOB-объекта, отфильтруйте событие для `CopyBlob`вызовов, `PutBlob`и `PutBlockList` REST API. Эти вызовы API активируют событие **Microsoft. Storage. BlobCreated** только после полной фиксации данных в блочном BLOB-объекте. Сведения о создании фильтра см. в разделе [Фильтрация событий для сетки событий](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Список событий для интерфейсов API для Azure Data Lake Storage поколения 2

Эти события активируются при включении иерархического пространства имен в учетной записи хранения, а клиенты вызывают Azure Data Lake Storage 2-го поколения интерфейсы API.

> [!NOTE]
> Эти события доступны в общедоступной предварительной версии, и доступны только регионы "Западная часть США **2** " и " **Западная Центральная часть США** ".

 |Имя события|Описание|
 |----------|-----------|
 |**Microsoft. Storage. BlobCreated** | Активируется при создании или замене большого двоичного объекта. <br>В частности, это событие активируется, когда клиенты используют `CreateFile` операции `FlushWithClose` и, доступные в Azure Data Lake Storage 2-го поколения REST API. |
 |**Microsoft. Storage. BlobDeleted** |Активируется при удалении большого двоичного объекта. <br>В частности, это событие также активируется, когда клиенты вызывают `DeleteFile` операцию, доступную в REST API Azure Data Lake Storage 2-го поколения. |
 |**Microsoft. Storage. Блобренамед**|Активируется при переименовании большого двоичного объекта. <br>В частности, это событие активируется, когда клиенты используют `RenameFile` операцию, доступную в REST API Azure Data Lake Storage 2-го поколения.|
 |**Microsoft. Storage. Директорикреатед**|Активируется при создании каталога. <br>В частности, это событие активируется, когда клиенты используют `CreateDirectory` операцию, доступную в REST API Azure Data Lake Storage 2-го поколения.|
 |**Microsoft. Storage. Директориренамед**|Активируется при переименовании каталога. <br>В частности, это событие активируется, когда клиенты используют `RenameDirectory` операцию, доступную в REST API Azure Data Lake Storage 2-го поколения.|
 |**Microsoft. Storage. Директориделетед**|Активируется при удалении каталога. <br>В частности, это событие активируется, когда клиенты используют `DeleteDirectory` операцию, доступную в REST API Azure Data Lake Storage 2-го поколения.|

> [!NOTE]
> Если необходимо, чтобы событие **Microsoft. Storage. BlobCreated** вызывалось только при полной фиксации блочного BLOB-объекта, отфильтруйте событие для `FlushWithClose` вызова REST API. Этот вызов API активирует событие **Microsoft. Storage. BlobCreated** только после полной фиксации данных в блочном BLOB-объекте. Сведения о создании фильтра см. в разделе [Фильтрация событий для сетки событий](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Содержимое ответа на событие

При активации события служба службы "Сетка событий" отправляет данные об этом событии в подписку на конечную точку.

В этом разделе содержится пример того, как будут выглядеть данные для каждого события хранилища BLOB-объектов.

### <a name="microsoftstorageblobcreated-event"></a>Событие Microsoft. Storage. BlobCreated

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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Событие Microsoft. Storage. BlobCreated (Data Lake Storage 2-го поколения)

Если учетная запись хранения BLOB-объектов имеет иерархическое пространство имен, то данные выглядят примерно так, как в предыдущем примере, за исключением этих изменений:

* Для ключа задается значение `2`. `dataVersion`

* Для ключа задается строка `CreateFile` или `FlushWithClose`. `data.api`

* `contentOffset` Ключ включен в набор данных.

> [!NOTE]
> Если приложения используют `PutBlockList` операцию для отправки нового большого двоичного объекта в учетную запись, данные не будут содержать эти изменения.

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

### <a name="microsoftstorageblobdeleted-event"></a>Событие Microsoft. Storage. BlobDeleted

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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Событие Microsoft. Storage. BlobDeleted (Data Lake Storage 2-го поколения)

Если учетная запись хранения BLOB-объектов имеет иерархическое пространство имен, то данные выглядят примерно так, как в предыдущем примере, за исключением этих изменений:

* Для ключа задается значение `2`. `dataVersion`

* Для ключа задается строка `DeleteFile`. `data.api`

* Ключ содержит путь `dfs.core.windows.net`. `url`

> [!NOTE]
> Если приложения используют `DeleteBlob` операцию для удаления большого двоичного объекта из учетной записи, данные не будут содержать эти изменения.

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

### <a name="microsoftstorageblobrenamed-event"></a>Событие Microsoft. Storage. Блобренамед

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

### <a name="microsoftstoragedirectorycreated-event"></a>Событие Microsoft. Storage. Директорикреатед

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>Событие Microsoft. Storage. Директориренамед

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

### <a name="microsoftstoragedirectorydeleted-event"></a>Событие Microsoft. Storage. Директориделетед

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

| Свойство | type | Описание |
| -------- | ---- | ----------- |
| topic | string | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | string | Определенный издателем путь к субъекту событий. |
| eventType | string | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | string | Время создания события с учетом времени поставщика в формате UTC. |
| id | string | Уникальный идентификатор события. |
| data | object | Данные события хранилища BLOB-объектов. |
| dataVersion | string | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | string | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | type | Описание |
| -------- | ---- | ----------- |
| api | string | Операция, вызвавшая событие. |
| clientRequestId | string | предоставленный клиентом идентификатор запроса для операции API хранилища. Этот идентификатор можно использовать для сопоставления с журналами диагностики службы хранилища Azure с помощью поля "Client-Request-ID" в журналах и может быть предоставлено в запросах клиента с помощью заголовка "x-MS-Client-Request-ID". Ознакомьтесь со статьей [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Формат журналов Аналитики Службы хранилища). |
| requestId | string | Создаваемый службой идентификатор запроса для операции API хранилища. Может использоваться для корреляции журналов диагностики службы хранилища Azure с помощью поля request-id-header в журналах. Возвращается при инициации вызова API в заголовке x-ms-request-id. Ознакомьтесь со статьей [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Формат журналов Аналитики Службы хранилища). |
| eTag | string | Значение, которое позволяет выполнять операции условно. |
| contentType | string | Тип содержимого, указанный для BLOB-объекта. |
| contentLength | integer | Размер большого двоичного объекта в байтах. |
| blobType | string | Тип большого двоичного объекта. Допустимые значения: BlockBlob или PageBlob. |
| contentOffset | number | Смещение в байтах операции записи, выполненной в момент, когда приложение, запускающее событие, выполнило запись в файл. <br>Отображается только для событий, запускаемых в учетных записях хранения BLOB-объектов с иерархическим пространством имен.|
| дестинатионурл |string | URL-адрес файла, который будет существовать после завершения операции. Например, если файл переименован, `destinationUrl` свойство содержит URL-адрес нового имени файла. <br>Отображается только для событий, запускаемых в учетных записях хранения BLOB-объектов с иерархическим пространством имен.|
| саурцеурл |string | URL-адрес файла, который существует до операции. Например, при переименовании `sourceUrl` файла содержит URL-адрес исходного имени файла перед операцией переименования. <br>Отображается только для событий, запускаемых в учетных записях хранения BLOB-объектов с иерархическим пространством имен. |
| url | string | Путь к BLOB-объекту. <br>Если клиент использует REST API больших двоичных объектов, URL-адрес имеет следующую структуру:  *\<Storage-Account-\>Name.\<BLOB.Core.Windows.net/-name\>/\<имя файла-контейнера\>* . <br>Если клиент использует Data Lake Storage REST API, URL-адрес имеет следующую структуру:  *\<\>Storage-Account-Name. DFS.Core.Windows.NET/\<File-System-Name\>/\<File-Name. \>* . |
| recursive | string | `True`для выполнения операции со всеми дочерними каталогами; в `False`противном случае — значение. <br>Отображается только для событий, запускаемых в учетных записях хранения BLOB-объектов с иерархическим пространством имен. |
| sequencer | string | Значение непрозрачной строки, представляющее логическую последовательность событий для любого отдельного имени большого двоичного объекта.  Пользователи могут использовать стандартное сравнение строк для понимания относительной последовательности двух событий в одном имени большого двоичного объекта. |
| storageDiagnostics | object | Диагностические данные, которые иногда включаются службой хранилища Azure. Если они присутствуют, то должны игнорироваться потребителями события. |
|Свойство|type|Описание|
|-------------------|------------------------|-----------------------------------------------------------------------|

## <a name="next-steps"></a>Следующие шаги

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
* Основные сведения о работе с событиями хранилища BLOB-объектов см. в статье [Перенаправление событий хранилища BLOB-объектов в пользовательскую конечную веб-точку (предварительная версия)](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
