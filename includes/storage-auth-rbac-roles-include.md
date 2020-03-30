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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279567"
---
Azure предоставляет следующие встроенные роли RBAC для авторизации доступа к данным о каплях и очередях с помощью Azure AD и OAuth:

- [Хранение владельца данных Blob:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)Использование для установки собственности и управления управлением доступом POSIX для хранения данных Azure Data Lake Data. Дополнительные сведения см. в статье [Контроль доступа в Azure Data Lake Storage 2-го поколения](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Хранилище Blob Data Contributor](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Используйте для предоставления разрешений на чтение/запись/удаление ресурсов хранилища Blob.
- [Хранение Blob Data Reader](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Использование для предоставления только для чтения разрешений на ресурсы хранения Blob.
- [Вкладчик очереди хранения:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)Используйте для предоставления разрешений на чтение/запись/удаление очередям Azure.
- [Хранение очереди чтения данных:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)Использование для предоставления только для чтения разрешений на очереди Azure.
- [Процессор сообщений для хранения данных очереди:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)используйте для предоставления разрешений на получение, извлечение и удаление сообщений в очередях хранения данных Azure.
- [Отправитель сообщений для хранения данных:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)Используйте для предоставления разрешений на добавление сообщений в очередях хранения azure.

> [!NOTE]
> Распространение ролевых заданий RBAC может занять до пяти минут.
>
> Только роли, четко определенные для доступа к данным, позволяют директору безопасности получить доступ к данным blob или очереди. Такие роли, как **Владелец,** **Вкладчик**и **Вкладчик учетной записи хранения,** позволяют директору безопасности управлять учетной записью хранилища, но не предоставляют доступ к данным капли или очереди в этой учетной записи.
>
> Доступ к данным о blob или очереди на портале Azure может быть авторизован с помощью учетной записи Azure AD или ключа доступа к учетной записи хранилища. Для получения дополнительной информации [см.](../articles/storage/common/storage-access-blobs-queues-portal.md)
