---
title: Просмотр разрешений роли администратора в центре администрирования — Azure Active Directory | Документация Майкрософт
description: Теперь вы можете просматривать членов роли администратора Azure AD и управлять ими в центре администрирования Azure AD.
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
ms.openlocfilehash: bf45d45481712e30d40bacec9a3c4d80d1ed56b6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707556"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Просмотр пользовательских назначений ролей в Azure Active Directory

В этой статье описывается, как просматривать настраиваемые роли, назначенные в Azure Active Directory (Azure AD). В Azure Active Directory (Azure AD) роли можно назначать на уровне каталога или с областью действия одного приложения. Назначения ролей в области каталога добавляются в список отдельных назначений ролей приложений, но назначения ролей в области одного приложения не добавляются в список назначений на уровне каталога.

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-the-azure-ad-portal"></a>Просмотр назначений роли с областью действия каталога с помощью портала Azure AD

1. Войдите в  [центр администрирования Azure AD](https://aad.portal.azure.com)с правами администратора привилегированных ролей или глобального администратора в Организации Azure AD.
1. Выберите **Azure Active Directory**, выберите **роли и администраторы**, а затем выберите роль, чтобы открыть ее и просмотреть ее свойства.
1. Выберите **назначения** , чтобы просмотреть назначения для роли.

    ![Просмотр назначений ролей и разрешений при открытии роли из списка](./media/roles-view-assignments/role-assignments.png)

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-azure-ad-powershell"></a>Просмотр назначений роли с областью действия каталога с помощью Azure AD PowerShell

Вы можете автоматизировать назначение ролей администратора Azure AD пользователям с помощью Azure PowerShell. В этой статье используется модуль [Azure Active Directory PowerShell версии 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) .

### <a name="prepare-powershell"></a>Подготовка PowerShell

Сначала необходимо [скачать модуль PowerShell для предварительной версии Azure AD](https://www.powershellgallery.com/packages/AzureAD/).

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

### <a name="view-the-assignments-of-a-role"></a>Просмотр назначений роли

Пример просмотра назначений роли.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-microsoft-graph-api"></a>Просмотр назначений роли с областью действия каталога с помощью Microsoft Graph API

HTTP-запрос на получение назначения роли для заданного определения роли.

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Отклик

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview"></a>Просмотр назначений роли с областью действия одного приложения с помощью портала Azure AD (Предварительная версия)

1. Войдите в  [центр администрирования Azure AD](https://aad.portal.azure.com)с правами администратора привилегированных ролей или глобального администратора в Организации Azure AD.
1. Выберите Azure Active Directory, выберите **Регистрация приложений**, а затем выберите регистрацию приложения, чтобы просмотреть его свойства. Может потребоваться выбрать **все приложения** , чтобы просмотреть полный список регистраций приложений в вашей организации Azure AD.

    ![Создание или изменение регистраций приложений на странице Регистрация приложений](./media/roles-create-custom/appreg-all-apps.png)

1. Выберите **роли и администраторы**, а затем выберите роль, чтобы просмотреть ее свойства.

    ![Просмотр назначений ролей регистрации приложений на странице Регистрация приложений](./media/roles-view-assignments/appreg-assignments.png)

1. Выберите **назначения** , чтобы просмотреть назначения для роли.

    ![Просмотр назначений ролей регистрации приложений из свойств регистрации приложения](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>Следующие шаги

* Вы можете оставить комментарий на [форуме об административных ролях Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Дополнительные сведения о ролях и назначении роли администратора см. в разделе [Назначение ролей администратора](directory-assign-admin-roles.md).
* Сведения о разрешениях пользователей по умолчанию см. в разделе [сравнение разрешений гостевых пользователей и пользователей-участников](../fundamentals/users-default-permissions.md).
