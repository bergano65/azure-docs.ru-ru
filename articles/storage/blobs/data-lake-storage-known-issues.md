---
title: Известные проблемы с Azure Data Lake Storage 2-го поколения | Документация Майкрософт
description: Сведения об ограничениях и известных проблемах Azure Data Lake Storage 2-го поколения.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: f3861ab8839ba0483c5096e29cd09b6268bd765e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563918"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Известные проблемы с Azure Data Lake Storage 2-го поколения

В этой статье собраны сведения об ограничениях и известных проблемах Azure Data Lake Storage 2-го поколения.

## <a name="supported-blob-storage-features"></a>Поддерживаемые функции хранилища BLOB-объектов

Все большее число возможностей хранилища BLOB-объектов поддерживают учетные записи с иерархическим пространством имен. Актуальный полный список таких возможностей см. [здесь](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Интеграции поддерживаемых служб Azure

Azure Data Lake Storage 2-го поколения поддерживает несколько служб Azure, которые можно использовать для приема данных, выполнения аналитики и создания визуальных представлений. Список поддерживаемых служб Azure см. в статье [Службы Azure, которые поддерживают Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-azure-services.md).

Также см. [список служб Azure, которые поддерживают Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Поддерживаемые платформы с открытым исходным кодом

Несколько платформ с открытым исходным кодом, которые поддерживают Data Lake Storage 2-го поколения. Полный список см. в статье [Платформы с открытым кодом, поддерживающие Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-open-source-platforms.md).

См. статью [Платформы с открытым кодом, поддерживающие Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>API хранилища BLOB-объектов

API больших двоичных объектов и API Data Lake Storage 2-го поколения могут обрабатывать одни и те же данные.

В этом разделе описываются проблемы и ограничения, возникающие при использования API больших двоичных объектов и API Data Lake Storage 2-го поколения для обработки одних и тех же данных.

* Не поддерживается одновременная запись в один экземпляр файла через API больших двоичных объектов и API Data Lake Storage. Когда выполняется запись в файл с помощью API Data Lake Storage 2-го поколения, информация о блокировках этого файла не возвращается вызовом [Get Block List](https://docs.microsoft.com/rest/api/storageservices/get-block-list) из API больших двоичных объектов. Файл можно перезаписать с помощью API Data Lake Storage 2-го поколения или API больших двоичных объектов. Это не повлияет на свойства файла.

* Если вы выполняете операцию [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) без указания разделителя, результаты будут содержать не только большие двоичные объекты, но и каталоги. Если вы хотите указать разделитель, используйте только прямую косую черту (`/`). Это единственный поддерживаемый разделитель.

* Если вы используете API [Delete BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) для удаления каталога, удаляется только пустой каталог. Это означает, что API больших двоичных объектов невозможно использовать для рекурсивного удаления каталогов.

Следующие REST API больших двоичных объектов не поддерживаются:

* [Put BLOB](https://docs.microsoft.com/rest/api/storageservices/put-blob) (вставка BLOB-объекта);
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Get Page Ranges](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges) (получение диапазона страницы);
* [Incremental Copy Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob) (инкрементная копия Blob);
* [Put Page from URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) (размещение страницы по URL-адресу);
* [Put Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob) (добавление BLOB-объекта);
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Append Block from URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url) (добавление блока по URL-адресу).

Неуправляемые диски виртуальной машины не поддерживаются в учетных записях с иерархическим пространством имен. Если вы хотите использовать в учетной записи хранения иерархические пространства имен, разместите неуправляемые диски виртуальных машин в другой учетной записи хранения, для которой не включена функция иерархического пространства имен.

<a id="api-scope-data-lake-client-library"></a>

## <a name="file-system-support-in-sdks-powershell-and-azure-cli"></a>Поддержка файловых систем в пакетах SDK, PowerShell и Azure CLI

- Операции получения и сохранения списков управления доступом в данный момент не выполняются рекурсивно.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

Всегда используйте только последнюю версию AzCopy ([AzCopy 10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Более старые версии AzCopy, например AzCopy 8.1, не поддерживаются.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Обозреватель службы хранилища Azure

Используйте только версии `1.6.0` или выше.

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Обозреватель службы хранилища на портале Azure

Списки управления доступом пока не поддерживаются.

<a id="third-party-apps"></a>

## <a name="thirdpartyapplications"></a>Сторонние приложения

Сторонние приложения, использующие REST API, скорее всего, будут успешно работать в сочетании с приложениями Data Lake Storage 2-го поколения, которые вызывают API больших двоичных объектов.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Списки управления доступом (ACL) и анонимный доступ на чтение

Если для контейнера предоставлен [анонимный доступ на чтение](storage-manage-access-to-resources.md), то списки управления доступом не влияют на этот контейнер и (или) файлы в нем.

## <a name="premium-performance-blockblobstorage-storage-accounts"></a>Учетные записи хранения BlockBlobStorage с производительностью уровня "Премиум"

### <a name="diagnostic-logs"></a>Журналы диагностики

Журналы диагностики пока нельзя включить с помощью портала Azure. Их можно включить только с помощью PowerShell. Пример:

```powershell
#To login
Connect-AzAccount

#Set default block blob storage account.
Set-AzCurrentStorageAccount -Name premiumGen2Account -ResourceGroupName PremiumGen2Group

#Enable logging
Set-AzStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays 14
```

### <a name="lifecycle-management-policies"></a>Политики управления жизненным циклом

- Политики управления жизненным циклом для учетных записей хранения BlockBlobStorage категории "Премиум" пока не поддерживаются. 

- Перемещение данных с уровня "Премиум" на более низкие уровни не поддерживается. 

- Действие **удаления большого двоичного объекта** в настоящее время не поддерживается. 

### <a name="hdinsight-support"></a>Поддержка HDInsight

При создании кластера HDInsight вы не сможете выбрать учетную запись BlockBlobStorage, если для нее включена функция иерархического пространства имен. Но такую учетную запись можно подключить к кластеру после его создания.

### <a name="dremio-support"></a>Поддержка Dremio

Dremio пока невозможно подключить к учетной записи BlockBlobStorage, если для нее включена функция иерархического пространства имен. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Драйвер Windows Azure Storage Blob (WASB) (не поддерживается для Data Lake Storage 2-го поколения)

В настоящее время драйвер WASB, который создавался только для работы с API больших двоичных объектов, вызывает ряд проблем в некоторых типовых сценариях, в частности, если он используется как клиент для учетной записи хранения с иерархическим пространством имен. Доступ с использованием нескольких протоколов в Azure Data Lake Storage не позволяет решить эти проблемы. 

На текущий момент и в любом обозримом будущем не предоставляется поддержка клиентов, которые используют драйвер WASB в качестве клиента для учетной записи хранения с иерархическим пространством имен. Вместо него мы рекомендуем использовать драйвер [ABFS (Azure BLOB File System)](data-lake-storage-abfs-driver.md) в среде Hadoop. Если вам нужно выполнить миграцию из локальной среды Hadoop, у которой версия старше, чем Hadoop branch-3, откройте запрос в службу поддержки Azure, чтобы мы могли помочь вам и вашей организации.
