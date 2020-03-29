---
title: Назначить пользователю или группе корпоративное приложение в Azure AD
description: Узнайте, как выбрать корпоративное приложение и назначить для него пользователя или группу в Azure Active Directory.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186e36e4625a60362c54972b16b53f0f3e6753fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409198"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Назначение пользователя или группы корпоративному приложению в Azure Active Directory

В этой статье показано, как присваивать пользователям или группам корпоративные приложения в Active Directory Azure (Azure AD) либо из портала Azure, либо с помощью PowerShell. При присвоении пользователю приложения приложение появляется в [панели доступа my Apps](https://myapps.microsoft.com/) пользователя для легкого доступа. Если приложение предоставляет роли, можно также назначить определенную роль пользователю.

Для усиления контроля некоторые типы корпоративных приложений могут быть настроены таким образом, чтобы [требовать назначения пользователя.](#configure-an-application-to-require-user-assignment) 

Чтобы [назначить пользователя или группу корпоративному приложению,](#assign-users-or-groups-to-an-app-via-the-azure-portal)необходимо войти в систему в качестве глобального администратора, администратора приложений, администратора облачных приложений или назначенного владельца приложения предприятия.

> [!NOTE]
> Групповое назначение требует Azure Active Directory Premium P1 или P2. Групповое назначение поддерживается только для групп безопасности. Членство в nested группе и группах Office 365 в настоящее время не поддерживается. Дополнительные требования к лицензированию для функций, обсуждаемых в этой статье, можно узнать на [странице ценообразования Azure Active Directory.](https://azure.microsoft.com/pricing/details/active-directory) 

## <a name="configure-an-application-to-require-user-assignment"></a>Настройка приложения для запроса назначения пользователя

При следующих типах приложений у вас есть возможность требовать от пользователей назначения приложения, прежде чем они смогут получить к нему доступ:

- Приложения, настроенные для федеративного единого входного (SSO) с проверкой подлинности на основе SAML
- Приложения Прокси-приложений, которые используют Azure Active Directory Pre-Authentication
- Приложения, построенные на платформе приложения Azure AD, которые используют OAuth 2.0 / OpenID Connect Authentication после того, как пользователь или админ дали согласие на это приложение.

Когда требуется назначение пользователя, только те пользователи, которые вы явно назначаете в приложение, смогут войти в систему. Они могут получить доступ к приложению на своей странице My Apps или с помощью прямой ссылки. 

Когда назначение *не требуется,* либо потому, что вы установили эту опцию **нет** или потому, что приложение использует другой режим SSO, любой пользователь сможет получить доступ к приложению, если у него есть прямая ссылка на приложение или **URL-адрес пользовательского доступа** на странице **свойств** приложения. 

Эта настройка не влияет на то, отображается ли приложение на панели доступа My Apps. Приложения отображаются на панели доступа пользователей My Apps после того, как вы назначили пользователю или группу приложения. Для фона [см. Управление доступом к приложениям.](what-is-access-management.md)


Для получения запроса пользователя для приложения:

1. Войти на [портал Azure](https://portal.azure.com) с учетной записью администратора или в качестве владельца приложения.

2. Выберите **активный каталог Azure**. В левом меню навигации выберите **приложения Enterprise.**

3. Выберите приложение из списка. Если вы не видите приложение, начните вводить его имя в поле поиска. Или используйте элементы управления фильтром, чтобы выбрать тип приложения, статус или видимость, а затем выберите **Apply.**

4. В левом меню навигации выберите **Свойства**.

5. Убедитесь, что **необходимо назначение пользователя?** **Yes**

   > [!NOTE]
   > Если **требуется назначение пользователя?** переключение недоступно, вы можете использовать PowerShell для установки свойства appRoleAssignmentRequired на главном сервисе.

6. Выберите кнопку **«Сохранить»** в верхней части экрана.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Назначить пользователей или групп ы приложение через портал Azure

1. Вопиюсь на [портал Azure](https://portal.azure.com) с глобальным администратором, администратором приложений или учетной записью администратора облачных приложений или в качестве назначенного владельца корпоративного приложения.
2. Выберите **активный каталог Azure**. В левом меню навигации выберите **приложения Enterprise.**
3. Выберите приложение из списка. Если вы не видите приложение, начните вводить его имя в поле поиска. Или используйте элементы управления фильтром, чтобы выбрать тип приложения, статус или видимость, а затем выберите **Apply.**
4. В левом меню навигации выберите **пользователей и группы.**
   > [!NOTE]
   > Если вы хотите назначить пользователей приложениями Майкрософт, таким как приложения Office 365, некоторые из этих приложений используют PowerShell. 
5. Нажмите кнопку **Добавить пользователя**.
6. На панели **добавления назначения** выберите **пользователей и группы.**
7. Выберите пользователя или группу, которые вы хотите назначить приложению, или начните вводить имя пользователя или группы в поле поиска. Вы можете выбрать несколько пользователей и групп, и ваш выбор будет отображаться под **выбранными элементами.**
8. После завершения, нажмите **Выберите**.

   ![Назначение приложению пользователя или группы](./media/assign-user-or-group-access-portal/assign-users.png)

9. На **панели пользователей и групп** выберите одного или нескольких пользователей или групп из списка, а затем выберите кнопку **«Выберите»** в нижней части панели.
10. Если приложение поддерживает его, можно назначить роль пользователю или группе. На панели **добавления назначения** выберите **«Выбрать роль».** Затем, на панели **Select Role,** выберите роль для применения к выбранным пользователям или группам, а затем выберите **OK** в нижней части панели. 

    > [!NOTE]
    > Если приложение не поддерживает выбор ролей, назначается роль доступа по умолчанию. В этом случае приложение управляет уровнем доступа пользователей.

2. На панели **добавления назначения** выберите кнопку **«Назначить»** в нижней части панели.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Назначить пользователей или групп ы приложение через PowerShell

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

   ![Отображает роли, доступные пользователю с помощью роли аналитики рабочего места](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

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

## <a name="related-articles"></a>Связанные статьи

- [Подробнее о доступе конечных пользователей к приложениям](end-user-experiences.md)
- [Планирование развертывания панели доступа Azure AD](access-panel-deployment-plan.md)
- [Управление доступом к приложениям](what-is-access-management.md)
 
## <a name="next-steps"></a>Дальнейшие действия

- [Просмотр всех моих групп](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Удаление назначения пользователя или группы из корпоративного приложения](remove-user-or-group-access-portal.md)
- [Отключение входа пользователя в корпоративное приложение](disable-user-sign-in-portal.md)
- [Изменение имени или логотипа корпоративного приложения](change-name-or-logo-portal.md)
