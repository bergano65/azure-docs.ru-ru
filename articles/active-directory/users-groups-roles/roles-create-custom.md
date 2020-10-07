---
title: Создание пользовательских ролей в управлении доступом на основе ролей в Azure Active Directory | Документация Майкрософт
description: Создание и назначение пользовательских ролей Azure Active Directory в области действия ресурса для ресурсов Azure Active Directory.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4210ad382301851a41d3fbd7ee3dc20a748fb544
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802197"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Создание и назначение пользовательской роли в Azure Active Directory

В этой статье описано создание новых пользовательских ролей в Azure Active Directory (Azure AD). Основные сведения о пользовательских ролях см. на [этой странице](roles-custom-overview.md). Роль можно назначать только в области действия уровня каталога или в области действия ресурса регистрации приложения.

Пользовательские роли можно создавать на вкладке [Роли и администраторы](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) на странице обзора Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Создание роли на портале Azure

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Создание пользовательской роли с доступом для управления регистрацией приложений

1. Войдите в  [Центр администрирования Azure AD](https://aad.portal.azure.com)  с разрешениями "Администратор привилегированных ролей" или "Глобальный администратор" в организации Azure AD.
1. Выберите **Azure Active Directory** > **Роли и администраторы** > **Новая пользовательская роль**.

   ![Создание или изменение ролей на странице "Роли и администраторы"](./media/roles-create-custom/new-custom-role.png)

1. На вкладке **Основы** введите имя и описание роли и нажмите кнопку **Далее**.

   ![Ввод имени и описания пользовательской роли на вкладке "Основы"](./media/roles-create-custom/basics-tab.png)

1. На вкладке **Разрешения** выберите разрешения, необходимые для управления основными свойствами и свойствами учетных данных для регистрации приложений. Подробное описание каждого разрешения см. на странице [Подтипы регистрации приложений и разрешения в Azure Active Directory](./roles-custom-available-permissions.md).
   1. Сначала введите credentials в строке поиска и выберите разрешение `microsoft.directory/applications/credentials/update`.

      ![Выбор разрешений для пользовательской роли на вкладке "Разрешения"](./media/roles-create-custom/permissions-tab.png)

   1. Затем в строке поиска введите basic, выберите разрешение `microsoft.directory/applications/basic/update` и нажмите кнопку **Далее**.
1. На вкладке **Отзыв и создание** проверьте разрешения и нажмите кнопку **Создать**.

Ваша пользовательская роль отобразится в списке доступных ролей для назначения.

## <a name="create-a-role-using-powershell"></a>Создание роли с помощью PowerShell

### <a name="prepare-powershell"></a>Подготовка PowerShell

Во-первых, необходимо [скачать предварительную версию модуля Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Чтобы установить модуль Azure AD PowerShell, выполните следующие команды:

``` PowerShell
Install-Module AzureADPreview
Import-Module AzureADPreview
```

Чтобы проверить, готов ли модуль к использованию, выполните следующую команду:

``` PowerShell
Get-Module AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Создание настраиваемой роли

Создайте роль с помощью следующего сценария PowerShell:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Назначение пользовательской роли с помощью Azure AD PowerShell

Назначьте роль с помощью следующего сценария PowerShell:

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-role-with-graph-api"></a>Создание роли с помощью API Graph

1. Создайте определение роли.

    Ниже приведен HTTP-запрос на создание пользовательского определения роли.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Текст

    ``` HTTP
    {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
    }
    ```

    > [!Note]
    > `"templateId": "GUID"`— Это необязательный параметр, который отправляется в тексте в зависимости от требования. Если имеется требование создать несколько различных пользовательских ролей с общими параметрами, лучше всего создать шаблон и определить `templateId` значение. Вы можете создать `templateId` значение заранее с помощью командлета PowerShell `(New-Guid).Guid` . 

1. Создайте назначение роли.

    Ниже приведен HTTP-запрос на создание пользовательского определения роли.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Текст

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Назначение пользовательской роли, ограниченной областью действия ресурса

Как и встроенные роли, пользовательские роли назначаются по умолчанию в масштабах всей организации для предоставления разрешения на доступ для всех регистраций приложений в организации. Но в отличие от встроенных ролей, пользовательские роли также можно назначать в области действия отдельного ресурса Azure AD. Это позволяет предоставить пользователю разрешение на обновление учетных данных и основных свойств отдельного приложения без создания второй пользовательской роли.

1. Войдите в  [Центр администрирования Azure AD](https://aad.portal.azure.com) с разрешениями "Разработчик приложений" в организации Azure AD.
1. Щелкните **Регистрация приложений**.
1. Выберите регистрацию приложения, к которой предоставляется доступ для управления. Чтобы просмотреть полный список регистраций приложений в вашей организации Azure AD, выберите **Все приложения**.

    ![Выбор регистрации приложения в качестве области действия ресурса для назначения роли](./media/roles-create-custom/appreg-all-apps.png)

1. При регистрации приложения выберите **Роли и администраторы**. Если вы еще не создали ее, выполните инструкции приведенные в [разделе выше](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Затем выберите роль, чтобы открыть страницу **Назначения**.
1. Выберите **Добавить назначение**, чтобы добавить пользователя. Пользователю будут предоставлены разрешения только для выбранной регистрации приложения.

## <a name="next-steps"></a>Дальнейшие действия

- Вы можете оставить комментарий на [форуме об административных ролях Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Дополнительные сведения о ролях и назначении роли администратора см. в разделе [Назначение ролей администратора](directory-assign-admin-roles.md).
- Сведения о разрешениях пользователей по умолчанию см. в разделе [сравнение разрешений гостевых пользователей и пользователей-участников](../fundamentals/users-default-permissions.md).
