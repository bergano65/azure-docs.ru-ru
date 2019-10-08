---
title: Примеры для службы хранилища Azure с использованием JavaScript | Документация Майкрософт
description: Просмотрите, загрузите и запустите образцы кода и приложений для хранилища Azure. Воспользуйтесь примерами для начала работы с большими двоичными объектами, очередями, таблицами и файлами с помощью клиентских библиотек хранилища JavaScript/Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/26/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 363739bbab67b92e78e0cdb56cf98cdb8e1446eb
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350889"
---
# <a name="azure-storage-samples-using-javascript"></a>Примеры для службы хранилища Azure с использованием JavaScript

В таблицах ниже приведен обзор репозитория примеров и сценарии, описанные в каждом примере. Щелкните ссылки для просмотра соответствующего примера кода на сайте GitHub.

## <a name="blob-samples"></a>Примеры больших двоичных объектов

| **Сценарий** | **Пример кода** |
|--------------|-----------------|
| Блочный BLOB-объект | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L43) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Шифрование на стороне клиента | [Управление ключами учетной записи хранения в Azure Key Vault с помощью JavaScript](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| Копирование BLOB-объекта | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L73) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Create Container (Создание контейнера) | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L54) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Delete BLOB (Удаление BLOB-объекта) | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L103) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Delete Container (Удаление контейнера) | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L110) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Метаданные больших двоичных объектов | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L538) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Свойства большого двоичного объекта | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L478) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Container ACL (Список управления доступом для контейнера) | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L444) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Метаданные контейнера | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L409) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Container Properties (Свойства контейнера) | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L377) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Get Page Ranges (Получение диапазона страницы) | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L170) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Lease Blob (Аренда BLOB-объекта) | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L216) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Lease Container (Аренда контейнера) | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L185) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Список больших двоичных объектов и контейнеров | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L134) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Страничный BLOB-объект | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L129) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| SAS | [Использование подписанного URL-адреса на языке JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L257) |
| Свойства службы | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L308) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Настройка правил CORS | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L152) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |
| Создание моментального снимка большого двоичного объекта | [Getting Started with Azure Blob Service in JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L79) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript) |

## <a name="file-samples"></a>Примеры файлов

| **Сценарий** | **Пример кода** |
|--------------|-----------------|
| Создание общих папок, каталогов и файлов | [Getting Started with Azure File Service in JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L97) (Приступая к работе со службой файлов Azure на языке JavaScript) |
| Удаление общих папок, каталогов и файлов | [Getting Started with Azure File Service in JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L135) (Приступая к работе со службой файлов Azure на языке JavaScript) |
| Скачивание файлов | [Getting Started with Azure File Service in JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L128) (Приступая к работе со службой файлов Azure на языке JavaScript) |
| Список каталогов и файлов | [Getting Started with Azure File Service in JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L115) (Приступая к работе со службой файлов Azure на языке JavaScript) |
| Список общих папок | [Getting Started with Azure File Service in JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L187) (Приступая к работе со службой файлов Azure на языке JavaScript) |

## <a name="queue-samples"></a>Примеры очередей

| **Сценарий** | **Пример кода** |
|--------------|-----------------|
| Добавление сообщения | [Getting Started with Azure Queue Service in JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L142) (Приступая к работе со Службой очередей Azure на языке JavaScript) |
| Шифрование на стороне клиента | [Управление ключами учетной записи хранения в Azure Key Vault с помощью JavaScript](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| Создание очередей | [Getting Started with Azure Queue Service in JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L57) (Приступая к работе со Службой очередей Azure на языке JavaScript) |
| Удалить сообщение | [Getting Started with Azure Queue Service in JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L164) (Приступая к работе со Службой очередей Azure на языке JavaScript) |
| Удаление очереди. | [Getting Started with Azure Queue Service in JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L203) (Приступая к работе со Службой очередей Azure на языке JavaScript) |
| Перечисление очередей | [Getting Started with Azure Queue Service in JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L111) (Приступая к работе со Службой очередей Azure на языке JavaScript) |
| Просмотр сообщения | [Getting Started with Azure Queue Service in JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L170) (Приступая к работе со Службой очередей Azure на языке JavaScript) |
| ACL очереди | [Getting Started with Azure Queue Service in JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L192) (Приступая к работе со Службой очередей Azure на языке JavaScript) |
| Правила очереди CORS | [Getting Started with Azure Queue Service in JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L55) (Приступая к работе со Службой очередей Azure на языке JavaScript) |
| метаданные в очереди | [Getting Started with Azure Queue Service in JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L161) (Приступая к работе со Службой очередей Azure на языке JavaScript) |
| Свойства службы очередей | [Getting Started with Azure Queue Service in JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L94) (Приступая к работе со Службой очередей Azure на языке JavaScript) |
| Статистика очереди | [Getting Started with Azure Queue Service in JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L149) (Приступая к работе со Службой очередей Azure на языке JavaScript) |
| Сообщение об обновлении | [Getting Started with Azure Queue Service in JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L176) (Приступая к работе со Службой очередей Azure на языке JavaScript) |

## <a name="table-samples"></a>Примеры таблиц

| **Сценарий** | **Пример кода** |
|--------------|-----------------|
| Пакетная служба сущностей | [Getting Started with Azure Table Service in JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L87) (Приступая к работе со службой таблиц Azure на языке JavaScript) |
| Создание таблицы | [Getting Started with Azure Table Service in JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L41) (Приступая к работе со службой таблиц Azure на языке JavaScript) |
| Удаление сущности или таблицы | [Getting Started with Azure Table Service in JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L67) (Приступая к работе со службой таблиц Azure на языке JavaScript) |
| Вставка, слияние или замена сущностей | [Getting Started with Azure Table Service in JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) (Приступая к работе со службой таблиц Azure на языке JavaScript) |
| Вывод списка таблиц | [Getting Started with Azure Table Service in JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L63) (Приступая к работе со службой таблиц Azure на языке JavaScript) |
| Query Entities (Сущности запроса) | [Getting Started with Azure Table Service in JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L59) (Приступая к работе со службой таблиц Azure на языке JavaScript) |
| Запросы к таблицам | [Getting Started with Azure Table Service in JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L140) (Приступая к работе со службой таблиц Azure на языке JavaScript) |
| Запрос в диапазоне | [Getting Started with Azure Table Service in JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L102) (Приступая к работе со службой таблиц Azure на языке JavaScript) |
| SAS | [Использование подписанного URL-адреса на языке JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L87) |
| Таблица ACL | [Getting Started with Azure Table Service in JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L255) (Приступая к работе со службой таблиц Azure на языке JavaScript) |
| Правила таблицы CORS | [Getting Started with Azure Table Service in JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L149) (Приступая к работе со службой таблиц Azure на языке JavaScript) |
| Свойства таблицы | [Getting Started with Azure Table Service in JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L188) (Приступая к работе со службой таблиц Azure на языке JavaScript) |
| Статистика таблицы | [Getting Started with Azure Table Service in JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L243) (Приступая к работе со службой таблиц Azure на языке JavaScript) |
| Update Entity (Обновление сущности) | [Getting Started with Azure Table Service in JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) (Приступая к работе со службой таблиц Azure на языке JavaScript) |

## <a name="azure-code-samples-library"></a>Библиотека примеров кода Azure

Чтобы просмотреть полную библиотеку примеров, перейдите в [библиотеку примеров кода Azure](https://azure.microsoft.com/resources/samples/?service=storage), включающую примеры для службы хранилища Azure, которые можно скачать и запустить локально. Пример библиотеки кода содержит пример кода в формате ZIP. Кроме того, можно просмотреть и клонировать репозиторий GitHub для каждого примера.

[!INCLUDE [storage-node-samples-include](../../../includes/storage-node-samples-include.md)]

## <a name="getting-started-guides"></a>Руководства по началу работы

Ознакомьтесь со следующими руководствами, если вам нужны инструкции по установке клиентских библиотек службы хранилища Azure и началу работы с ними.

* [Getting Started with Azure Blob Service in JavaScript](../blobs/storage-quickstart-blobs-nodejs.md) (Приступая к работе со службой BLOB-объектов Azure на языке JavaScript)
* [Getting Started with Azure Queue Service in JavaScript](../queues/storage-nodejs-how-to-use-queues.md) (Приступая к работе со Службой очередей Azure на языке JavaScript)
* [Getting Started with Azure Table Service in JavaScript](../../cosmos-db/table-storage-how-to-use-nodejs.md) (Приступая к работе со службой таблиц Azure на языке JavaScript)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о примерах для других языков см. здесь:

* .NET: [Примеры для службы хранилища Azure с использованием .NET](storage-samples-dotnet.md)
* Java: [Примеры для службы хранилища Azure с использованием Java](storage-samples-java.md)
* Python: [Примеры для работы со службой хранилища Azure с помощью Python](storage-samples-python.md)
* Остальные языки: [Примеры для службы хранилища Azure](storage-samples.md)
