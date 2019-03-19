---
title: включение файла
description: включение файла
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9402c4b24c9d64b4b69d750fbd19de40cda396f3
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57218096"
---
Azure предоставляет следующие встроенные роли RBAC для доступа к данным хранилища:

- [Владелец данных BLOB-объектов хранилища (предварительная версия)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview). Предоставляет права владельца и позволяет управлять доступом POSIX для Azure Data Lake Storage 2-го поколения (предварительная версия). Дополнительные сведения см. в статье [Контроль доступа в Azure Data Lake Storage 2-го поколения](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Участник для данных больших двоичных объектов хранилища (предварительная версия)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview). Предоставляет разрешения (чтение, запись и удаление) к ресурсам хранилища BLOB-объектов.
- [Модуль чтения данных больших двоичных объектов хранилища (предварительная версия).](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview) Предоставляет разрешения только для чтения к ресурсам хранилища BLOB-объектов.
- [Участник для данных очереди хранилища (предварительная версия).](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview) Предоставляет разрешения (чтение, запись и удаление) к очередям Azure.
- [Модуль чтения данных очереди хранилища (предварительная версия).](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview) Предоставляет разрешения только для чтения к очередям Azure.

Дополнительные сведения о том, как встроенные роли определяются для службы хранилища Azure, см. в статье, посвященной [обзору определений ролей](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Сведения о создании настраиваемых ролей RBAC, см. в разделе [Создание пользовательских ролей для управления доступом к](../articles/role-based-access-control/custom-roles.md). 
