---
title: Просмотр пользовательских ролевых заданий на портале Azure AD Документы Майкрософт
description: Теперь вы можете видеть и управлять членами роли администратора Azure AD в центре администратора Ad Azure.
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
ms.openlocfilehash: f04bd7788a9cc9657e14aedfb153182d6e53eb95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259712"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Просмотр пользовательских ролевых заданий в active-каталоге Azure

В этой статье описывается, как просматривать пользовательские роли, назначенные в Active Directory Azure (Azure AD). В Active Directory Azure (Azure AD) роли могут быть назначены в общеорганизационном диапазоне или с областью единого приложения.

- К назначению ролей добавляются и можно увидеть в списке назначений роли одного приложения.
- Назначения ролей в единой области приложения не добавляются и не могут быть замечены в списке общеорганизационных назначений.

## <a name="view-role-assignments-in-the-azure-portal"></a>Просмотр ролевых заданий на портале Azure

Эта процедура описывает просмотр назначений роли с общеорганизационным охватом.

1. Вопийте в  [центре админов Azure Ad](https://aad.portal.azure.com)с привилегированным администратором ролей или глобальными разрешениями администратора в организации Azure AD.
1. Выберите **Active Directory Azure,** выберите **роли и администраторы,** а затем выберите роль, чтобы открыть его и просмотреть ее свойства.
1. Выберите **назначения** для просмотра назначений для роли.

    ![Просмотр назначений ролей и разрешений при открытии роли из списка](./media/roles-view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Просмотр ролевых заданий с помощью Azure AD PowerShell

В этом разделе описаны просмотры назначений роли с общеорганизационным охватом. В этой статье используется модуль [Azure Active Directory PowerShell Version 2.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) Для просмотра заданий с одним приложением с помощью PowerShell можно использовать cmdlets в [пользовательских ролях Присваивать с PowerShell.](roles-assign-powershell.md)

### <a name="prepare-powershell"></a>Подготовка PowerShell

Во-первых, необходимо [загрузить модуль Azure AD для предварительного просмотра PowerShell.](https://www.powershellgallery.com/packages/AzureAD/)

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

### <a name="view-the-assignments-of-a-role"></a>Просмотр заданий роли

Пример просмотра заданий роли.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Просмотр ролевых заданий с помощью API Microsoft Graph

В этом разделе описаны просмотры назначений роли с общеорганизационным охватом.  Для просмотра назначений с одним приложением с помощью Graph API можно использовать операции в [пользовательских ролях Присваивайте с Помощью Graph API.](roles-assign-graph.md)

HTTP просит получить назначение роли для определения данной роли.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Ответ

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-assignments-of-single-application-scope"></a>Просмотр заданий области с одним приложением

В этом разделе описаны назначения просмотра роли с областью однопригостного применения. Эта функция сейчас доступна в виде общедоступной предварительной версии.

1. Вопийте в  [центре админов Azure Ad](https://aad.portal.azure.com)с привилегированным администратором ролей или глобальными разрешениями администратора в организации Azure AD.
1. Выберите **регистрацию приложений,** а затем выберите регистрацию приложения для просмотра его свойств. Возможно, вам придется выбрать **все приложения,** чтобы увидеть полный список регистраций приложений в организации Azure AD.

    ![Создание или отодвивание регистраций приложений со страницы регистрации приложений](./media/roles-create-custom/appreg-all-apps.png)

1. При регистрации приложения выберите **роли и администраторов,** а затем выберите роль для просмотра ее свойств.

    ![Просмотр назначений ролей регистрации приложений со страницы регистрации приложений](./media/roles-view-assignments/appreg-assignments.png)

1. Выберите **назначения** для просмотра назначений для роли. Открытие представления назначений из регистрации приложения показывает назначения, которые используются для этого ресурса Azure AD.

    ![Просмотр назначений ролей регистрации приложений из свойств регистрации приложения](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>Дальнейшие действия

* Вы можете оставить комментарий на [форуме об административных ролях Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Дополнительные сведения о ролях и назначении роли администратора см. в разделе [Назначение ролей администратора](directory-assign-admin-roles.md).
* Сведения о разрешениях пользователей по умолчанию см. в разделе [сравнение разрешений гостевых пользователей и пользователей-участников](../fundamentals/users-default-permissions.md).
