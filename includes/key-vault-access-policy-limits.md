---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: c74f2543e96087378741d6388b7c27dc4d05ddc8
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89381240"
---
Хранилище ключей поддерживает до 1024 записей политики доступа, при этом каждая запись предоставляет отдельный набор разрешений конкретному субъекту безопасности. Из-за этого ограничения рекомендуется назначать политики доступа группам пользователей, где это возможно, а не отдельные пользователи. Использование групп значительно упрощает управление разрешениями для нескольких пользователей в Организации. Дополнительные сведения см. в статье [Управление доступом к приложениям и ресурсам с помощью групп Azure Active Directory](/azure/active-directory/fundamentals/active-directory-manage-groups) .

Дополнительные сведения об управлении доступом в Key Vault см. в разделе [Azure Key Vault Security: Identity and access management](/azure/key-vault/general/overview-security#identity-and-access-management) (Безопасность Azure Key Vault: управление удостоверениями и доступом). 
