---
title: Примеры для службы хранилища Azure с использованием .NET | Документация Майкрософт
description: Просмотрите, загрузите и запустите образцы кода и приложений для хранилища Azure. Воспользуйтесь примерами для начала работы с большими двоичными объектами, очередями, таблицами и файлами с помощью клиентских библиотек хранилища .NET.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 57249bd2fa4d3c8aefe19a85ec9a2b6b584b00d2
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743955"
---
# <a name="azure-storage-samples-using-net"></a>Примеры для службы хранилища Azure с использованием .NET

В таблице ниже приведен обзор репозитория примеров и сценарии, описанные в каждом примере. Щелкните ссылки для просмотра соответствующего примера кода на сайте GitHub.

## <a name="blob-samples"></a>Примеры больших двоичных объектов

| **Сценарий** | **Пример кода** |
|--------------|-----------------|
| Добавление больших двоичных объектов | [Приступая к работе с большими двоичными объектами](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144) |
| Блочный BLOB-объект | [Веб-приложение фотоальбома для хранилища BLOB-объектов Azure](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Шифрование на стороне клиента | [Примеры шифрования больших двоичных объектов](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs) |
| Копирование BLOB-объекта | [Приступая к работе с большими двоичными объектами](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Create Container (Создание контейнера) | [Веб-приложение фотоальбома для хранилища BLOB-объектов Azure](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Delete BLOB (Удаление BLOB-объекта) | [Веб-приложение фотоальбома для хранилища BLOB-объектов Azure](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Delete Container (Удаление контейнера) | [Приступая к работе с большими двоичными объектами](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Метаданные, свойства и статистика больших двоичных объектов | [Приступая к работе с большими двоичными объектами](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| ACL, метаданные и свойства контейнера | [Веб-приложение фотоальбома для хранилища BLOB-объектов Azure](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Get Page Ranges (Получение диапазона страницы) | [Приступая к работе с большими двоичными объектами](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Аренда большого двоичного объекта и контейнера | [Приступая к работе с большими двоичными объектами](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Список больших двоичных объектов и контейнеров | [Приступая к работе с большими двоичными объектами](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| Страничный BLOB-объект | [Приступая к работе с большими двоичными объектами](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| SAS | [Приступая к работе с большими двоичными объектами](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Свойства службы | [Приступая к работе с большими двоичными объектами](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Создание моментального снимка большого двоичного объекта | [Backup Azure Virtual Machine Disks with Incremental Snapshots](https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs) (Резервное копирование дисков виртуальной машины Azure с помощью добавочных моментальных снимков) |

## <a name="file-samples"></a>Примеры файлов

| **Сценарий** | **Пример кода** |
|--------------|-----------------|
| Создание общих папок, каталогов и файлов | [Пример хранилища файлов .NET для службы хранилища Azure](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Удаление общих папок, каталогов и файлов | [Приступая к работе со службой файлов Azure в .NET](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs) |
| Метаданные и свойства каталога | [Пример хранилища файлов .NET для службы хранилища Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Скачивание файлов | [Пример хранилища файлов .NET для службы хранилища Azure](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Метрики, метаданные и свойства файла | [Пример хранилища файлов .NET для службы хранилища Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Свойства службы файлов | [Пример хранилища файлов .NET для службы хранилища Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Список каталогов и файлов | [Пример хранилища файлов .NET для службы хранилища Azure](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Список общих папок | [Пример хранилища файлов .NET для службы хранилища Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Статистика, метаданные и свойства общей папки | [Пример хранилища файлов .NET для службы хранилища Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |

## <a name="queue-samples"></a>Примеры очередей

| **Сценарий** | **Пример кода** |
|--------------|-----------------|
| Добавление сообщения | [Приступая к работе со службой очередей Azure в .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Шифрование на стороне клиента | [Очередь шифрования на стороне клиента .NET для службы хранилища Azure](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs) |
| Создание очередей | [Приступая к работе со службой очередей Azure в .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Удаление сообщения и очереди | [Приступая к работе со службой очередей Azure в .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Просмотр сообщения | [Приступая к работе со службой очередей Azure в .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| ACL, метаданные и статистика очереди | [Приступая к работе со службой очередей Azure в .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Свойства службы очередей | [Приступая к работе со службой очередей Azure в .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Сообщение об обновлении | [Приступая к работе со службой очередей Azure в .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |

## <a name="table-samples"></a>Примеры таблиц

| **Сценарий** | **Пример кода** |
|--------------|-----------------|
| Создание таблицы | [Управление параллелизмом с помощью службы хранилища Azure: пример приложения](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Удаление сущности или таблицы | [Getting Started with Azure Table Storage in .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) (Приступая к работе с хранилищем таблиц Azure в .NET) |
| Вставка, слияние или замена сущностей | [Управление параллелизмом с помощью службы хранилища Azure: пример приложения](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Query Entities (Сущности запроса) | [Getting Started with Azure Table Storage in .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) (Приступая к работе с хранилищем таблиц Azure в .NET) |
| Запросы к таблицам | [Getting Started with Azure Table Storage in .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) (Приступая к работе с хранилищем таблиц Azure в .NET) |
| Свойства и ACL таблицы | [Getting Started with Azure Table Storage in .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs) (Приступая к работе с хранилищем таблиц Azure в .NET) |
| Update Entity (Обновление сущности) | [Управление параллелизмом с помощью службы хранилища Azure: пример приложения](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |

## <a name="azure-code-samples-library"></a>Библиотека примеров кода Azure

Чтобы просмотреть полную библиотеку примеров, перейдите в [библиотеку примеров кода Azure](https://azure.microsoft.com/resources/samples/?service=storage), включающую примеры для службы хранилища Azure, которые можно скачать и запустить локально. Пример библиотеки кода содержит пример кода в формате ZIP. Кроме того, можно просмотреть и клонировать репозиторий GitHub для каждого примера.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Руководства по началу работы

Ознакомьтесь со следующими руководствами, если вам нужны инструкции по установке клиентских библиотек службы хранилища Azure и началу работы с ними.

* [Приступая к работе со службой BLOB-объектов Azure в .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Приступая к работе со службой очередей Azure в .NET](../storage-dotnet-how-to-use-queues.md)
* [Приступая к работе со службой таблиц Azure в .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Приступая к работе со службой файлов Azure в .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о примерах для других языков см. здесь:

* Java: [Примеры для службы хранилища Azure с использованием Java](storage-samples-java.md)
* Python: [Примеры для работы со службой хранилища Azure с помощью Python](storage-samples-python.md)
* Остальные языки: [Примеры для службы хранилища Azure](../storage-samples.md)
