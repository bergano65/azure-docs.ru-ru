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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75460558"
---
## <a name="protect-your-access-keys"></a>Защита ключей доступа

Ключи доступа к учетной записи хранения аналогичны корневому паролю для вашей учетной записи хранения. Всегда будьте внимательны при защите ключей доступа. Используйте Azure Key Vault для безопасного управления ключами и их смены. Старайтесь не распространять ключи доступа для других пользователей, жестко программировать их или сохранять в любом месте в виде обычного текста, доступного другим пользователям. Если вы считаете, что они могли быть скомпрометированы, поворачивайте свои ключи.

По возможности используйте Azure Active Directory (Azure AD) для авторизации запросов к хранилищу BLOB-объектов и очередей вместо общего ключа. Azure AD обеспечивает более высокую безопасность и простоту использования общего ключа. Дополнительные сведения о предоставлении доступа к данным с помощью Azure AD см. в статье [авторизация доступа к BLOB-объектам и очередям Azure с помощью Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
