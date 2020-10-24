---
author: baanders
description: включить файл для шага разрешений доступа в программе установки Azure Digital двойников
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: cbcaf4b4ad1b6c00f8c452582b986b6ee3b2806e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478871"
---
Azure Digital двойников использует [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) для управления доступом на основе РОЛЕЙ (RBAC). Это означает, что прежде чем пользователь сможет выполнять вызовы плоскости данных в ваш экземпляр Azure Digital двойников, ему нужно назначить роль с соответствующими разрешениями.

Для Azure Digital двойников эта роль является _**владельцем Azure Digital двойников данных**_. Дополнительные сведения о ролях и безопасности см. в статье [*Security for Azure Digital двойников Solutions*](../articles/digital-twins/concepts-security.md).

[!INCLUDE [digital-twins-role-rename-note.md](digital-twins-role-rename-note.md)]

В этом разделе показано, как создать назначение ролей для пользователя в своем экземпляре Azure Digital двойников, используя электронную почту этого пользователя в клиенте Azure AD в подписке Azure. В зависимости от вашей роли в организации вы можете настроить это разрешение самостоятельно или настроить его от имени другого пользователя, который будет управлять экземпляром Digital двойников Azure.

### <a name="assign-the-role"></a>Назначение роли

Чтобы предоставить пользователю разрешения на управление экземпляром Digital двойников Azure, необходимо назначить им роль _**владельца данных Digital двойников**_ в экземпляре.

> [!NOTE]
> Обратите внимание, что эта роль отличается от роли *владельца* Azure AD, которая также может быть назначена в области экземпляра Azure Digital двойников. Это две отдельные роли управления, и *владелец* Azure AD не предоставляет доступ к функциям плоскости данных, предоставленным *владельцем данных Azure Digital двойников*.