---
title: включение файла
description: включение файла
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: c2b409f0eefe5efa389432cbb007cc08e0c6ae1e
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71078276"
---
Azure предоставляет следующие встроенные роли RBAC для авторизации доступа к данным большого двоичного объекта и очереди с помощью Azure AD и OAuth:

- [Владелец данных BLOB-объекта хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Используется для установки владельца и управления доступом POSIX для Azure Data Lake Storage 2-го поколения. Дополнительные сведения см. в статье [Контроль доступа в Azure Data Lake Storage 2-го поколения](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Участник данных BLOB-объекта хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Предоставляет разрешения (чтение, запись и удаление) к ресурсам хранилища BLOB-объектов.
- [Модуль чтения данных BLOB-объекта хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Предоставляет разрешения только для чтения к ресурсам хранилища BLOB-объектов.
- [Участник данных очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Предоставляет разрешения (чтение, запись и удаление) к очередям Azure.
- [Модуль чтения данных очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Предоставляет разрешения только для чтения к очередям Azure.
- [Обработчик сообщений данных очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Используйте, чтобы предоставить разрешения на просмотр, получение и удаление сообщений в очередях службы хранилища Azure.
- [Отправитель сообщений очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Используйте, чтобы предоставить разрешения на добавление в сообщения в очередях службы хранилища Azure.

> [!NOTE]
> Для распространения назначений ролей RBAC может потребоваться до пяти минут.
>
> Только роли, явно определенные для доступа к данным, позволяют субъекту безопасности получать доступ к данным большого двоичного объекта или очереди. Роли, такие как **владелец**, **участник**и **участник учетной записи хранения** , позволяют субъекту безопасности управлять учетной записью хранения, но не предоставляют доступ к данным большого двоичного объекта или очереди в этой учетной записи.
