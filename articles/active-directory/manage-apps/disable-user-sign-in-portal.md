---
title: Отключение входа пользователей в корпоративное приложение в Azure AD
description: Узнайте, как в Azure Active Directory можно отключить корпоративное приложение, чтобы пользователи не могли войти в него.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/12/2019
ms.author: kenwith
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0671d3dec963c0b475133881b00224cfe11e8370
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861650"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Отключение входа пользователя в корпоративное приложение в Azure Active Directory

Вы можете легко отключить корпоративное приложение, чтобы пользователи не могли войти в него в Azure Active Directory (Azure AD). Необходимы соответствующие разрешения для управления корпоративным приложением. И вы должны быть глобальным администратором для каталога.

## <a name="how-do-i-disable-user-sign-ins"></a>Как отключить вход пользователей?

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
1. Выберите **все службы**, введите **Azure Active Directory** в текстовом поле, а затем нажмите клавишу **Ввод**.
1. На панели **Azure Active Directory**  -   **_директоринаме_*_ (то есть на панели Azure AD для каталога, которым вы управляете) выберите _* корпоративные приложения**.
1. В области **корпоративные приложения — все приложения** отображается список приложений, которыми можно управлять. Выберите приложение.
1. На панели **_AppName_*_ (то есть в области с именем выбранного приложения в заголовке) выберите _* свойства**.
1. На панели **свойства _AppName_*_-_*** выберите **нет** для **включенных пользователей для входа в** систему.
1. Щелкните **Сохранить** .

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Отключение приложения, не включенного в список, с помощью Azure AD PowerShell

Если вы знакомы с идентификатором приложения, которое не отображается в списке корпоративных приложений (например, если вы удалили приложение или еще не создали субъект-службу в связи с тем, что приложение предварительно выполнило авторизацию Майкрософт), можно вручную создать субъект-службу для приложения, а затем отключить его с помощью [командлета PowerShell AzureAD](/powershell/module/azuread/New-AzureADServicePrincipal).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Просмотр всех моих групп](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Назначение корпоративному приложению пользователя или группы](assign-user-or-group-access-portal.md)
* [Удаление назначения пользователя или группы из корпоративного приложения](./assign-user-or-group-access-portal.md)
* [Изменение имени или логотипа корпоративного приложения](./add-application-portal-configure.md)
