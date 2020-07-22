---
title: Удаление ограничений на создание регистраций приложений в Azure AD | Документация Майкрософт
description: Назначение настраиваемой роли для предоставления неограниченного числа регистраций приложений в Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5acfa98636f54f87facf9771beb7d94dbd2b324
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.contentlocale: ru-RU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84731738"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Краткое руководство. Предоставление разрешения на создание неограниченного количества регистраций приложений

В этом кратком руководстве рассказывается о том, как создать настраиваемую роль с разрешением на создание неограниченного числа регистраций приложения, а затем назначить эту роль пользователю. После этого назначенный пользователь сможет создавать регистрации приложений с помощью портала Azure AD, Azure AD PowerShell или API Microsoft Graph. В отличие от встроенной роли разработчика приложения, эта настраиваемая роль предоставляет возможность создавать неограниченное количество регистраций приложений. Роль разработчика приложения также предоставляет такую возможность, но для предотвращения превышения [квоты на объекты на уровне каталога](directory-service-limits-restrictions.md) число создаваемых объектов ограничено 250.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisite"></a>Предварительные требования

Для создания и назначения настраиваемых ролей Azure AD требуется роль администратора с минимальными полномочиями.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Создание новой настраиваемой роли с помощью портала Azure AD

1. Войдите в  [Центр администрирования Azure AD](https://aad.portal.azure.com)  с разрешениями "Администратор привилегированных ролей" или "Глобальный администратор" в организации Azure AD.
1. Последовательно выберите **Azure Active Directory**, **Роли и администраторы** и **Новая пользовательская роль**.

    ![Создание или изменение ролей на странице "Роли и администраторы"](./media/roles-create-custom/new-custom-role.png)

1. На вкладке **Основные сведения** укажите в качестве имени роли "Создатель регистрации приложения" и добавьте для нее такое описание: "Может создавать неограниченное число регистраций приложений". Затем выберите **Далее**.

    ![Ввод имени и описания настраиваемой роли на вкладке "Основные сведения"](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. На вкладке **Разрешения** в поле поиска введите "microsoft.directory/applications/create", а затем установите флажки рядом с нужными разрешениями и нажмите **Далее**.

    ![Выбор разрешений для настраиваемой роли на вкладке "Разрешения"](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. На вкладке **Просмотр и создание** проверьте разрешения и нажмите кнопку **Создать**.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Назначение роли пользователю с помощью портала Azure AD

1. Войдите в  [Центр администрирования Azure AD](https://aad.portal.azure.com) с разрешениями "Администратор привилегированных ролей" или "Глобальный администратор" в организации Azure AD.
1. Выберите **Azure Active Directory**, а затем — **Роли и администраторы**.
1. Выберите роль "Создатель регистрации приложения" и щелкните **добавить назначения**.
1. Чтобы назначить необходимого пользователя на роль, выберите его, а затем щелкните **Выбрать**.

Готово! Из этого краткого руководства вы узнали, как создать настраиваемую роль с разрешением на создание неограниченного числа регистраций приложения, а затем назначить эту роль пользователю.

> [!TIP]
> Чтобы назначить роль приложению с помощью портала Azure AD, введите имя приложения в поле поиска на странице назначения. По умолчанию приложения не отображаются в списке, но возвращаются в результатах поиска.

### <a name="app-registration-permissions"></a>Разрешения для регистрации приложений

Создавать регистрации приложений можно при наличии по крайней мере одного из двух разрешений, отличающихся принципом действия.

- microsoft.directory/applications/createAsOwner. При назначении этого разрешения создатель добавляется в качестве первого владельца созданной регистрации приложения, которая учитывается при оценке соблюдения квоты создателя в 250 созданных объектах.
- microsoft.directory/applicationPolicies/create. При назначении этого разрешения создатель не добавляется в качестве первого владельца созданной регистрации приложения и она не учитывается при оценке соблюдения квоты создателя в 250 созданных объектах. Используйте это разрешение с осторожностью, так как в случае его применения ничто не мешает создавать регистрации приложений до тех пор, пока не будет исчерпана квота на уровне каталога. Если назначены оба разрешения, это разрешение имеет приоритет.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Создание настраиваемых ролей с помощью Azure AD PowerShell

Создайте роль с помощью следующего скрипта PowerShell:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Назначение настраиваемой роли с помощью Azure AD PowerShell

#### <a name="prepare-powershell"></a>Подготовка PowerShell

Сначала установите модуль Azure AD PowerShell [из коллекции PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Импортируйте модуль Azure AD PowerShell (предварительная версия) с помощью следующей команды:

```powershell
import-module azureadpreview
```

Чтобы убедиться в готовности модуля к использованию, сопоставьте версию, возвращенную следующей командой, с указанной здесь.

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Назначение настраиваемой роли

Назначьте роль с помощью следующего скрипта PowerShell:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Создание настраиваемой роли с помощью API Microsoft Graph

Ниже приведен HTTP-запрос на создание настраиваемой роли.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Текст

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Назначение настраиваемой роли с помощью API Microsoft Graph

Назначение ролей объединяет идентификатор субъекта безопасности (который может быть пользователем или субъектом-службой), идентификатор определения роли (или самой роли) и область ресурсов Azure AD.

Отправьте HTTP-запрос на назначение настраиваемой роли.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Текст

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Дальнейшие действия

- Вы можете оставить комментарий на [форуме об административных ролях Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Дополнительные сведения о ролях и назначении роли администратора см. в разделе [Назначение ролей администратора](directory-assign-admin-roles.md).
- Сведения о разрешениях пользователей по умолчанию см. в разделе [сравнение разрешений гостевых пользователей и пользователей-участников](../fundamentals/users-default-permissions.md).
