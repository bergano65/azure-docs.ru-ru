---
title: Включить имя файла
description: включить файл
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d73bab76860abf3e9fa442dad44e1ddb66d147a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519550"
---
Azure предоставляет следующие встроенные роли RBAC для авторизации доступа к данным большого двоичного объекта и очереди с помощью Azure AD и OAuth:

- [Владелец данных BLOB-объекта хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner). Используйте, чтобы задать владение и управлять доступом POSIX для Azure Data Lake Storage 2-го поколения. Дополнительные сведения см. в статье [Контроль доступа в Azure Data Lake Storage 2-го поколения](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Участник данных BLOB-объектов хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor). Используйте, чтобы предоставить разрешения на чтение, запись и удаление для ресурсов хранилища BLOB-объектов.
- [Модуль чтения BLOB-объектов хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader). Используйте, чтобы предоставить доступ только для чтения к ресурсам хранилища больших двоичных объектов.
- [Участник данных очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): используйте, чтобы предоставить разрешения на чтение, запись и удаление для очередей Azure.
- [Модуль чтения очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): используйте для предоставления разрешений только на чтение для очередей Azure.
- [Обработчик сообщений данных очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): используется для предоставления разрешений на просмотр, извлечение и удаление сообщений в очередях службы хранилища Azure.
- [Отправитель сообщений очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): используйте, чтобы предоставить разрешения на добавление в сообщения в очередях службы хранилища Azure.

Подробные сведения о встроенных ролях RBAC для службы хранилища Azure для служб данных и службы управления см. в разделе " **хранилище** " [встроенных ролей Azure для Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage). Дополнительные сведения о различных типах ролей, которые предоставляют разрешения в Azure, см. в разделе [роли администратора классической подписки, роли RBAC Azure и роли Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!NOTE]
> Для распространения назначений ролей RBAC может потребоваться до пяти минут.
>
> Только роли, явно определенные для доступа к данным, позволяют субъекту безопасности получать доступ к данным большого двоичного объекта или очереди. Роли, такие как **владелец**, **участник**и **участник учетной записи хранения** , позволяют субъекту безопасности управлять учетной записью хранения, но не предоставляют доступ к данным большого двоичного объекта или очереди в этой учетной записи.
>
> Доступ к данным большого двоичного объекта или очереди в портал Azure можно авторизовать с помощью учетной записи Azure AD или ключа доступа к учетной записи хранения. Дополнительные сведения см. [в статье использование портал Azure для доступа к данным BLOB-объектов или очередей](../articles/storage/common/storage-access-blobs-queues-portal.md).
