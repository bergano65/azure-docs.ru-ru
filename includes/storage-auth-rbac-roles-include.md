---
title: Включить имя файла
description: включить файл
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b49d757067d8ee888bee52f3931c8d48d786c044
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024910"
---
Azure предоставляет следующие встроенные роли RBAC для авторизации доступа к данным большого двоичного объекта и очереди с помощью Azure AD и OAuth:

- [Владелец данных BLOB-объекта хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner). Используйте, чтобы задать владение и управлять доступом POSIX для Azure Data Lake Storage 2-го поколения. Дополнительные сведения см. в статье [Контроль доступа в Azure Data Lake Storage 2-го поколения](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Участник данных BLOB-объектов хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor). Используйте, чтобы предоставить разрешения на чтение, запись и удаление для ресурсов хранилища BLOB-объектов.
- [Модуль чтения BLOB-объектов хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader). Используйте, чтобы предоставить доступ только для чтения к ресурсам хранилища больших двоичных объектов.
- [Делегирование BLOB-объектов хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): получите ключ делегирования пользователя, который будет использоваться для создания подписи общего доступа, подписанной с помощью учетных данных Azure AD для контейнера или большого двоичного объекта.
- [Участник данных очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): используйте, чтобы предоставить разрешения на чтение, запись и удаление для очередей Azure.
- [Модуль чтения очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): используйте для предоставления разрешений только на чтение для очередей Azure.
- [Обработчик сообщений данных очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): используется для предоставления разрешений на просмотр, извлечение и удаление сообщений в очередях службы хранилища Azure.
- [Отправитель сообщений очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): используйте, чтобы предоставить разрешения на добавление в сообщения в очередях службы хранилища Azure.

Только роли, явно определенные для доступа к данным, позволяют субъекту безопасности получать доступ к данным большого двоичного объекта или очереди. Встроенные роли, такие как **владелец**, **участник**и **участник учетной записи хранения** , позволяют субъекту безопасности управлять учетной записью хранения, но не предоставляют доступ к данным большого двоичного объекта или очереди в пределах этой учетной записи через Azure AD. Однако если роль включает в себя **Microsoft. Storage/storageAccounts/listKeys/Action**, то пользователь, которому назначена роль, может получить доступ к данным в учетной записи хранения с помощью авторизации общего ключа с помощью ключей доступа к учетной записи. Дополнительные сведения см. [в статье использование портал Azure для доступа к данным BLOB-объектов или очередей](../articles/storage/common/storage-access-blobs-queues-portal.md).

Подробные сведения о встроенных ролях RBAC для службы хранилища Azure для служб данных и службы управления см. в разделе " **хранилище** " [встроенных ролей Azure для Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage). Дополнительные сведения о различных типах ролей, которые предоставляют разрешения в Azure, см. в разделе [роли администратора классической подписки, роли RBAC Azure и роли Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Для распространения назначений ролей RBAC может потребоваться до пяти минут.
