---
title: Предоставление пользователям доступа на основе ролей
titleSuffix: Azure Maps
description: Использование управления доступом на основе ролей для предоставления пользователям прав на Azure Maps
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 86f89397f3685443071788580253ee11ce4b70be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988449"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Предоставление пользователям доступа на основе ролей для Azure Maps

Вы предоставляете *Управление доступом на основе ролей* (RBAC), назначив группу Azure AD или субъекты безопасности одному или нескольким определениям ролей управления доступом Azure Maps. Чтобы просмотреть определения ролей RBAC, доступные для Azure Maps, перейдите в раздел **Управление доступом (IAM)**. Выберите **роли**, а затем найдите роли, которые начинаются с *Azure Maps*.

* Чтобы эффективно управлять большим объемом доступа пользователей к Azure Maps, см. статью [группы Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups).
* Чтобы разрешить пользователям проходить проверку подлинности в приложении, необходимо создать пользователей в Azure AD. См. статью [Добавление и удаление пользователей с помощью Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory).

Дополнительные сведения об [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/) для эффективного управления каталогом для пользователей.

1. Перейдите к **учетной записи Azure Maps**. Выберите назначение ролей **управления доступом (IAM)**  >  **Role assignment**.

    ![Предоставление RBAC](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. На вкладке **назначения ролей** в разделе **роль**выберите встроенное определение роли Azure Maps, например **Azure Maps модуль чтения данных** или **участник данных Azure Maps**. В поле **Назначение доступа к** выберите **Пользователь, группа или субъект-служба Azure AD**. Выберите участника по имени. Затем нажмите кнопку **Save** (Сохранить).

   * См. Дополнительные сведения о [добавлении и удалении назначений ролей](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

> [!WARNING]
> Azure Maps определения встроенных ролей предоставляют очень большие разрешения на доступ к множеству Azure Maps интерфейсов API. Чтобы ограничить число API пользователей минимальным, см. раздел [Создание пользовательского определения роли и назначение пользователей](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) для определения пользовательской роли. Это позволит пользователям иметь минимальные привилегии, необходимые для приложения.