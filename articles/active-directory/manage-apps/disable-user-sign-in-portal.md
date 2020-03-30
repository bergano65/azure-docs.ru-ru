---
title: Отменяемые пользовательские встыки для корпоративного приложения в Azure AD
description: Узнайте, как в Azure Active Directory можно отключить корпоративное приложение, чтобы пользователи не могли войти в него.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10553898376c4b9236ee62718fffccd45b12d70b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274093"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Отключение входа пользователя в корпоративное приложение в Azure Active Directory

Отключить корпоративное приложение легко, чтобы пользователи не могли войти в него в Active Directory Azure (Azure AD). Для управления корпоративным приложением необходимы соответствующие разрешения. И, вы должны быть глобальным админ для каталога.

## <a name="how-do-i-disable-user-sign-ins"></a>Как отключить вход пользователей?

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
1. Выберите **все службы,** введите **Active Directory Azure** в текстовом поле, а затем выберите **Enter.**
1. В панели каталога Active -  ***DirectoryName*** **Azure**(т.е. панели Azure AD для управления каталогом) выберите **приложения Enterprise.**
1. В **приложениях Enterprise - Все панели приложений,** вы видите список приложений, которыми вы можете управлять. Выберите приложение.
1. В области ***имя_приложения*** (то есть в области с именем выбранного приложения в заголовке) выберите **Свойства**.
1. На ***панели приложения*** - **Свойства,** выберите **Нет** для **включенных для пользователей, чтобы войти в систему?.**
1. Щелкните **Сохранить** .

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Используйте Azure AD PowerShell для отключить незарегистрированное приложение

Если вы знаете AppId приложения, которое не отображается в списке приложений Enterprise (например, из-за того, что вы удалили приложение или директор службы еще не был создан из-за предварительного авторизованного приложения корпорацией Майкрософт), вы можете вручную создать принцип службы для приложения, а затем отключить его с помощью [AzureAD PowerShell cmdlet.](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0)

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
* [Удаление назначения пользователя или группы из корпоративного приложения](remove-user-or-group-access-portal.md)
* [Изменение имени или логотипа корпоративного приложения](change-name-or-logo-portal.md)
