---
title: Создание пользовательского определения роли в управлении доступом на основе ролей в Azure AD — Azure Active Directory | Документация Майкрософт
description: Создание пользовательских ролей Azure AD с областью действия ресурса для Azure Active Directory ресурсов.
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
ms.openlocfilehash: ae773bc1a4e1831dbe462149bb827c26b7e74b96
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722298"
---
# <a name="create-a-custom-role-and-assign-a-resource-scope-in-azure-active-directory"></a>Создание настраиваемой роли и назначение области действия ресурса в Azure Active Directory

В этой статье описывается создание новых пользовательских ролей в Azure Active Directory (Azure AD). Пользовательские роли можно создать на вкладке **роли и администраторы** на странице Обзор Azure AD или [на странице регистрации приложения](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview). Роль может быть назначена либо в области уровня каталога, либо в контексте только регистраций приложений.

Дополнительные сведения см. в статье [Общие сведения о пользовательских ролях](roles-custom-overview.md) .

## <a name="using-the-azure-ad-portal"></a>Использование портала Azure AD

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Создание новой настраиваемой роли для предоставления доступа для управления регистрацией приложений

1. Войдите в  [центр администрирования Azure AD](https://aad.portal.azure.com)с правами администратора привилегированных ролей или глобального администратора в Организации Azure AD.
1. Выберите роли **Azure Active Directory** > **и администраторы** > **создать настраиваемую роль**.

   ![Создание или изменение ролей на странице "роли и Администраторы"](./media/roles-create-custom/new-custom-role.png)

1. На вкладке **Основные сведения** укажите имя и описание роли.

   ![Укажите имя и описание настраиваемой роли на вкладке "основы"](./media/roles-create-custom/basics-tab.png)

1. Чтобы выбрать разрешения, необходимые для управления учетными данными регистрации приложений, и основные свойства, такие как имя:
   1. В строке поиска введите "учетные данные" и выберите `microsoft.directory/applications/credentials/update` разрешение.

      ![Выберите разрешения для пользовательской роли на вкладке разрешения.](./media/roles-create-custom/permissions-tab.png)

   1. В строке поиска введите "Basic", выберите `microsoft.directory/applications/basic/update` разрешение и нажмите кнопку **Далее**.
1. На вкладке " **Проверка и создание** " Проверьте разрешения и нажмите кнопку " **создать**".

Ваша настраиваемая роль отобразится в списке доступных ролей для назначения.

## <a name="assign-a-role-scoped-to-a-resource"></a>Назначение роли, ограниченной для ресурса

Как и встроенные роли, пользовательские роли можно назначить в области организации по умолчанию, чтобы предоставить доступ ко всем регистрациям приложений. Однако пользовательские роли также могут быть назначены в области объекта. Это позволяет предоставить уполномоченному пользователю разрешение на обновление учетных данных и основных свойств отдельного приложения без создания второй настраиваемой роли.

1. Войдите в [центр администрирования Azure AD](https://aad.portal.azure.com) с разрешениями разработчика приложения в Организации Azure AD.
1. Щелкните **Регистрация приложений**.
1. Выберите регистрацию приложения, к которой предоставляется доступ для управления. Может потребоваться выбрать **все приложения** , чтобы просмотреть полный список регистраций приложений в вашей организации Azure AD.

    ![Выберите регистрацию приложения в качестве области действия ресурса для назначения роли](./media/roles-create-custom/appreg-all-apps.png)

1. В окне Регистрация приложения выберите **роли и администраторы**. Если вы еще не создали его, инструкции приведены в [предыдущей процедуре](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

    Если назначить эту роль из контекста открытой регистрации приложения, то у уполномоченного есть разрешения на регистрацию этого единого приложения. Назначаемая роль отображается в списке при каждой регистрации приложения. Эта модель доступа, в которой владелец может назначать разрешения для конкретных ресурсов Azure AD по ролям, аналогична модели, используемой в [Azure RBAC](../../role-based-access-control/overview.md) для контроля доступа к ресурсам Azure.

1. Выберите роль, чтобы открыть страницу **назначения** .
1. Выберите **добавить назначение** , чтобы добавить пользователя. Пользователю не будут предоставлены разрешения для любой регистрации приложения, кроме выбранного.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Создание настраиваемой роли с помощью Azure AD PowerShell

### <a name="prepare-powershell"></a>Подготовка PowerShell

Во-первых, необходимо [скачать модуль Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

Чтобы установить модуль Azure AD PowerShell, выполните следующие команды:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Чтобы проверить, готов ли модуль к использованию, выполните следующую команду:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Создание настраиваемой роли

Создайте новую роль, используя следующий сценарий PowerShell:

``` PowerShell
# Basic role information
$description = "Application Support Administrator"
$displayName = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/allProperties/read",
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="create-a-custom-role-using-microsoft-graph-api"></a>Создание настраиваемой роли с помощью Microsoft Graph API

1. Создайте определение роли.

    HTTP-запрос на создание пользовательского определения роли.

    ПОМЕСТИТЬ

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Body

    ``` HTTP
    {
    "description":"Can manage basic aspects of application registrations.",
    "displayName":"Application Support Administrator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "allowedResourceActions": 
            [
                "microsoft.directory/applications/basic/read",
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
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions/roleAssignments
    ```

    Body

    ``` HTTP
    {
    "principalId":"<GUID OF USER>",
    "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
    "resourceScope":["/<GUID OF APPLICATION REGISTRATION>"]
    }
    ```

## <a name="next-steps"></a>Следующие шаги

- Вы можете оставить комментарий на [форуме об административных ролях Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Дополнительные сведения о ролях и назначении роли администратора см. в разделе [Назначение ролей администратора](directory-assign-admin-roles.md).
- Сведения о разрешениях пользователей по умолчанию см. в разделе [сравнение разрешений гостевых пользователей и пользователей-участников](../fundamentals/users-default-permissions.md).
