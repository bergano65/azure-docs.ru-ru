---
title: Примеры для работы со службой хранилища Azure с помощью Python | Документация Майкрософт
description: Просмотрите, загрузите и запустите образцы кода и приложений для хранилища Azure. Воспользуйтесь примерами, чтобы начать работу с большими двоичными объектами, очередями, таблицами и файлами с помощью клиентских библиотек хранилища Python.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 3fc18646270bbec3836845bcf317afc2c1d3a6df
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351164"
---
# <a name="azure-storage-samples-using-python"></a>Примеры для работы со службой хранилища Azure с помощью Python

В таблицах ниже приведен обзор репозитория примеров и сценарии, описанные в каждом примере. Щелкните ссылки для просмотра соответствующего примера кода на сайте GitHub.

## <a name="blob-samples"></a>Примеры больших двоичных объектов

| **Сценарий** | **Пример кода** |
|--------------|-----------------|
| Добавление больших двоичных объектов | [Начало работы со службой BLOB-объектов Azure в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L166) |
| Блочный BLOB-объект | [Начало работы со службой BLOB-объектов Azure в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L77) |
| Шифрование на стороне клиента | [Управление ключами учетной записи хранения в Azure Key Vault с помощью Python](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| Копирование BLOB-объекта | [Начало работы со службой BLOB-объектов Azure в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L102) |
| Create Container (Создание контейнера) | [Начало работы со службой BLOB-объектов Azure в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L91) |
| Delete BLOB (Удаление BLOB-объекта) | [Начало работы со службой BLOB-объектов Azure в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L114) |
| Delete Container (Удаление контейнера) | [Начало работы со службой BLOB-объектов Azure в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L118) |
| Метаданные, свойства и статистика больших двоичных объектов | [Начало работы со службой BLOB-объектов Azure в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L298) |
| ACL, метаданные и свойства контейнера | [Начало работы со службой BLOB-объектов Azure в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L268) |
| Get Page Ranges (Получение диапазона страницы) | [Начало работы со службой BLOB-объектов Azure в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L151) |
| Аренда большого двоичного объекта и контейнера | [Начало работы со службой BLOB-объектов Azure в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L377) |
| Список больших двоичных объектов и контейнеров | [Начало работы со службой BLOB-объектов Azure в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L103) |
| Страничный BLOB-объект | [Начало работы со службой BLOB-объектов Azure в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L124) |
| SAS | [Использование подписанного URL-адреса в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L145) |
| Свойства службы | [Начало работы со службой BLOB-объектов Azure в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L540) |
| Создание моментального снимка большого двоичного объекта | [Начало работы со службой BLOB-объектов Azure в Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L214) |

## <a name="file-samples"></a>Примеры файлов

| **Сценарий** | **Пример кода** |
|--------------|-----------------|
| Создание общих папок, каталогов и файлов | [Начало работы со службой файлов Azure в Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L71) |
| Удаление общих папок, каталогов и файлов | [Начало работы со службой файлов Azure в Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L170) |
| Метаданные и свойства каталога | [Начало работы со службой файлов Azure в Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L175) |
| Скачивание файлов | [Начало работы со службой файлов Azure в Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L138) |
| Метрики, метаданные и свойства файла | [Начало работы со службой файлов Azure в Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L193) |
| Свойства службы файлов | [Начало работы со службой файлов Azure в Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L125) |
| Список каталогов и файлов | [Начало работы со службой файлов Azure в Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L153) |
| Список общих папок | [Начало работы со службой файлов Azure в Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L82) |
| Статистика, метаданные и свойства общей папки | [Начало работы со службой файлов Azure в Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L144) |

## <a name="queue-samples"></a>Примеры очередей

| **Сценарий** | **Пример кода** |
|--------------|-----------------|
| Добавление сообщения | [Начало работы со службой очередей Azure в Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L94) |
| Шифрование на стороне клиента | [Управление ключами учетной записи хранения в Azure Key Vault с помощью Python](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| Создание очередей | [Начало работы со службой очередей Azure в Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L75) |
| Удаление сообщения и очереди | [Начало работы со службой очередей Azure в Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L144) |
| Просмотр сообщения | [Начало работы со службой очередей Azure в Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L110) |
| ACL, метаданные и статистика очереди | [Начало работы со службой очередей Azure в Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L148) |
| Свойства службы очередей | [Начало работы со службой очередей Azure в Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L128) |
| Сообщение об обновлении | [Начало работы со службой очередей Azure в Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L120) |

## <a name="table-samples"></a>Примеры таблиц

| **Сценарий** | **Пример кода** |
|--------------|-----------------|
| Создание таблицы | [Начало работы со службой таблиц Azure в Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46) |
| Удаление сущности или таблицы | [Начало работы со службой таблиц Azure в Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79) |
| Вставка, слияние или замена сущностей | [Начало работы со службой таблиц Azure в Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57) |
| Query Entities (Сущности запроса) | [Начало работы со службой таблиц Azure в Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62) |
| Запросы к таблицам | [Начало работы со службой таблиц Azure в Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py) |
| Свойства и ACL таблицы | [Начало работы со службой таблиц Azure в Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138) |
| Update Entity (Обновление сущности) | [Начало работы со службой таблиц Azure в Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68) |

## <a name="azure-code-samples-library"></a>Библиотека примеров кода Azure

Чтобы просмотреть полную библиотеку примеров, перейдите в [библиотеку примеров кода Azure](https://azure.microsoft.com/resources/samples/?service=storage), включающую примеры для службы хранилища Azure, которые можно скачать и запустить локально. Пример библиотеки кода содержит пример кода в формате ZIP. Кроме того, можно просмотреть и клонировать репозиторий GitHub для каждого примера.

[!INCLUDE [storage-python-samples-include](../../../includes/storage-python-samples-include.md)]

## <a name="getting-started-guides"></a>Руководства по началу работы

Ознакомьтесь со следующими руководствами, если вам нужны инструкции по установке клиентских библиотек службы хранилища Azure и началу работы с ними.

* [Начало работы со службой BLOB-объектов Azure в Python](../blobs/storage-quickstart-blobs-python.md)
* [Начало работы со службой очередей Azure в Python](../queues/storage-python-how-to-use-queue-storage.md)
* [Начало работы со службой таблиц Azure в Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Начало работы со службой файлов Azure в Python](../files/storage-python-how-to-use-file-storage.md)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о примерах для других языков см. здесь:

* .NET: [Примеры для службы хранилища Azure с использованием .NET](storage-samples-dotnet.md)
* Java: [Примеры для службы хранилища Azure с использованием Java](storage-samples-java.md)
* JavaScript/Node.js: [Примеры для службы хранилища Azure с использованием JavaScript](storage-samples-javascript.md)
* Остальные языки: [Примеры для службы хранилища Azure](storage-samples.md)
