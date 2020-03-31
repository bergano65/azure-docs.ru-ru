---
title: Присвоить пользовательские роли с помощью Azure PowerShell - Azure AD Документы Майкрософт
description: Управление пользовательскими рольми администратора Azure AD с помощью Azure PowerShell.
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
ms.openlocfilehash: 0a2096b7899039e7a9d3455bc0c6fb3ec84ebd1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025320"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Назначайте пользовательские роли с областью ресурсов с помощью PowerShell в Active Directory Azure

В этой статье описывается, как создать назначение ролей в общеорганизационном разделе в Active Directory Azure (Azure AD). Назначение роли в общеорганизационном диапазоне предоставляет доступ к организации Azure AD. Чтобы создать назначение ролей с областью единого ресурса Azure AD, [см. Как создать пользовательскую роль и назначить ее в области ресурсов.](roles-create-custom.md) В этой статье используется модуль [Azure Active Directory PowerShell Version 2.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)

Для получения дополнительной информации о функциях администратора Ad Azure [см.](directory-assign-admin-roles.md)

## <a name="required-permissions"></a>Необходимые разрешения

Подключитесь к вашему администратору Azure AD с помощью глобальной учетной записи администратора для присвоения или удаления ролей.

## <a name="prepare-powershell"></a>Подготовка PowerShell

Установите модуль Azure AD PowerShell из [галереи PowerShell.](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17) Затем импортируйте модуль предварительного просмотра Azure AD PowerShell, используя следующую команду:

``` PowerShell
import-module azureadpreview
```

Чтобы убедиться, что модуль готов к использованию, сопой версию, возвращенную следующей командой, к указанной здесь:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Теперь можно начать использование командлетов в модуле. Полное описание cmdlets в модуле Azure AD смотрите онлайн-справочную документацию для [модуля предварительного просмотра Azure AD.](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Назначить роль пользователю или директору службы с областью охвата ресурсов

1. Откройте модуль предварительного просмотра Azure AD PowerShell.
1. Восподписаните, выдав команду. `Connect-AzureAD`
1. Создайте новую роль, используя следующий сценарий PowerShell.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Чтобы назначить роль директору службы вместо пользователя, используйте [cmdlet Get-AzureADMSServicePrincipal.](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0)

## <a name="operations-on-roledefinition"></a>Операции по определению ролей

Объекты определения ролей содержат определение встроенной или пользовательской роли, а также разрешения, выданные этим назначением ролей. Этот ресурс отображает как пользовательские определения ролей, так и встроенные каталогиRolesы (которые отображаются в эквивалентной форме roleDefinition). Сегодня организация Azure AD может определить максимум 30 уникальных пользовательских определений ролей.

### <a name="create-operations-on-roledefinition"></a>Создание операций по определению ролей

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/standard/read",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>Читать операции по определению ролей

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Обновление операций по определению ролей

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Удаление операций на РолевОпределение

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Операции по назначению ролей

Назначения ролей содержат информацию, связывающую данный принцип безопасности (пользователя или руководителя службы приложения) с определением роли. При необходимости можно добавить область единого ресурса Azure AD для назначенных разрешений.  Ограничение сферы действия разрешений поддерживается для встроенных и пользовательских ролей.

### <a name="create-operations-on-roleassignment"></a>Создание операций по назначению ролей

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

### <a name="read-operations-on-roleassignment"></a>Читать операции по назначению ролей

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Удаление операций по назначению ролей

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Дальнейшие действия

- Поделитесь с нами на [форуме административных ролей Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Подробнее о ролях и назначениях функций [Assign administrator roles](directory-assign-admin-roles.md)администратора администратора AD, см.
- Сведения о разрешениях пользователей по умолчанию см. в разделе [сравнение разрешений гостевых пользователей и пользователей-участников](../fundamentals/users-default-permissions.md).
