---
title: Схема событий службы "Сетка событий Azure" для хранилища больших двоичных объектов
description: Описание свойств событий хранилища больших двоичных объектов в службе "Сетка событий Azure"
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 0ab81d3c1d4c68827cf1569bf4a22c3311fe355d
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555822"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Схема событий службы "Сетка событий Azure" для хранилища BLOB-объектов

В этой статье описаны свойства и схема событий хранилища BLOB-объектов. Общие сведения о схемах событий см. в статье [схема событий службы "Сетка событий Azure](event-schema.md)".

Список примеров сценариев и руководств см. в статье [Источники событий в службе "Сетка событий Azure"](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>Список событий для API-интерфейсов RESTFUL для BLOB-объектов

Эти события активируются, когда клиент создает, заменяет или удаляет большой двоичный объект, вызывая интерфейсы API-интерфейсов RESTFUL для больших двоичных объектов.

 |Имя события |Описание|
 |----------|-----------|
 |**Microsoft. Storage. BlobCreated** |Активируется при создании или замене большого двоичного объекта. <br>В частности, это событие активируется, когда клиенты используют `PutBlob`, `PutBlockList` или операции `CopyBlob`, доступные в REST API больших двоичных объектов.   |
 |**Microsoft. Storage. BlobDeleted** |Активируется при удалении большого двоичного объекта. <br>В частности, это событие активируется, когда клиенты вызывают операцию `DeleteBlob`, доступную в REST API больших двоичных объектов. |

> [!NOTE]
> Если необходимо, чтобы событие **Microsoft. Storage. BlobCreated** вызывалось только при полной фиксации блочного большого двоичного объекта, отфильтруйте событие для `CopyBlob`, `PutBlob` и `PutBlockList` REST APIных вызовов. Эти вызовы API активируют событие **Microsoft. Storage. BlobCreated** только после полной фиксации данных в блочном BLOB-объекте. Сведения о создании фильтра см. в разделе [Фильтрация событий для сетки событий](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Список событий для интерфейсов API для Azure Data Lake Storage поколения 2

Эти события активируются при включении иерархического пространства имен в учетной записи хранения, а клиенты вызывают Azure Data Lake Storage 2-го поколения интерфейсы API.

|Имя события|Описание|
|----------|-----------|
|**Microsoft. Storage. BlobCreated** | Активируется при создании или замене большого двоичного объекта. <br>В частности, это событие активируется, когда клиенты используют операции `CreateFile` и `FlushWithClose`, доступные в Azure Data Lake Storage 2-го поколения REST API. |
|**Microsoft. Storage. BlobDeleted** |Активируется при удалении большого двоичного объекта. <br>В частности, это событие также срабатывает, когда клиенты вызывают операцию `DeleteFile`, доступную в Azure Data Lake Storage 2-го поколения REST API. |
|**Microsoft. Storage. Блобренамед**|Активируется при переименовании большого двоичного объекта. <br>В частности, это событие активируется, когда клиенты используют операцию `RenameFile`, доступную в Azure Data Lake Storage 2-го поколения REST API.|
|**Microsoft. Storage. Директорикреатед**|Активируется при создании каталога. <br>В частности, это событие активируется, когда клиенты используют операцию `CreateDirectory`, доступную в Azure Data Lake Storage 2-го поколения REST API.|
|**Microsoft. Storage. Директориренамед**|Активируется при переименовании каталога. <br>В частности, это событие активируется, когда клиенты используют операцию `RenameDirectory`, доступную в Azure Data Lake Storage 2-го поколения REST API.|
|**Microsoft. Storage. Директориделетед**|Активируется при удалении каталога. <br>В частности, это событие активируется, когда клиенты используют операцию `DeleteDirectory`, доступную в Azure Data Lake Storage 2-го поколения REST API.|

> [!NOTE]
> Если необходимо, чтобы событие **Microsoft. Storage. BlobCreated** вызывалось только при полной фиксации блочного BLOB-объекта, отфильтруйте событие для `FlushWithClose` REST API вызове. Этот вызов API активирует событие **Microsoft. Storage. BlobCreated** только после полной фиксации данных в блочном BLOB-объекте. Сведения о создании фильтра см. в разделе [Фильтрация событий для сетки событий](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

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

* Для ключа `dataVersion` задано значение `2`.

* Для ключа `data.api` задано строковое `CreateFile` или `FlushWithClose`.

* Ключ `contentOffset` включен в набор данных.

> [!NOTE]
> Если приложения используют операцию `PutBlockList` для передачи нового большого двоичного объекта в учетную запись, данные не будут содержать эти изменения.

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

* Для ключа `dataVersion` задано значение `2`.

* Для ключа `data.api` задано строковое `DeleteFile`.

* Ключ `url` содержит путь `dfs.core.windows.net`.

> [!NOTE]
> Если в приложениях используется операция `DeleteBlob` для удаления большого двоичного объекта из учетной записи, данные не будут содержать эти изменения.

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

Событие содержит следующие основные данные:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| Раздел | string | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | string | Определенный издателем путь к субъекту событий. |
| eventType | string | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | string | Время создания события с учетом времени поставщика в формате UTC. |
| id | string | Уникальный идентификатор события. |
| data | object | Данные события хранилища BLOB-объектов. |
| dataVersion | string | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | string | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| api | string | Операция, вызвавшая событие. |
| clientRequestId | string | предоставленный клиентом идентификатор запроса для операции API хранилища. Этот идентификатор можно использовать для сопоставления с журналами диагностики службы хранилища Azure с помощью поля "Client-Request-ID" в журналах и может быть предоставлено в запросах клиента с помощью заголовка "x-MS-Client-Request-ID". Ознакомьтесь со статьей [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Формат журналов Аналитики Службы хранилища). |
| requestId | string | Создаваемый службой идентификатор запроса для операции API хранилища. Может использоваться для корреляции журналов диагностики службы хранилища Azure с помощью поля request-id-header в журналах. Возвращается при инициации вызова API в заголовке x-ms-request-id. Ознакомьтесь со статьей [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Формат журналов Аналитики Службы хранилища). |
| eTag | string | Значение, которое позволяет выполнять операции условно. |
| сontentType | string | Тип содержимого, указанный для BLOB-объекта. |
| contentLength | целое число | Размер большого двоичного объекта в байтах. |
| blobType | string | Тип большого двоичного объекта. Допустимые значения: BlockBlob или PageBlob. |
| contentOffset | number | Смещение в байтах операции записи, выполненной в момент, когда приложение, запускающее событие, выполнило запись в файл. <br>Отображается только для событий, запускаемых в учетных записях хранения BLOB-объектов с иерархическим пространством имен.|
| дестинатионурл |string | URL-адрес файла, который будет существовать после завершения операции. Например, если файл переименован, свойство `destinationUrl` содержит URL-адрес нового имени файла. <br>Отображается только для событий, запускаемых в учетных записях хранения BLOB-объектов с иерархическим пространством имен.|
| саурцеурл |string | URL-адрес файла, который существует до операции. Например, если файл переименован, `sourceUrl` содержит URL-адрес исходного имени файла перед операцией переименования. <br>Отображается только для событий, запускаемых в учетных записях хранения BLOB-объектов с иерархическим пространством имен. |
| URL-адрес | string | Путь к BLOB-объекту. <br>Если клиент использует REST API больших двоичных объектов, URL-адрес имеет следующую структуру: *\<storage-Account-name \>. blob.core.windows.net/\<container-name \> / \<file-name \>* . <br>Если клиент использует Data Lake Storage REST API, URL-адрес имеет следующую структуру: *\<storage-Account-name \>. dfs.core.windows.net/\<file-System-name \> / \<file-name \>* . |
| recursive | string | `True` выполнить операцию со всеми дочерними каталогами; в противном случае `False`. <br>Отображается только для событий, запускаемых в учетных записях хранения BLOB-объектов с иерархическим пространством имен. |
| sequencer | string | Значение непрозрачной строки, представляющее логическую последовательность событий для любого отдельного имени большого двоичного объекта.  Пользователи могут использовать стандартное сравнение строк для понимания относительной последовательности двух событий в одном имени большого двоичного объекта. |
| storageDiagnostics | object | Диагностические данные, которые иногда включаются службой хранилища Azure. Если они присутствуют, то должны игнорироваться потребителями события. |

## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
* Основные сведения о работе с событиями хранилища BLOB-объектов см. в статье [Перенаправление событий хранилища BLOB-объектов в пользовательскую конечную веб-точку (предварительная версия)](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
