---
title: Назначение пользователя или группы корпоративному приложению в Azure Active Directory | Документы Майкрософт
description: Узнайте, как выбрать корпоративное приложение и назначить для него пользователя или группу в Azure Active Directory.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: celested
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4daab0255e739e011dca3ae0a016e3a15c7213b0
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565790"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Назначение пользователя или группы корпоративному приложению в Azure Active Directory
Чтобы назначить пользователя или группу в корпоративном приложении, необходимо иметь соответствующие разрешения для управления корпоративным приложением, а также права глобального администратора для доступа к каталогу.

> [!NOTE]
> Требования к лицензированию компонентов, рассматриваемых в этой статье, см. на странице [цен на Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

> [!NOTE]
> В приложениях Майкрософт (например, в приложениях Office 365) назначить пользователей в корпоративном приложении можно с помощью PowerShell.


## <a name="assign-a-user-to-an-app---portal"></a>Назначение приложения пользователю — портал
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
1. Выберите **Все службы**, в текстовом поле введите Azure Active Directory, а затем нажмите клавишу **ВВОД**.
1. Выберите **Корпоративные приложения**.
1. На **корпоративные приложения — все приложения** панели отобразится список приложений, вы можете управлять. Выберите приложение.
1. На ***appname*** области (то есть области с именем выбранного приложения в заголовке) выберите **пользователи и группы**.
1. На ***appname*** **-пользователи и группы** области выберите **добавить пользователя**.
1. На **Добавление назначения** области выберите **пользователей и групп**.

    ![Назначение приложению пользователя или группы](./media/assign-user-or-group-access-portal/assign-users.png)
1. На **пользователей и групп** области, выберите один или несколько пользователей или групп в списке, а затем выберите **выберите** кнопки в нижней части области.
1. На **Добавление назначения** области выберите **роли**. Затем на **Выбор роли** , выберите роль для применения к выбранных пользователей или группы, затем выбрать команду **ОК** в нижней части области.
1. На **Добавление назначения** области выберите **назначить** кнопки в нижней части области. У назначенных пользователей и групп будут разрешения, определенные выбранной ролью для этого корпоративного приложения.

## <a name="allow-all-users-to-access-an-app---portal"></a>Предоставление всем пользователям доступа к приложению — портал
Чтобы предоставить всем пользователям доступ к приложению, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
1. Выберите **Все службы**, в текстовом поле введите Azure Active Directory, а затем нажмите клавишу **ВВОД**.
1. Выберите **Корпоративные приложения**.
1. В области **Корпоративные приложения** выберите **All applications** (Все приложения). Отобразится список приложений, которыми вы можете управлять.
1. В области **Корпоративные приложения — All applications (Все приложения)** выберите приложение.
1. На ***appname*** области выберите **свойства**.
1. На  ***appname* -свойства** установите **требуется назначение пользователей?** присвоить **нет**. 

Параметр **Требуется назначение пользователей**:

- Не влияет на ли приложение отображается на панели доступа к приложениям. Чтобы отобразить приложение на панели доступа, необходимо назначить приложение соответствующим пользователям или группе.
- Только функции, используемые облачными приложениями с поддержкой единого входа SAML и локальными приложениями с поддержкой Application Proxy. См. дополнительные сведения о [едином входе для приложений](what-is-single-sign-on.md).
- Требует, чтобы пользователи предоставляли согласие для приложения. Администратор может предоставить согласие для всех пользователей.  См. дополнительные сведения о [настройки способа предоставления согласия для приложения пользователями](configure-user-consent.md).


## <a name="assign-a-user-to-an-app---powershell"></a>Назначение приложения пользователю — PowerShell

1. Откройте окно Windows PowerShell с повышенными привилегиями.

    >[!NOTE] 
    > Вам потребуется установить модуль Azure AD (с помощью команды `Install-Module -Name AzureAD`). Если будет предложено установить модуль NuGet или новый модуль PowerShell Azure для Active Directory версии 2, введите Y и нажмите клавишу ВВОД.

1. Выполните команду `Connect-AzureAD` и войдите в систему с помощью учетных данных глобального администратора.
1. Чтобы назначить пользователя и его роль в приложении, используйте следующий скрипт:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"
    
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```     

Дополнительные сведения о том, как назначить пользователю роль в приложении, см. в документации по командлету [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0).

Чтобы назначить группу корпоративному приложению, измените `Get-AzureADUser` на `Get-AzureADGroup`.

### <a name="example"></a>Пример

В этом примере пользователь Britta Simon назначается в приложении [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) с помощью PowerShell.

1. В PowerShell присвойте соответствующие значения переменным $username, $app_name и $app_role_name. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. В этом примере неизвестно, как именно называется роль приложения, которую мы хотим назначить пользователю Britta Simon. Выполните следующие команды, чтобы получить пользователя ($user) и субъект-службу ($sp) с помощью отображаемых имен участника-пользователя и субъекта-службы.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
1. Выполните команду `$sp.AppRoles`, чтобы отобразить роли, доступные для приложения Workplace Analytics. В этом примере мы хотим назначить пользователю Britta Simon роль Analyst (Limited Access) (аналитик с ограниченным доступом).
    
    ![Роль в приложении Workplace Analytics](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Присвойте имя роли переменной `$app_role_name`.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Выполните следующую команду, чтобы назначить пользователю роль в приложении:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Дальнейшие действия
* [Просмотр всех моих групп](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Удаление назначения пользователя или группы из корпоративного приложения](remove-user-or-group-access-portal.md)
* [Отключение входа пользователя в корпоративное приложение](disable-user-sign-in-portal.md)
* [Изменение имени или логотипа корпоративного приложения](change-name-or-logo-portal.md)
