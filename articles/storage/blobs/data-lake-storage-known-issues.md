---
title: Известные проблемы с Azure Data Lake Storage 2-го поколения | Документация Майкрософт
description: Сведения об ограничениях и известных проблемах Azure Data Lake Storage 2-го поколения.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: e80d1a05765d224dc4682c6f64faccc8c81f8ebd
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007472"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Известные проблемы с Azure Data Lake Storage 2-го поколения

В этой статье описываются ограничения и известные проблемы Azure Data Lake Storage 2-го поколения.

## <a name="supported-blob-storage-features"></a>Поддерживаемые функции хранилища BLOB-объектов

Теперь все большее число функций хранилища BLOB-объектов работает с учетными записями, имеющими иерархическое пространство имен. Полный список см. в разделе [функции хранилища BLOB-объектов, доступные в Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Поддерживаемые интеграции служб Azure

Azure Data Lake Storage 2-го поколения поддерживает несколько служб Azure, которые можно использовать для приема данных, выполнения анализа и создания визуальных представлений. Список поддерживаемых служб Azure см. в статье [службы Azure, поддерживающие Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-azure-services.md).

Ознакомьтесь [со службами Azure, которые поддерживают Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Поддерживаемые платформы с открытым исходным кодом

Несколько платформ с открытым исходным кодом, которые поддерживают Data Lake Storage 2-го поколения. Полный список см. в разделе [платформы с открытым исходным кодом, которые поддерживают Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-open-source-platforms.md).

См. раздел [платформы с открытым исходным кодом, поддерживающий Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>API хранилища BLOB-объектов

API больших двоичных объектов и интерфейсы API Data Lake Storage 2-го поколения могут обрабатывать одни и те же данные.

В этом разделе описываются проблемы и ограничения использования API больших двоичных объектов и Data Lake Storage 2-го поколения API-интерфейсов для обработки одних и тех же данных.

* Для записи в один и тот же экземпляр файла нельзя использовать интерфейсы API BLOB и Data Lake Storage API. При записи в файл с помощью Data Lake Storage 2-го поколения API, блоки этого файла не будут видны для вызовов API [получения списка блоков](https://docs.microsoft.com/rest/api/storageservices/get-block-list) . Файл можно перезаписать с помощью Data Lake Storage 2-го поколения API или API больших двоичных объектов. Это не повлияет на свойства файла.

* При использовании операции " [список больших двоичных объектов](https://docs.microsoft.com/rest/api/storageservices/list-blobs) " без указания разделителя в результаты будут включены как каталоги, так и большие двоичные объекты. Если вы решили использовать разделитель, используйте только косую черту ( `/` ). Это единственный поддерживаемый разделитель.

* Если удалить каталог с помощью API [удаления BLOB-объектов](https://docs.microsoft.com/rest/api/storageservices/delete-blob) , этот каталог будет удален только в том случае, если он пуст. Это означает, что вы не сможете рекурсивно использовать API удаления каталогов.

API-интерфейсы RESTFUL для больших двоичных объектов не поддерживаются:

* [Разместить BLOB-объект (страница)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Get Page Ranges (Получение диапазона страницы)](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Добавочное копирование большого двоичного объекта](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Размещение страницы по URL-адресу](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Размещение большого двоичного объекта (добавление)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Добавить блок из URL-адреса](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Неуправляемые диски виртуальной машины не поддерживаются в учетных записях, имеющих иерархическое пространство имен. Если вы хотите включить иерархическое пространство имен в учетной записи хранения, разместите неуправляемые диски виртуальной машины в учетной записи хранения, для которой не включена функция иерархического пространства имен.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks-powershell-and-azure-cli"></a>Поддержка файловой системы в пакетах SDK, PowerShell и Azure CLI

- Операции получения и установки ACL в настоящее время не являются рекурсивными.
- Поддержка [Azure CLI](data-lake-storage-directory-file-acl-cli.md) доступна в общедоступной предварительной версии.


## <a name="lifecycle-management-policies"></a>Политики управления жизненным циклом

Удаление моментальных снимков BLOB-объектов пока не поддерживается. 

## <a name="archive-tier"></a>Уровень архива

В настоящее время существует ошибка, влияющая на уровень доступа к архиву.

## <a name="blobfuse"></a>Blobfuse

Blobfuse не поддерживается.

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Используйте только последнюю версию AzCopy ([AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).Более ранние версии AzCopy, например AzCopy v 8.1, не поддерживаются.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Обозреватель службы хранилища Azure

Используйте только версии  `1.6.0`   или выше.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Обозреватель службы хранилища в портал Azure

Списки управления доступом пока не поддерживаются.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Сторонние приложения

Сторонние приложения, использующие API-интерфейсы RESTFUL для работы, продолжают работать, если использовать их с Data Lake Storage 2-го поколения приложениями, которые вызывают API больших двоичных объектов, скорее всего, будут работать.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Списки управления доступом (ACL) и анонимный доступ на чтение

Если контейнеру предоставлен [анонимный доступ на чтение](storage-manage-access-to-resources.md) , ACL не влияют на этот контейнер или файлы в этом контейнере.

## <a name="premium-performance-block-blob-storage-accounts"></a>Учетные записи хранения для блочных BLOB-объектов уровня "Премиум"

### <a name="diagnostic-logs"></a>Журналы диагностики

Журналы диагностики еще нельзя включить с помощью портал Azure. Их можно включить с помощью PowerShell. Пример:

```powershell
#To login
Connect-AzAccount

#Set default block blob storage account.
Set-AzCurrentStorageAccount -Name premiumGen2Account -ResourceGroupName PremiumGen2Group

#Enable logging
Set-AzStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays 14
```

### <a name="lifecycle-management-policies"></a>Политики управления жизненным циклом

- Политики управления жизненным циклом еще не поддерживаются в учетных записях хранения блочных BLOB-объектов класса Premium 

- Перемещение данных с уровня "Премиум" на более низкие уровни невозможно. 

- Действие " **Удалить BLOB-объект** " сейчас не поддерживается. 

### <a name="hdinsight-support"></a>Поддержка HDInsight

При создании кластера HDInsight невозможно выбрать учетную запись хранения блочных BLOB-объектов, для которой включена функция иерархического пространства имен. Тем не менее учетную запись можно подключить к кластеру после ее создания.

### <a name="dremio-support"></a>Поддержка дремио

Дремио еще не подключается к учетной записи хранения блочных BLOB-объектов, для которой включена функция иерархического пространства имен. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Драйвер Windows Azure Storage Blob (WASB) (не поддерживается в Data Lake Storage 2-го поколения)

В настоящее время драйвер WASB, предназначенный для работы только с API больших двоичных объектов, сталкивается с проблемами в некоторых распространенных сценариях. В частности, если это клиент для иерархической учетной записи хранения с поддержкой пространств имен. Многопротокольный доступ на Data Lake Storage не позволит устранить эти проблемы. 

В течение времени (и, вероятнее всего, в будущем) мы не будем поддерживать клиентов, использующих драйвер WASB в качестве клиента для иерархической учетной записи хранения с поддержкой пространств имен. Вместо этого рекомендуется использовать драйвер [файловой системы Azure BLOB (абфс)](data-lake-storage-abfs-driver.md) в среде Hadoop. Если вы пытаетесь выполнить миграцию из локальной среды Hadoop с версией, предшествующей Hadoop Branch-3, откройте запрос в службу поддержки Azure, чтобы мы могли общаться с вами по нужному пути вперед и вашей организации.
