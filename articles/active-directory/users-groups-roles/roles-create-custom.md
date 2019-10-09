---
title: Создание и назначение пользовательской роли в управлении доступом на основе ролей в Azure AD — Azure Active Directory | Документация Майкрософт
description: Создание и назначение пользовательских ролей Azure AD с областью действия ресурса для Azure Active Directory ресурсов.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/04/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1a5aee1be7580956c32947e9bda7e2928a006c3
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026399"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Создание и назначение пользовательской роли в Azure Active Directory

В этой статье описывается создание новых пользовательских ролей в Azure Active Directory (Azure AD). Основные сведения о пользовательских ролях см. в разделе [Обзор пользовательских ролей](roles-custom-overview.md). Роль может быть назначена либо в области действия уровня каталога, либо в области действия ресурса регистрации приложения.

Пользовательские роли можно создать на вкладке [роли и администраторы](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) на странице Обзор Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Создание роли в портал Azure

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Создание новой настраиваемой роли для предоставления доступа для управления регистрацией приложений

1. Войдите в [центр администрирования Azure ad](https://aad.portal.azure.com) with с правами администратора привилегированных ролей или разрешения глобального администратора в Организации Azure AD.
1. Выберите **Azure Active Directory** > **ролей и администраторов** > **Новая настраиваемая роль**.

   ![Создание или изменение ролей на странице "роли и Администраторы"](./media/roles-create-custom/new-custom-role.png)

1. На вкладке **Основные сведения** укажите имя и описание роли, а затем нажмите кнопку **Далее**.

   ![Укажите имя и описание настраиваемой роли на вкладке "основы"](./media/roles-create-custom/basics-tab.png)

1. На вкладке **разрешения** выберите разрешения, необходимые для управления основными свойствами и свойствами учетных данных для регистрации приложений. Подробное описание каждого разрешения см. в разделе [подтипы регистрации приложений и разрешения в Azure Active Directory](./roles-custom-available-permissions.md).
   1. Сначала введите "учетные данные" в строке поиска и выберите разрешение `microsoft.directory/applications/credentials/update`.

      ![Выберите разрешения для пользовательской роли на вкладке разрешения.](./media/roles-create-custom/permissions-tab.png)

   1. Затем в строке поиска введите "Basic", выберите разрешение `microsoft.directory/applications/basic/update`, а затем нажмите кнопку **Далее**.
1. На вкладке " **Проверка и создание** " Проверьте разрешения и нажмите кнопку " **создать**".

Ваша настраиваемая роль отобразится в списке доступных ролей для назначения.

## <a name="create-a-role-using-powershell"></a>Создание роли с помощью PowerShell

### <a name="prepare-powershell"></a>Подготовка PowerShell

Сначала необходимо [скачать модуль PowerShell для предварительной версии Azure AD](https://www.powershellgallery.com/packages/AzureADPreview).

Чтобы установить модуль Azure AD PowerShell, выполните следующие команды:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Чтобы проверить, готов ли модуль к использованию, выполните следующую команду:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Создание настраиваемой роли

Создайте новую роль, используя следующий сценарий PowerShell:

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Назначение настраиваемой роли с помощью Azure AD PowerShell

Назначьте роль с помощью следующего скрипта PowerShell:

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

## <a name="create-a-role-with-graph-api"></a>Создание роли с API Graph

1. Создайте определение роли.

    HTTP-запрос на создание пользовательского определения роли.

    ПОМЕСТИТЬ

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Body

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

1. Создайте назначение ролей.

    HTTP-запрос на создание пользовательского определения роли.

    ПОМЕСТИТЬ

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Body

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Назначение пользовательской роли области действия ресурса

Как и встроенные роли, пользовательские роли назначаются по умолчанию в области всей Организации по умолчанию, чтобы предоставить разрешения на доступ для всех регистраций приложений в Организации. Но в отличие от встроенных ролей пользовательские роли также можно назначать в области действия одного ресурса Azure AD. Это позволяет предоставить пользователю разрешение на обновление учетных данных и основных свойств отдельного приложения без создания второй настраиваемой роли.

1. Войдите в [центр администрирования Azure AD](https://aad.portal.azure.com) с разрешениями разработчика приложения в Организации Azure AD.
1. Щелкните **Регистрация приложений**.
1. Выберите регистрацию приложения, к которой предоставляется доступ для управления. Может потребоваться выбрать **все приложения** , чтобы просмотреть полный список регистраций приложений в вашей организации Azure AD.

    ![Выберите регистрацию приложения в качестве области действия ресурса для назначения роли](./media/roles-create-custom/appreg-all-apps.png)

1. В окне Регистрация приложения выберите **роли и администраторы**. Если вы еще не создали его, инструкции приведены в [предыдущей процедуре](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Выберите роль, чтобы открыть страницу **назначения** .
1. Выберите **добавить назначение** , чтобы добавить пользователя. Пользователю будут предоставлены любые разрешения только для выбранной регистрации приложения.

## <a name="next-steps"></a>Следующие шаги

- Вы можете оставить комментарий на [форуме об административных ролях Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Дополнительные сведения о ролях и назначении роли администратора см. в разделе [Назначение ролей администратора](directory-assign-admin-roles.md).
- Сведения о разрешениях пользователей по умолчанию см. в разделе [сравнение разрешений гостевых пользователей и пользователей-участников](../fundamentals/users-default-permissions.md).
