---
title: Назначение пользователя или группы корпоративному приложению в Azure Active Directory | Документы Майкрософт
description: Узнайте, как выбрать корпоративное приложение и назначить для него пользователя или группу в Azure Active Directory.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d28b9c31b8fbad8a565ff8cbdf717bfb3bc1309
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72896474"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Назначение пользователя или группы корпоративному приложению в Azure Active Directory

Чтобы назначить пользователя или группу для корпоративного приложения, необходимо назначить любую из этих ролей администратора: глобальный администратор, администратор приложения, администратор облачных приложений или назначить его владельцем корпоративного приложения.  В приложениях Майкрософт (например, в приложениях Office 365) назначить пользователей в корпоративном приложении можно с помощью PowerShell.

> [!NOTE]
> Требования к лицензированию компонентов, рассматриваемых в этой статье, см. на странице [цен на Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="assign-a-user-to-an-app---portal"></a>Назначение приложения пользователю — портал

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
1. Выберите **Все службы**, в текстовом поле введите Azure Active Directory, а затем нажмите клавишу **ВВОД**.
1. Выберите **Корпоративные приложения**.
1. В области **корпоративные приложения — все приложения** отображается список приложений, которыми можно управлять. Выберите приложение.
1. На панели ***AppName*** (то есть в области с именем выбранного приложения в заголовке) выберите **Пользователи & группы**.
1. В области ***AppName*** **— Пользователи и группы** выберите **Добавить пользователя**.
1. На панели **Добавление назначения** выберите **Пользователи и группы**.

   ![Назначение приложению пользователя или группы](./media/assign-user-or-group-access-portal/assign-users.png)

1. В области **Пользователи и группы** выберите одного или нескольких пользователей или групп из списка, а затем нажмите кнопку **выбрать** в нижней части панели.
1. На панели **Добавление назначения** выберите **роль**. Затем на панели **Выбор роли** выберите роль, которая будет применяться к выбранным пользователям или группам, а затем нажмите кнопку **ОК** в нижней части панели.
1. На панели **Добавление назначения** нажмите кнопку **назначить** в нижней части панели. У назначенных пользователей и групп будут разрешения, определенные выбранной ролью для этого корпоративного приложения.

## <a name="allow-all-users-to-access-an-app---portal"></a>Предоставление всем пользователям доступа к приложению — портал

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
1. Выберите **Все службы**, в текстовом поле введите Azure Active Directory, а затем нажмите клавишу **ВВОД**.
1. Выберите **Корпоративные приложения**.
1. В области **Корпоративные приложения** выберите **All applications** (Все приложения). Отобразится список приложений, которыми вы можете управлять.
1. В области **Корпоративные приложения — All applications (Все приложения)** выберите приложение.
1. На панели ***AppName*** выберите **свойства**.
1. В области  ***AppName* — свойства** укажите **требуемое назначение пользователя?** значение **нет**.

Параметр **Требуется назначение пользователей**:

- Если этот параметр имеет значение Да, пользователи сначала должны быть назначены этому приложению, прежде чем он сможет получить к нему доступ.
- Если этот параметр имеет значение нет, доступ будет предоставлен всем пользователям, которые обращаются к URL-адресу прямой ссылки приложения или URL-адресу приложения.
- Не влияет на отображение приложения на панели доступа к приложениям. Чтобы отобразить приложение на панели доступа, необходимо назначить приложение соответствующим пользователям или группе.
- Только функции облачных приложений, настроенных для единого входа SAML, приложения прокси приложения, использующие Azure Active Directory предварительной проверки подлинности или приложения, созданные непосредственно на платформе приложений Azure AD, в которой используется OAuth 2,0/ OpenID Connect Connect Authentication после того, как пользователь или администратор согласились с этим приложением. См. дополнительные сведения о [едином входе для приложений](what-is-single-sign-on.md). См. дополнительные сведения о [настройки способа предоставления согласия для приложения пользователями](configure-user-consent.md).
- Этот параметр не действует, если приложение настроено для любого другого режима единого входа.

## <a name="assign-a-user-to-an-app---powershell"></a>Назначение приложения пользователю — PowerShell

1. Откройте окно Windows PowerShell с повышенными привилегиями.

   > [!NOTE]
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

   ![Показывает роли, доступные пользователю с ролью аналитики рабочей области](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

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

- [Просмотр всех моих групп](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Удаление назначения пользователя или группы из корпоративного приложения](remove-user-or-group-access-portal.md)
- [Отключение входа пользователя в корпоративное приложение](disable-user-sign-in-portal.md)
- [Изменение имени или логотипа корпоративного приложения](change-name-or-logo-portal.md)
