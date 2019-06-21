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
ms.openlocfilehash: 026717dff2b6883eb643497dec91226e4afe8133
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184961"
---
Azure предоставляет следующие встроенные роли RBAC для авторизации доступа к данным больших двоичных объектов и очереди, с помощью Azure AD и OAuth:

- [Владелец данных хранилища BLOB-объектов](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Предоставляет права владельца и позволяет управлять доступом POSIX для Azure Data Lake Storage 2-го поколения (предварительная версия). Дополнительные сведения см. в статье [Контроль доступа в Azure Data Lake Storage 2-го поколения](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Участник для данных больших двоичных объектов хранилища](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Предоставляет разрешения (чтение, запись и удаление) к ресурсам хранилища BLOB-объектов.
- [Модуль чтения данных хранилища BLOB-объектов](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Предоставляет разрешения только для чтения к ресурсам хранилища BLOB-объектов.
- [Участник для данных очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Предоставляет разрешения (чтение, запись и удаление) к очередям Azure.
- [Модуль чтения данных очереди хранилища](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Предоставляет разрешения только для чтения к очередям Azure.
- [Данные в хранилище очередей сообщений процессора](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Использовать для считывания grant, получение и разрешений на удаление сообщений в очередях службы хранилища Azure.
- [Данные в хранилище очереди отправителя сообщения](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Использовать для предоставления добавить разрешения для сообщения в очередях службы хранилища Azure.

> [!NOTE]
> Имейте в виду, что назначения ролей RBAC может занять до пяти минут для распространения.
