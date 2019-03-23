---
title: включение файла
description: включение файла
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: c7fa81f6d23962eedb3dfeafdd397b62a83d130e
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372685"
---
Azure предоставляет следующие встроенные роли RBAC для доступа к данным хранилища:

- [Владелец данных хранилища BLOB-объектов](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Предоставляет права владельца и позволяет управлять доступом POSIX для Azure Data Lake Storage 2-го поколения (предварительная версия). Дополнительные сведения см. в статье [Контроль доступа в Azure Data Lake Storage 2-го поколения](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Участник для данных больших двоичных объектов хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Предоставляет разрешения (чтение, запись и удаление) к ресурсам хранилища BLOB-объектов.
- [Модуль чтения данных хранилища BLOB-объектов](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Предоставляет разрешения только для чтения к ресурсам хранилища BLOB-объектов.
- [Участник для данных очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Предоставляет разрешения (чтение, запись и удаление) к очередям Azure.
- [Модуль чтения данных очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Предоставляет разрешения только для чтения к очередям Azure.
- [Данные в хранилище очередей сообщений процессора](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor-preview): Использовать для считывания grant, получение и разрешений на удаление сообщений в очередях службы хранилища Azure.
- [Данные в хранилище очереди отправителя сообщения](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender-preview): Использовать для предоставления добавить разрешения для сообщения в очередях службы хранилища Azure.

Дополнительные сведения о том, как встроенные роли определяются для службы хранилища Azure, см. в статье, посвященной [обзору определений ролей](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Сведения о создании настраиваемых ролей RBAC, см. в разделе [Создание пользовательских ролей для управления доступом к](../articles/role-based-access-control/custom-roles.md). 
