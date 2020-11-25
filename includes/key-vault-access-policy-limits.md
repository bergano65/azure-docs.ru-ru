---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 8c8c9563c954e3d1a84ea2b9f411187d5fb9f226
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026077"
---
Хранилище ключей поддерживает до 1024 политик доступа, при этом каждая запись предоставляет отдельный набор разрешений для определенного субъекта безопасности. Из-за этого ограничения рекомендуется назначать политики доступа группам пользователей, где это возможно, а не отдельные пользователи. Использование групп значительно упрощает управление разрешениями для нескольких пользователей в Организации. Дополнительные сведения см. в статье [Управление доступом к приложениям и ресурсам с помощью групп Azure Active Directory](../articles/active-directory/fundamentals/active-directory-manage-groups.md) .

Дополнительные сведения об управлении доступом в Key Vault см. в разделе [Azure Key Vault Security: Identity and access management](../articles/key-vault/general/overview-security.md#identity-and-access-management) (Безопасность Azure Key Vault: управление удостоверениями и доступом).