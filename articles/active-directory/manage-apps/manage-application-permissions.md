---
title: Управление разрешениями пользователей и администраторов — Azure Active Directory | Документация Майкрософт
description: Узнайте, как просматривать разрешения для приложения в Azure AD и управлять ими. Например, если требуется отозвать все разрешения, предоставленные приложению.
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d878c7b2f78d037e89235f3bb30c02fd11a7ae
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277650"
---
# <a name="take-action-on-overpriviledged-or-suspicious-application-in-azure-active-directory"></a>Примите участие в оверпривиледжед или подозрительном приложении в Azure Active Directory

Узнайте, как просматривать разрешения приложения и управлять ими. В зависимости от сценария в этой статье будут представлены различные действия, которые можно выполнить для защиты приложения. Это относится ко всем приложениям, добавленным в клиент Azure Active Directory (Azure AD) через согласие пользователя или администратора.

Дополнительные сведения о предоставлении согласия для приложений см. в статье [Платформа предоставления согласия Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Обязательные условия

Для выполнения приведенных ниже действий необходимо войти в систему как глобальный администратор, администратор приложения или администратор облачных приложений.

Чтобы ограничить доступ к приложениям, необходимо запросить назначение пользователя, а затем назначить пользователей или группы приложению.  Подробнее см. статью [Методы назначения пользователей и групп](methods-for-assigning-users-and-groups.md).

Чтобы получить контекстные скрипты PowerShell для выполнения действий, можно воспользоваться порталом Azure AD.
 
1. Войдите на [портал Azure](https://portal.azure.com) от имени глобального администратора, администратора приложения или администратора облачных приложений.
2. Выберите **Azure Active Directory**  >  **корпоративные приложения**.
3. Выберите приложение, доступ к которому необходимо ограничить.
4. Нажмите кнопку **Разрешения**. На панели команд выберите **проверить разрешения**.

![Просмотр разрешений](./media/manage-application-permissions/review-permissions.png)

## <a name="i-want-to-control-access-to-an-application"></a>Я хочу управлять доступом к приложению

Рекомендуется ограничить доступ к этому приложению, включив параметр назначения пользователя.

1. Войдите на [портал Azure](https://portal.azure.com) от имени глобального администратора, администратора приложения или администратора облачных приложений.
2. Выберите **Azure Active Directory**  >  **корпоративные приложения**.
3. Выберите приложение, доступ к которому необходимо ограничить.
4. Выберите **Свойства** , а затем задайте для параметра требование к пользователю значение Да.
5. Выберите **пользователь и группы** , а затем удалите ненужных пользователей, назначенных приложению.
6. Назначьте приложению пользователей или группы.

Необязательно, можно удалить всех пользователей, назначенных приложению с помощью PowerShell.

## <a name="i-want-to-revoke-all-permissions-for-an-application"></a>Я хочу отозвать все разрешения для приложения

С помощью PowerShell отменяет все разрешения, предоставленные этому приложению.

> [!NOTE]
> Отмена текущего предоставленного разрешения не приведет к запрету пользователям реконсеинг для приложений. Если вы хотите запретить пользователям отправку в приложение, прочитайте статью [Настройка согласия конечных пользователей для приложений](configure-user-consent.md).

Необязательно. Вы можете отключить приложение, чтобы запретить пользователям доступ к приложению и приложению из доступа к данным.

1. Войдите на [портал Azure](https://portal.azure.com) от имени глобального администратора, администратора приложения или администратора облачных приложений.
2. Выберите **Azure Active Directory**  >  **корпоративные приложения**.
3. Выберите приложение, доступ к которому необходимо ограничить.
4. Выберите **Свойства** , а затем установите флажок включить для пользователей вход в систему? Нет.

## <a name="application-is-suspicious-and-i-want-to-investigate"></a>Приложение является подозрительным, и я хочу исследовать его

Рекомендуется ограничить доступ к этому приложению, включив параметр назначения пользователей в и просматривая разрешения, предоставленные приложению пользователю и администраторам.

1. Войдите на [портал Azure](https://portal.azure.com) от имени глобального администратора, администратора приложения или администратора облачных приложений.
3. Выберите **Azure Active Directory**  >  **корпоративные приложения**.
5. Выберите приложение, доступ к которому необходимо ограничить.
6. Выберите **Свойства** , а затем задайте для параметра требование к пользователю значение Да.
7. Выберите **разрешения** и проверьте разрешения, предоставленные администратором и пользователем.

Необязательно, вы можете:

- С помощью PowerShell удалите всех пользователей, назначенных, чтобы предотвратить вход в приложение.
- С помощью PowerShell недействительные маркеры обновления для пользователей, имеющих доступ к приложению.
- Отмена всех разрешений для этого приложения с помощью PowerShell
- Отключите приложение, чтобы запретить пользователям доступ к данным и запретить им доступ к этим приложениям.


## <a name="application-is-malicious-and-im-compromised"></a>Приложение является вредоносным и скомпрометировано

Рекомендуется отключить приложение, чтобы запретить пользователям доступ к приложению и приложению из доступа к данным. Если вместо этого удалить приложение, конечные пользователи смогут повторно согласиться с приложением и предоставить доступ к данным.

1. Войдите на [портал Azure](https://portal.azure.com) от имени глобального администратора, администратора приложения или администратора облачных приложений.
2. Выберите **Azure Active Directory**  >  **корпоративные приложения**.
3. Выберите приложение, доступ к которому необходимо ограничить.
4. Выберите **Свойства** , а затем скопируйте идентификатор объекта.

### <a name="powershell-commands"></a>Команды PowerShell


Получение идентификатора объекта субъекта-службы

1. Войдите на [портал Azure](https://portal.azure.com) от имени глобального администратора, администратора приложения или администратора облачных приложений.
2. Выберите **Azure Active Directory**  >  **корпоративные приложения**.
3. Выберите приложение, доступ к которому необходимо ограничить.
4. Выберите **Свойства** , а затем скопируйте идентификатор объекта.

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
Удаление всех пользователей, назначенных приложению.
 ```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get Azure AD App role assignments using objectId of the Service Principal
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

    # Remove all users and groups assigned to the application
    $assignments | ForEach-Object {
        if ($_.PrincipalType -eq "User") {
            Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        } elseif ($_.PrincipalType -eq "Group") {
            Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        }
    }
 ```

Отозвать разрешения, предоставленные приложению

```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get all delegated permissions for the service principal
    $spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

    # Remove all delegated permissions
    $spOAuth2PermissionsGrants | ForEach-Object {
        Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
    }

    # Get all application permissions for the service principal
    $spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

    # Remove all delegated permissions
    $spApplicationPermissions | ForEach-Object {
        Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
    }
```
Сделать токены обновления недействительными
```powershell
        Connect-AzureAD

        # Get Service Principal using objectId
        $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

        # Get Azure AD App role assignments using objectID of the Service Principal
        $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

        # Revoke refresh token for all users assigned to the application
        $assignments | ForEach-Object {
            Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
        }
```
## <a name="next-steps"></a>Дальнейшие действия
- [Управление согласия для приложений и оценка запроса согласия](manage-consent-requests.md)
- [Настройка согласия пользователя](configure-user-consent.md)
- [Настройка рабочего процесса согласия администратора](configure-admin-consent-workflow.md)
