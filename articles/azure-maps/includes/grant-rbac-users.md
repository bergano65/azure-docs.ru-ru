---
title: Предоставление пользователям доступа на основе ролей
titleSuffix: Azure Maps
description: Использование управления доступом на основе ролей для предоставления пользователям прав на Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 19806fe24d0ff3b87ebe61b45ac302947c734fa0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895574"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Предоставление пользователям доступа на основе ролей для Azure Maps

Вы предоставляете *Управление доступом на основе ролей Azure (Azure RBAC)* , назначив группу Azure AD или субъекты безопасности одному или нескольким определениям ролей Azure Maps. Чтобы просмотреть определения ролей Azure, доступные для Azure Maps, перейдите в раздел **Управление доступом (IAM)** . Выберите **роли** , а затем найдите роли, которые начинаются с *Azure Maps* .

* Чтобы эффективно управлять большим объемом доступа пользователей к Azure Maps, см. статью [группы Azure AD](../../active-directory/fundamentals/active-directory-manage-groups.md).
* Чтобы разрешить пользователям проходить проверку подлинности в приложении, необходимо создать пользователей в Azure AD. См. статью [Добавление и удаление пользователей с помощью Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md).

Дополнительные сведения об [Azure AD](../../active-directory/fundamentals/index.yml) для эффективного управления каталогом для пользователей.

1. Перейдите к **учетной записи Azure Maps** . Выберите назначение ролей **управления доступом (IAM)**  >  **Role assignment** .

    ![Предоставление доступа с помощью Azure RBAC](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. На вкладке **назначения ролей** в разделе **роль** выберите встроенное определение роли Azure Maps, например **Azure Maps модуль чтения данных** или **участник данных Azure Maps** . В поле **Назначение доступа к** выберите **Пользователь, группа или субъект-служба Azure AD** . Выберите участника по имени. Затем нажмите кнопку **Save** (Сохранить).

   * См. Дополнительные сведения о [добавлении и удалении назначений ролей](../../role-based-access-control/role-assignments-portal.md).

> [!WARNING]
> Azure Maps определения встроенных ролей предоставляют очень большие разрешения на доступ к множеству Azure Maps интерфейсов API. Чтобы ограничить число API пользователей минимальным, см. раздел [Создание пользовательского определения роли и назначение пользователей](../../role-based-access-control/custom-roles.md) для определения пользовательской роли. Это позволит пользователям иметь минимальные привилегии, необходимые для приложения.