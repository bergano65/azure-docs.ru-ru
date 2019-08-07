---
title: Назначение пользовательских ролей с областью действия ресурса с помощью Azure PowerShell-Azure Active Directory | Документация Майкрософт
description: Управление членами настраиваемой роли администратора Azure AD с помощью Azure PowerShell.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/05/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ed8df92ac6a43a6cbf935bdb564f6cf0658608a
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812785"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Назначение пользовательских ролей с областью действия ресурса с помощью PowerShell в Azure Active Directory

В этой статье описывается, как создать назначение роли в масштабе всей Организации в Azure Active Directory (Azure AD). Назначение роли в масштабе всей организации предоставляет доступ в рамках всей Организации Azure AD. Сведения о создании назначения ролей с областью действия одного ресурса Azure AD см. в статье [Создание настраиваемой роли и ее назначение в области ресурсов](roles-create-custom.md). В этой статье используется модуль [Azure Active Directory PowerShell версии 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) .

Дополнительные сведения о ролях администратора Azure AD см. [в разделе Назначение ролей администратора в Azure Active Directory](directory-assign-admin-roles.md).

## <a name="required-permissions"></a>Необходимые разрешения

Подключитесь к клиенту Azure AD с помощью учетной записи глобального администратора, чтобы назначить или удалить роли.

## <a name="prepare-powershell"></a>Подготовка PowerShell

Установите модуль Azure AD PowerShell из [коллекция PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Затем импортируйте модуль предварительной версии Azure AD PowerShell с помощью следующей команды:

``` PowerShell
import-module azureadpreview
```

Чтобы убедиться, что модуль готов к использованию, сопоставьте версию, возвращенную следующей командой, указанному ниже.

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Теперь можно начать использование командлетов в модуле. Полное описание командлетов в модуле Azure AD см. в интерактивной справочной документации по модулю [Azure AD Preview](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17).

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Назначение роли пользователю или субъекту-службе с областью действия ресурса

1. Откройте модуль PowerShell для предварительной версии Azure AD.
1. Выполните вход, выполнив команду `Connect-AzureAD`.
1. Создайте новую роль, используя следующий сценарий PowerShell.

``` PowerShell
# Get the user and role definition you want to link
 $user = Get-AzureADMSUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
    "displayName eq 'f/128 Filter Photos'"
$resourceScopes = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScopes $resourceScopes -RoleDefinitionId $roleDefinition.objectId -PrincipalId $user.objectId
```

Чтобы назначить роль субъекту-службе, а не пользователю, используйте [командлет Get-азуреадмссервицепринЦипал](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0).

## <a name="operations-on-roledefinition"></a>Операции с определения роли

Объекты определения ролей содержат определение встроенной или пользовательской роли вместе с разрешениями, предоставленными этим назначением роли. В этом ресурсе отображаются пользовательские определения ролей и встроенные directoryRoles (которые отображаются в форме эквивалента определения роли). Сейчас в Организации Azure AD может быть определено не более 30 уникальных настраиваемых RoleDefinitions.

### <a name="create-operations-on-roledefinition"></a>Создание операций в определения роли

``` PowerShell
# Basic information

$description = "Application Registration Credential Administrator"
$displayName = "Custom Demo Admin"
$resourceScopes = @('/')
$templateId = "355aed8a-864b-4e2b-b225-ea95482e7570"

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/default/read",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinitions -RolePermissions $rolePermissions -ResourceScopes $resourceScopes -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>Операции чтения в определения роли

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
$customAdmin = Get-AzureADMSRoleDefinitions -ObjectId '86593cfc-114b-4a15-9954-97c3494ef49b'

# Get single role definition by templateId
$customAdmin = Get-AzureADMSRoleDefinitions -Filter "templateId eq '355aed8a-864b-4e2b-b225-ea95482e757not
```

### <a name="update-operations-on-roledefinition"></a>Обновление операций в определения роли

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinitions -ObjectId $customAdmin.ObjectId -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Удаление операций с определения роли

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -ObjectId $customAdmin.ObjectId
```

## <a name="operations-on-roleassignment"></a>Операции с RoleAssignment

Назначения ролей содержат сведения, связывающие указанного участника безопасности (субъекта-службы) с определением роли. При необходимости можно добавить область одного ресурса Azure AD для назначенных разрешений.  Ограничение области разрешений поддерживается для встроенных и пользовательских ролей.

### <a name="create-operations-on-roleassignment"></a>Создание операций в RoleAssignment

``` PowerShell
# Scopes to scope granted permissions to
$resourceScopes = @('/')

# IDs of principal and role definition you want to link
$principalId = "27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac"
$roleDefinitionId = $customKeyCredAdmin.ObjectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignments -ResourceScopes $resourceScopes -RoleDefinitionId -PrincipalId $principalId
```

### <a name="read-operations-on-roleassignment"></a>Операции чтения в RoleAssignment

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignments -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignments -Filter "principalId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Удаление операций с RoleAssignment

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignments -ObjectId $roleAssignment.ObjectId
```

## <a name="next-steps"></a>Следующие шаги

- Поделитесь с нами на [форуме по административным ролям Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Дополнительные сведения о ролях и назначениях ролей администратора Azure AD см. в статье [назначение ролей администратора](directory-assign-admin-roles.md).
- Сведения о разрешениях пользователей по умолчанию см. в разделе [сравнение разрешений гостевых пользователей и пользователей-участников](../fundamentals/users-default-permissions.md).
