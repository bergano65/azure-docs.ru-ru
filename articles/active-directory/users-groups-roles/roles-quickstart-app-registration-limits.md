---
title: Предоставление отдельного разрешения на превышение ограничений регистрации приложений — Azure Active Directory | Документация Майкрософт
description: Назначьте настраиваемую роль для предоставления неограниченных регистраций приложений в Active Directory Azure AD.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91de3de743d168bea207f27fb162486ea625a63
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026283"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Краткое руководство. Предоставление разрешения на создание неограниченных регистраций приложений

В этом кратком руководстве вы создадите пользовательскую роль с разрешениями на создание неограниченного числа регистраций приложений, а затем назначаете эту роль пользователю. После этого назначенный пользователь может использовать портал Azure AD, Azure AD PowerShell, Azure AD API Graph или Microsoft Graph API для создания регистраций приложений. В отличие от встроенной роли разработчика приложения, эта пользовательская роль предоставляет возможность создавать неограниченное количество регистраций приложений. Роль разработчика приложения предоставляет возможность, но общее число созданных объектов ограничено 250, чтобы не допустить попадания [в квоту на объект на уровне каталога](directory-service-limits-restrictions.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisite"></a>Предварительные требования

Для создания и назначения настраиваемых ролей Azure AD требуется роль администратора с минимальными полномочиями.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Создание новой настраиваемой роли с помощью портала Azure AD

1. Войдите в [центр администрирования Azure ad](https://aad.portal.azure.com) with с правами администратора привилегированных ролей или разрешения глобального администратора в Организации Azure AD.
1. Выберите **Azure Active Directory**, выберите **роли и администраторы**, а затем выберите **создать настраиваемую роль**.

    ![Создание или изменение ролей на странице "роли и Администраторы"](./media/roles-create-custom/new-custom-role.png)

1. На вкладке **Основные сведения** в поле Имя роли введите "создатель регистрации приложения" и "может быть создано неограниченное количество регистраций приложений" для описания роли, а затем нажмите кнопку " **Далее**".

    ![Укажите имя и описание настраиваемой роли на вкладке "основы"](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. На вкладке **разрешения** введите Microsoft. Directory/Applications/Create в поле поиска, а затем установите флажки рядом с нужными разрешениями и нажмите кнопку **Далее**.

    ![Выберите разрешения для пользовательской роли на вкладке разрешения.](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. На вкладке " **Проверка и создание** " Проверьте разрешения и нажмите кнопку " **создать**".

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Назначение роли пользователю с помощью портала Azure AD

1. Войдите в [центр администрирования Azure ad](https://aad.portal.azure.com) with с правами администратора привилегированных ролей или разрешения глобального администратора в вашей организации Azure AD.
1. Выберите **Azure Active Directory** и выберите **роли и администраторы**.
1. Выберите роль создателя регистрация приложения и щелкните **добавить назначение**.
1. Выберите нужного пользователя и нажмите кнопку **выбрать** , чтобы добавить пользователя к роли.

Готово! В этом кратком руководстве вы успешно создали пользовательскую роль с разрешениями на создание неограниченного числа регистраций приложений, а затем назначаете эту роль пользователю.

> [!TIP]
> Чтобы назначить роль приложению с помощью портала Azure AD, введите имя приложения в поле поиска на странице назначения. По умолчанию приложения не отображаются в списке, но возвращаются в результатах поиска.

### <a name="app-registration-permissions"></a>Разрешения регистрации приложений

Существует два разрешения, позволяющие предоставить возможность создания регистраций приложений с различными поведений.

- Microsoft. Directory/Applications/Креатеасовнер: Назначение этого разрешения приводит к добавлению создателя в качестве первого владельца созданной регистрации приложения, а созданная регистрация приложения будет считаться для квоты на создание объектов создателя 250.
- Microsoft. Directory/АппликатионполиЦиес/создать: Назначение этого разрешения приводит к добавлению создателя в качестве первого владельца созданной регистрации приложения, а созданная регистрация приложения не будет учитываться в квоте на создание объектов создателя 250. Внимательно используйте это разрешение, так как ничто не мешает ему создавать регистрации приложений до тех пор, пока не будет достигнута квота на уровне каталога. Если назначены оба разрешения, приоритет имеет это разрешение.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Создание настраиваемой роли с помощью Azure AD PowerShell

Создайте новую роль, используя следующий сценарий PowerShell:

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Назначение настраиваемой роли с помощью Azure AD PowerShell

#### <a name="prepare-powershell"></a>Подготовка PowerShell

Сначала установите модуль Azure AD PowerShell из [коллекция PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Затем импортируйте модуль предварительной версии Azure AD PowerShell с помощью следующей команды:

```powershell
import-module azureadpreview
```

Чтобы убедиться, что модуль готов к использованию, сопоставьте версию, возвращенную следующей командой, указанному ниже.

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

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Создание настраиваемой роли с помощью Microsoft Graph API

HTTP-запрос на создание настраиваемой роли.

ПОМЕСТИТЬ

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Body

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

Назначение ролей объединяет идентификатор субъекта безопасности (который может быть пользователем или субъектом-службой), идентификатор определения роли (роли) и область ресурсов Azure AD.

HTTP-запрос на назначение настраиваемой роли.

ПОМЕСТИТЬ

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Body

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Следующие шаги

- Вы можете оставить комментарий на [форуме об административных ролях Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Дополнительные сведения о ролях и назначении роли администратора см. в разделе [Назначение ролей администратора](directory-assign-admin-roles.md).
- Сведения о разрешениях пользователей по умолчанию см. в разделе [сравнение разрешений гостевых пользователей и пользователей-участников](../fundamentals/users-default-permissions.md).
