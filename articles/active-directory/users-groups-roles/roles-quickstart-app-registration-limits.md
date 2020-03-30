---
title: Удалите ограничения на создание регистрации приложений - Azure AD Документы Майкрософт
description: Назначить пользовательскую роль для предоставления неограниченной регистрации приложений в Active Directory Azure AD
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7acd76ff45f783f614b2a1d3f0d5c10d800a1ea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559051"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Быстрый запуск: Разрешение на предоставление грантов для создания неограниченной регистрации приложений

В этом quickstart вы создадите пользовательскую роль с разрешением на создание неограниченного числа регистраций приложений, а затем присвоите эту роль пользователю. Назначенный пользователь может использовать портал Azure AD, Azure AD PowerShell или API Microsoft Graph для создания регистрации приложений. В отличие от встроенной роли разработчика приложений, эта пользовательская роль предоставляет возможность создавать неограниченное количество регистраций приложений. Роль разработчика приложений предоставляет возможность, но общее количество созданных объектов ограничено 250, чтобы предотвратить попадание [в общую квоту объектов каталога.](directory-service-limits-restrictions.md)

Если у вас нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/) перед началом.

## <a name="prerequisite"></a>Предварительные требования

Наименее привилегированной ролью, необходимой для создания и присвоения пользовательских ролей Azure AD, является администратор привилегированных ролей.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Создание новой пользовательской роли с помощью портала Azure AD

1. Войти на  [админ-центр Azure Ad](https://aad.portal.azure.com)с разрешениями privileged Role или глобальными разрешениями администратора в организации Azure AD.
1. Выберите **Активный каталог Azure,** выберите **роли и администраторы,** а затем выберите **новую пользовательскую роль.**

    ![Создание или отодевание ролей со страницы ролей и администраторов](./media/roles-create-custom/new-custom-role.png)

1. На **вкладке "Основы"** предоставьте "Создатель регистрации приложений" для названия роли и "Может создать неограниченное количество регистраций приложений" для описания роли, а затем выберите **Next**.

    ![предоставить имя и описание для пользовательской роли на вкладке Основы](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. На **вкладке «Разрешения»** введите «microsoft.directory/applications/create» в поле поиска, а затем выберите флажки рядом с желаемыми разрешениями, а затем выберите **Следующий.**

    ![Выберите разрешения для пользовательской роли на вкладке «Разрешения»](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. На **вкладке «Обзор» — создайте** вкладку, просмотрите разрешения и выберите **«Создать».**

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Назначить роль пользователю с помощью портала Azure AD

1. Вопийте в  [центре админ-администратора Azure](https://aad.portal.azure.com)с привилегированным администратором ролей или разрешениями глобального администратора в вашей организации Azure AD.
1. Выберите **активный каталог Azure,** а затем выберите **роли и администраторов.**
1. Выберите роль создателя регистрации приложений и выберите **назначение Добавить.**
1. Выберите нужного пользователя и нажмите **Выберите,** чтобы добавить пользователя в роль.

Готово! В этом quickstart вы успешно создали пользовательскую роль с разрешением на создание неограниченного числа регистраций приложений, а затем присвоите эту роль пользователю.

> [!TIP]
> Чтобы присвоить роль приложению с помощью портала Azure AD, введите имя приложения в поле поиска страницы назначения. Приложения не отображаются в списке по умолчанию, но возвращаются в результатах поиска.

### <a name="app-registration-permissions"></a>Разрешения на регистрацию приложений

Существует два разрешения на предоставление возможности создавать регистрации приложений, каждое с разным поведением.

- microsoft.directory/applications/createAsOwner: Назначение этого разрешения приводит к добавлению создателя в качестве первого владельца созданной регистрации приложения, а регистрация созданного приложения будет учитываться против квоты 250 созданных объектов создателя.
- microsoft.directory/applicationPolicies/create: Назначение этого разрешения приводит к тому, что создатель не будет добавлен в качестве первого владельца созданной регистрации приложения, а регистрация созданного приложения не будет учитываться против квоты 250 созданных объектов создателя. Используйте это разрешение осторожно, потому что ничто не мешает назначенику создавать регистрации приложений до тех пор, пока не будет получена квота уровня каталога. Если назначены оба разрешения, это разрешение имеет приоритет.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Создание пользовательской роли с помощью Azure AD PowerShell

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Назначить пользовательскую роль с помощью Azure AD PowerShell

#### <a name="prepare-powershell"></a>Подготовка PowerShell

Во-первых, установите модуль Azure AD PowerShell из [галереи PowerShell.](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17) Затем импортируйте модуль предварительного просмотра Azure AD PowerShell, используя следующую команду:

```powershell
import-module azureadpreview
```

Чтобы убедиться, что модуль готов к использованию, сопой версию, возвращенную следующей командой, к указанной здесь:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Назначить пользовательскую роль

Назначить роль с помощью ниже PowerShell сценарий:

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

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Создание пользовательской роли с помощью API Microsoft Graph

HTTP запрос на создание пользовательской роли.

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

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Назначить пользовательскую роль с помощью API Microsoft Graph

Назначение роли сочетает в себе идентификатор основного идентификатора безопасности (который может быть пользователем или директором службы), идентификатор определения роли (роль) и область ресурсов Azure AD.

HTTP запрос на присвоение пользовательской роли.

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
