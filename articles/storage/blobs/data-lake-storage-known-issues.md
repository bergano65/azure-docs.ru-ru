---
title: Известные проблемы с Azure Data Lake Storage 2-го поколения | Документация Майкрософт
description: Сведения об ограничениях и известных проблемах Azure Data Lake Storage 2-го поколения.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7fd76be8d17dc1c632e555a56d038d4f5c1e1486
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668597"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Известные проблемы с Azure Data Lake Storage 2-го поколения

В этой статье описываются ограничения и известные проблемы Azure Data Lake Storage 2-го поколения.

## <a name="supported-blob-storage-features"></a>Поддерживаемые функции хранилища BLOB-объектов

Теперь все большее число функций хранилища BLOB-объектов работает с учетными записями, имеющими иерархическое пространство имен. Полный список см. в разделе [функции хранилища BLOB-объектов, доступные в Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Поддерживаемые интеграции служб Azure

ADLS 2-го поколения поддерживает несколько служб Azure, которые можно использовать для приема данных, выполнения аналитики и создания визуальных представлений. Список поддерживаемых служб Azure см. в статье [службы Azure, поддерживающие Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-azure-services.md).

Ознакомьтесь [со службами Azure, которые поддерживают Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Поддерживаемые платформы с открытым исходным кодом

Несколько платформ с открытым исходным кодом, которые поддерживают Data Lake Storage 2-го поколения. Полный список см. в разделе [платформы с открытым исходным кодом, которые поддерживают Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-open-source-platforms.md).

См. раздел [платформы с открытым исходным кодом, поддерживающий Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>API хранилища BLOB-объектов

API больших двоичных объектов и интерфейсы API Data Lake Storage 2-го поколения могут обрабатывать одни и те же данные.

В этом разделе описываются проблемы и ограничения использования API больших двоичных объектов и Data Lake Storage 2-го поколения API-интерфейсов для обработки одних и тех же данных.

* Для записи в один и тот же экземпляр файла нельзя использовать интерфейсы API BLOB и Data Lake Storage API. При записи в файл с помощью Data Lake Storage 2-го поколения API, блоки этого файла не будут видны для вызовов API [получения списка блоков](https://docs.microsoft.com/rest/api/storageservices/get-block-list) . Файл можно перезаписать с помощью Data Lake Storage 2-го поколения API или API больших двоичных объектов. Это не повлияет на свойства файла.

* При использовании операции " [список больших двоичных объектов](https://docs.microsoft.com/rest/api/storageservices/list-blobs) " без указания разделителя в результаты будут включены как каталоги, так и большие двоичные объекты. Если вы решили использовать разделитель, используйте только косую черту (`/`). Это единственный поддерживаемый разделитель.

* Если удалить каталог с помощью API [удаления BLOB-объектов](https://docs.microsoft.com/rest/api/storageservices/delete-blob) , этот каталог будет удален только в том случае, если он пуст. Это означает, что вы не сможете рекурсивно использовать API удаления каталогов.

API-интерфейсы RESTFUL для больших двоичных объектов не поддерживаются:

* [Разместить BLOB-объект (страница)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Получить диапазоны страниц](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Добавочное копирование большого двоичного объекта](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Размещение страницы по URL-адресу](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Размещение большого двоичного объекта (добавление)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Добавить блок из URL-адреса](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Неуправляемые диски виртуальной машины не поддерживаются в учетных записях, имеющих иерархическое пространство имен. Если вы хотите включить иерархическое пространство имен в учетной записи хранения, разместите неуправляемые диски виртуальной машины в учетной записи хранения, для которой не включена функция иерархического пространства имен.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Поддержка файловой системы в пакетах SDK

- [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md) и [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md) и поддержка доступны в общедоступной предварительной версии. Другие пакеты SDK в настоящее время не поддерживаются.
- Операции получения и установки ACL в настоящее время не являются рекурсивными.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Поддержка файловой системы в PowerShell и Azure CLI

- Поддержка [PowerShell](data-lake-storage-directory-file-acl-powershell.md) и [Azure CLI](data-lake-storage-directory-file-acl-cli.md) доступна в общедоступной предварительной версии.
- Операции получения и установки ACL в настоящее время не являются рекурсивными.

## <a name="lifecycle-management-policies"></a>Политики управления жизненным циклом

* Удаление моментальных снимков BLOB-объектов пока не поддерживается.  

* В настоящее время существуют ошибки, затрагивающие политики управления жизненным циклом и уровень доступа архива. 

## <a name="diagnostic-logs"></a>Журналы диагностики

Обозреватель службы хранилища Azure 1.10. x не может использоваться для просмотра журналов диагностики. Чтобы просмотреть журналы, используйте AzCopy или пакеты SDK.

## <a name="blobfuse"></a>Blobfuse

Blobfuse не поддерживается.



<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Используйте только последнюю версию AzCopy ([AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Более ранние версии AzCopy, например AzCopy v 8.1, не поддерживаются.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Обозреватель службы хранилища Azure

Используйте только версии `1.6.0` или выше. В настоящее время существует ошибка хранилища, влияющая на `1.11.0`версии  , которая может привести к ошибкам проверки подлинности в определенных сценариях. Исправлено исправление ошибки хранилища, но в качестве обходного решения рекомендуется использовать версию `1.10.x` , которая доступна для [бесплатной загрузки](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). на `1.10.x` не влияет ошибка хранилища.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Обозреватель службы хранилища в портал Azure

Списки управления доступом пока не поддерживаются.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Сторонние приложения

Сторонние приложения, использующие API-интерфейсы RESTFUL для работы, продолжают работать, если использовать их с Data Lake Storage 2-го поколения приложениями, которые вызывают API больших двоичных объектов, скорее всего, будут работать.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Списки управления доступом (ACL) и анонимный доступ на чтение

Если контейнеру предоставлен [анонимный доступ на чтение](storage-manage-access-to-resources.md) , ACL не влияют на этот контейнер или файлы в этом контейнере.

## <a name="windows-azure-storage-blob-wasb-driver"></a>Драйвер Windows Azure Storage Blob (WASB)

В настоящее время существует несколько проблем, связанных с использованием драйвера WASB вместе с учетными записями с иерархическим пространством имен. Мы рекомендуем использовать драйвер [файловой системы BLOB-объектов Azure (абфс)](data-lake-storage-abfs-driver.md) в рабочих нагрузках. 





