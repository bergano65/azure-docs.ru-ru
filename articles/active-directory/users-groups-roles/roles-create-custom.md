---
title: Создание пользовательских ролей в элементе управления доступом на основе Azure AD Документы Майкрософт
description: Создавайте и назначайте пользовательские функции Azure AD с областью ресурсов в ресурсах Azure Active Directory.
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
ms.openlocfilehash: c2cb19c82f8c19bf87eeef755adb5756b2452512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025273"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Создание и присвоение пользовательской роли в Active Directory Azure

В этой статье описывается, как создавать новые пользовательские роли в active-каталоге Azure (Azure AD). Для основ пользовательских ролей см. [custom roles overview](roles-custom-overview.md) Роль может быть назначена либо на уровне каталога, либо только в области ресурсов регистрации приложений.

Пользовательские роли можно создавать во вкладке [«Роли и администраторы»](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) на странице обзора Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Создание роли на портале Azure

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Создание новой пользовательской роли для предоставления доступа к управлению регистрацией приложений

1. Вопийте в  [центре админов Azure Ad](https://aad.portal.azure.com)с привилегированным администратором ролей или глобальными разрешениями администратора в организации Azure AD.
1. Выберите **функции Active Directory** > Azure**и администраторы** > **Новой пользовательской роли.**

   ![Создание или отодевание ролей со страницы ролей и администраторов](./media/roles-create-custom/new-custom-role.png)

1. На вкладке **Основы,** укажите имя и описание роли, а затем нажмите **Далее**.

   ![предоставить имя и описание для пользовательской роли на вкладке Основы](./media/roles-create-custom/basics-tab.png)

1. На вкладке **«Разрешения»** выберите разрешения, необходимые для управления основными свойствами и учетными данными регистраций приложений. Подробное описание каждого разрешения можно узнать [в подтипах и разрешениях на регистрацию приложений в](./roles-custom-available-permissions.md)каталоге Active.
   1. Во-первых, введите "учетные данные" в панели поиска и выберите `microsoft.directory/applications/credentials/update` разрешение.

      ![Выберите разрешения для пользовательской роли на вкладке «Разрешения»](./media/roles-create-custom/permissions-tab.png)

   1. Далее введите "основной" в `microsoft.directory/applications/basic/update` панели поиска, выберите разрешение, а затем нажмите **Далее.**
1. На **вкладке «Обзор» — создайте** вкладку, просмотрите разрешения и выберите **«Создать».**

Ваша пользовательская роль будет отображаться в списке доступных ролей для присвоения.

## <a name="create-a-role-using-powershell"></a>Создание роли с помощью PowerShell

### <a name="prepare-powershell"></a>Подготовка PowerShell

Во-первых, необходимо [загрузить модуль Azure AD Preview PowerShell.](https://www.powershellgallery.com/packages/AzureADPreview)

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Назначить пользовательскую роль с помощью Azure AD PowerShell

Назначить роль с помощью ниже PowerShell сценарий:

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

## <a name="create-a-role-with-graph-api"></a>Создать роль с помощью API графика

1. Создайте определение роли.

    HTTP просит создать пользовательское определение роли.

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

1. Создайте назначение ролей.

    HTTP просит создать пользовательское определение роли.

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

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Назначить пользовательскую роль ресурсу

Например, встроенные роли, пользовательские роли назначаются по умолчанию в общеорганизационной сфере по умолчанию для предоставления разрешений на доступ ко всем регистрациям приложений в организации. Но в отличие от встроенных ролей, пользовательские роли также могут быть назначены в области одного ресурса Azure AD. Это позволяет дать пользователю разрешение на обновление учетных данных и основных свойств одного приложения без создания второй пользовательской роли.

1. Войти на работу в [центр админа Azure Ad](https://aad.portal.azure.com) с разрешениями разработчиков приложений в организации Azure AD.
1. Выберите **регистрацию приложений**.
1. Выберите регистрацию приложения, к которой вы предоставляете доступ к управлению. Возможно, вам придется выбрать **все приложения,** чтобы увидеть полный список регистраций приложений в организации Azure AD.

    ![Выберите регистрацию приложения в качестве области ресурсов для назначения ролей](./media/roles-create-custom/appreg-all-apps.png)

1. При регистрации приложения выберите **роли и администраторов.** Если вы еще не создали один, инструкции находятся в [предыдущей процедуре](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Выберите роль, чтобы открыть страницу **«Назначения».**
1. Выберите **назначение добавить** для добавления пользователя. Пользователю будут предоставлены любые разрешения только на выбранную регистрацию приложения.

## <a name="next-steps"></a>Дальнейшие действия

- Вы можете оставить комментарий на [форуме об административных ролях Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Дополнительные сведения о ролях и назначении роли администратора см. в разделе [Назначение ролей администратора](directory-assign-admin-roles.md).
- Сведения о разрешениях пользователей по умолчанию см. в разделе [сравнение разрешений гостевых пользователей и пользователей-участников](../fundamentals/users-default-permissions.md).
