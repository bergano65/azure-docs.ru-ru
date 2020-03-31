---
title: включить файл
description: включить файл
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460558"
---
## <a name="protect-your-access-keys"></a>Защитите ключи доступа

Ключи доступа к учетной записи хранилища аналогичны корневому паролю для учетной записи хранилища. Всегда будьте осторожны, чтобы защитить ключи доступа. Используйте Azure Key Vault для безопасного управления и поворота ключей. Избегайте распространения ключей доступа другим пользователям, жесткого кодирования или сохранения их в любом месте в простом тексте, доступном для других. Поверните ключи, если вы считаете, что они могли быть скомпрометированы.

По возможности используйте Active Directory Azure (Azure AD) для авторизации запросов в хранилище Blob и Queue вместо общего ключа. Azure AD обеспечивает превосходную безопасность и простоту использования по сравнению с общим ключом. Для получения дополнительной информации об авторизации доступа [Authorize access to Azure blobs and queues using Azure Active Directory](../articles/storage/common/storage-auth-aad.md)к данным с Помощью Azure AD см.
