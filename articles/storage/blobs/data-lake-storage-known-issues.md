---
title: Известные проблемы с Azure Data Lake Storage 2-го поколения | Документация Майкрософт
description: Узнайте об ограничениях и известных проблемах хранения azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 204b5dd4661b34aae8b76d65505a65e20f293f0f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115322"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Известные проблемы с Azure Data Lake Storage 2-го поколения

В этой статье описаны ограничения и известные проблемы хранения Azure Data Lake Data Gen2.

## <a name="supported-blob-storage-features"></a>Поддерживаемые функции хранилища BLOB-объектов

Все большее число функций хранения Blob теперь работают с учетными записями, которые имеют иерархическое пространство имен. Полный список можно найти [в функции хранилища данных Blob, доступные в Azure Data Lake Storage.](data-lake-storage-supported-blob-storage-features.md)

## <a name="supported-azure-service-integrations"></a>Поддерживаемые интеграции служб Azure

Azure Data Lake Storage 2 поддерживает несколько служб Azure, которые можно использовать для глотания данных, проведения аналитики и создания визуальных представлений. Для списка поддерживаемых служб Azure можно ознакомиться с [службами Azure, которые поддерживают данные Azure Data Lake Storage Gen2.](data-lake-storage-supported-azure-services.md)

Просмотрите [службы Azure, поддерживающие систему хранения данных Azure Data Lake Data Gen2.](data-lake-storage-supported-azure-services.md)

## <a name="supported-open-source-platforms"></a>Поддерживаемые платформы с открытым исходным кодом

Несколько платформ с открытым исходным кодом, которые поддерживают Data Lake Storage 2-го поколения. Полный список можно найти на [платформах с открытым исходным кодом, поддерживающих хранение данных Azure Data Lake Data Gen2.](data-lake-storage-supported-open-source-platforms.md)

Смотрите [платформы с открытым исходным кодом, поддерживающие данные Azure Data Lake Storage Gen2.](data-lake-storage-supported-open-source-platforms.md)

## <a name="blob-storage-apis"></a>API хранилища BLOB-объектов

Blob AБИ и AA-аПЫ хранения данных Озера Gen2 могут работать на одних и тех же данных.

В этом разделе описаны проблемы и ограничения с использованием blob AIS и AA-аДИ Data Lake Storage Gen2 для работы на одних и тех же данных.

* Для записи в один экземпляр файла нельзя использовать aIs Blob и AA, наносившие данные для хранения данных. Если вы пишете в файл с помощью API Data Lake Storage Gen2, то блоки этого файла не будут видны для вызовов на API [Blob Get Block List.](https://docs.microsoft.com/rest/api/storageservices/get-block-list) Перезапись файла можно с помощью aIS Data Lake Storage Gen2 или APIs Blob. Это не повлияет на свойства файлов.

* При использовании операции [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) без указания делимитера результаты будут включать как каталоги, так и капли. Если вы решите использовать делимитер, используйте`/`только передний слэш (). Это единственный поддерживаемый делимитер.

* Если вы используете API [Delete Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) для удаления каталога, этот каталог будет удален только в том случае, если он пуст. Это означает, что вы не можете использовать каталоги удаления Blob API.

Эти APIs Blob REST не поддерживаются:

* [Положите Блоб (Страница)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Get Page Ranges (Получение диапазона страницы)](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Инкрементная копия Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Поместите страницу с URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Положите Blob (приложение)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Блок приложения с URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Неуправляемые vM-диски не поддерживаются в учетных записях, которые имеют иерархическое пространство имен. Если требуется включить иерархическое пространство имен в учетной записи хранилища, поместите неуправляемые vM-диски в учетную запись хранения, в которой нет включенной иерархической функции пространства имен.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Поддержка файловой системы в SDK

Получить и установить ACL операции в настоящее время не повторяются.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Поддержка файловой системы в PowerShell и Azure CLI

- [Поддержка PowerShell](data-lake-storage-directory-file-acl-powershell.md) и [Azure CLI](data-lake-storage-directory-file-acl-cli.md) представлена в открытом доступе.
- Получить и установить ACL операции в настоящее время не повторяются.

## <a name="lifecycle-management-policies"></a>Политики управления жизненным циклом

* Удаление снимков blob пока не поддерживается.  

## <a name="archive-tier"></a>Архивный уровень

В настоящее время существует ошибка, которая влияет на уровень доступа к архиву.


## <a name="blobfuse"></a>Blobfuse

Blobfuse не поддерживается.

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Используйте только последнюю версию AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).Более ранние версии AzCopy, такие как AzCopy v8.1, не поддерживаются.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Обозреватель службы хранилища Azure

Используйте `1.6.0` только версии или выше.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Исследователь хранения на портале Azure

ACLs еще не поддерживаются.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Заявки третьих сторон

Сторонние приложения, которые используют REST AIS для работы, будут продолжать работать, если вы используете их с приложениями Data Lake Storage Gen2, которые называют Blob AIS, скорее всего, будут работать.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Списки управления доступом (ACL) и анонимный доступ к чтению

Если [анонимный доступ к чтению](storage-manage-access-to-resources.md) был предоставлен контейнеру, то ACL не влияют на этот контейнер или файлы в этом контейнере.

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Драйвер хранения данных Windows Azure (WASB) (не поддерживается с помощью Data Lake Storage Gen2)

В настоящее время драйвер WASB, который был разработан для работы только с Blob API, сталкивается с проблемами в нескольких распространенных сценариях. В частности, когда он является клиентом иерархической учетной записи хранения с поддержкой имен. Многопротоколный доступ к хранилищу озера данных не смягчит эти проблемы. 

В настоящее время (и, скорее всего, в обозримом будущем), мы не будем поддерживать клиентов, использующих драйвер WASB в качестве клиента для иерархической учетной записи хранения с поддержкой пространства имен. Вместо этого мы рекомендуем использовать драйвер [файловой системы Azure Blob (ABFS)](data-lake-storage-abfs-driver.md) в среде Hadoop. Если вы пытаетесь перенестись из среды на предпосылки Hadoop с версией раньше, чем ветвь Hadoop-3, пожалуйста, откройте билет поддержки Azure, чтобы мы могли связаться с вами на правильном пути для вас и вашей организации.
