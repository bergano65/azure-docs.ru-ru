---
title: включить файл
description: включить файл
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4b10955a1d3b85acbcae109836bebc03ec04c72c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279567"
---
Azure предоставляет следующие встроенные роли RBAC для авторизации доступа к данным большого двоичного объекта и очереди с помощью Azure AD и OAuth:

- [Владелец данных BLOB-объекта хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner). Используйте, чтобы задать владение и управлять доступом POSIX для Azure Data Lake Storage 2-го поколения. Дополнительные сведения см. в статье [Контроль доступа в Azure Data Lake Storage 2-го поколения](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Участник данных BLOB-объектов хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor). Используйте, чтобы предоставить разрешения на чтение, запись и удаление для ресурсов хранилища BLOB-объектов.
- [Модуль чтения BLOB-объектов хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader). Используйте, чтобы предоставить доступ только для чтения к ресурсам хранилища больших двоичных объектов.
- [Участник данных очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): используйте, чтобы предоставить разрешения на чтение, запись и удаление для очередей Azure.
- [Модуль чтения очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): используйте для предоставления разрешений только на чтение для очередей Azure.
- [Обработчик сообщений данных очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): используется для предоставления разрешений на просмотр, извлечение и удаление сообщений в очередях службы хранилища Azure.
- [Отправитель сообщений очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): используйте, чтобы предоставить разрешения на добавление в сообщения в очередях службы хранилища Azure.

> [!NOTE]
> Для распространения назначений ролей RBAC может потребоваться до пяти минут.
>
> Только роли, явно определенные для доступа к данным, позволяют субъекту безопасности получать доступ к данным большого двоичного объекта или очереди. Роли, такие как **владелец**, **участник**и **участник учетной записи хранения** , позволяют субъекту безопасности управлять учетной записью хранения, но не предоставляют доступ к данным большого двоичного объекта или очереди в этой учетной записи.
>
> Доступ к данным большого двоичного объекта или очереди в портал Azure можно авторизовать с помощью учетной записи Azure AD или ключа доступа к учетной записи хранения. Дополнительные сведения см. [в статье использование портал Azure для доступа к данным BLOB-объектов или очередей](../articles/storage/common/storage-access-blobs-queues-portal.md).
