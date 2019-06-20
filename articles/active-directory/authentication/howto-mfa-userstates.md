---
title: 'Azure состояний пользователя многофакторной проверки подлинности: Azure Active Directory'
description: Узнайте подробнее о состояниях пользователей в службе Многофакторной идентификации Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a2c036265049bf4b87435e96f779482da404caa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164817"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Включение двухфакторной проверки подлинности пользователя

Двухфакторную проверку подлинности можно требовать, используя один из двух подходов. Оба эти подхода требуют использования учетной записи глобального администратора. Первый способ — включить многофакторную идентификацию Azure (MFA) для каждого отдельного пользователя. В этом случае пользователи выполняют двухфакторную проверку подлинности при каждом входе (за некоторыми исключениями, к примеру, если они входят из доверенных IP-адресов или включена функция _запоминания устройств_). Второй вариант — настроить политику условного доступа, который требует двухфакторную проверку подлинности при определенных условиях.

> [!TIP]
> Включение многофакторной идентификации Azure с помощью политик условного доступа — это рекомендуемый подход. Изменения состояния пользователя больше не рекомендуется, если лицензии не используйте условного доступа, как потребуется пользователям выполнять MFA каждый раз при входе.

## <a name="choose-how-to-enable"></a>Выбор способа включения

**Включить, изменив состояние пользователя**. Это традиционный метод применения двухфакторной проверки подлинности, который рассматривается в этой статье. Он подходит и для Azure MFA в облаке, и для сервера Azure MFA. С помощью этого метода требует, чтобы выполнить двухфакторную проверку подлинности пользователи **каждый раз** они вход и переопределяет политики условного доступа. Это метод, используемый для людей с лицензии Office 365 или Microsoft 365 бизнес, так как они не включают функции условного доступа.

Включаемые политика условного доступа — это наиболее гибкий способ включить двухфакторную проверку подлинности для пользователей. Включение с помощью политики условного доступа только работает для Azure MFA в облаке и является компонентом уровня "премиум" Azure AD. Дополнительные сведения об этом методе можно найти в статье [Приступая к работе с многофакторной идентификацией Azure в облаке](howto-mfa-getstarted.md).

Включить с помощью защиты идентификации Azure AD. Этот метод использует политику рисков защиты идентификации Azure AD, чтобы требовать двухфакторную проверку подлинности только на основе риска входа для всех облачных приложений. Этот метод требует лицензирования Azure Active Directory P2. Дополнительные сведения об этом методе см. в статье [Защита идентификации Azure Active Directory](../identity-protection/howto-sign-in-risk-policy.md).

> [!Note]
> Дополнительные сведения о лицензиях и ценах можно найти на страницах цен для [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) и [Многофакторной идентификации](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="enable-azure-mfa-by-changing-user-state"></a>Включение Azure MFA путем изменения состояния пользователя

Учетные записи пользователей в службе Многофакторной идентификации Azure имеют три различных состояния:

| Status | Описание | Затронутые приложения, не использующие браузер | Затронутые приложения, использующие браузер | Затронутая современная аутентификация |
|:---:|:---:|:---:|:--:|:--:|
| Отключено |Состояние по умолчанию для нового пользователя, не зарегистрированного в Azure MFA. |Нет |Нет |Нет |
| Enabled |Пользователь указан в Azure MFA, но не зарегистрирован. Ему будет предложено зарегистрироваться при следующем входе в систему. |№  Они будут продолжать работать, пока не завершится регистрация. | Да. После окончания сеанса требуется регистрация в Azure MFA.| Да. После окончания срока действия маркера доступа требуется регистрация в Azure MFA. |
| Принудительно |Пользователь указан и зарегистрирован для использования Azure MFA. |Да. Для приложений нужны пароли приложений. |Да. Аутентификация в Azure MFA требуется при входе в систему. | Да. Аутентификация в Azure MFA требуется при входе в систему. |

Состояние пользователя отражает, указал ли администратор его в Azure MFA и завершил ли пользователь процесс регистрации.

Все пользователи начинают с состояния *Отключено*. При указании пользователей в Azure MFA их состояние меняется на *Включено*. После включения пользователи входят в систему и завершают регистрацию, затем их состояние меняется на *Enforced* (Принудительно).  

### <a name="view-the-status-for-a-user"></a>Просмотр состояния пользователя

Чтобы открыть страницу, на которой можно просмотреть состояние пользователей и управлять им, выполните следующее.

1. Войдите на [портал Azure](https://portal.azure.com) с использованием учетной записи администратора.
2. Последовательно выберите **Azure Active Directory** > **Пользователи и группы** > **Все пользователи**.
3. Выберите **Многофакторная идентификация**.
   ![Выберите Многофакторную идентификации](./media/howto-mfa-userstates/selectmfa.png)
4. Откроется новая страница, где будет показано состояние пользователя.
   ![Состояние пользователя Многофакторной идентификации (снимок экрана)](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Изменение состояния пользователя

1. Выполните шаги выше, чтобы перейти на страницу **пользователей** MFA.
2. Найдите пользователя, для которого требуется включить Azure MFA. Возможно, потребуется изменить представление в верхней части страницы.
   ![Выберите пользователя, чтобы изменить состояние на вкладке "пользователи"](./media/howto-mfa-userstates/enable1.png)
3. Установите флажок рядом с именем пользователя.
4. Справа, в разделе **быстрых действий**, щелкните **Включить** или **Отключить**.
   ![Включить выбранного пользователя, нажав кнопку "Включить" в меню быстрых действий](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Пользователи с состоянием *Включено* автоматически перейдут в состояние *Enforced* (Принудительно) при регистрации в службе Azure MFA. Не изменяйте состояние пользователя вручную на *Enforced* (Принудительно).

5. Подтвердите свой выбор во всплывающем окне, которое откроется.

После включения уведомите пользователей по электронной почте. Сообщите, что им будет предложено зарегистрироваться при следующем входе. Кроме того, если ваша организация использует небраузерные приложения, не поддерживающие современную аутентификацию, потребуется создать пароли приложений. Можно также добавить ссылку на [руководство для пользователей Azure MFA](../user-help/multi-factor-authentication-end-user.md), чтобы им было проще приступить к работе.

### <a name="use-powershell"></a>Использование PowerShell

Чтобы изменить состояние пользователя с помощью [Azure AD PowerShell](/powershell/azure/overview), измените `$st.State`. Существуют три возможных состояния:

* Enabled
* Принудительно
* Отключено  

Не переводите пользователей непосредственно в состояние *Применено*. Если сделать это, приложения, не использующие браузер, перестанут работать, так как пользователь не прошел регистрацию в Azure MFA и не получил [пароль приложения](howto-mfa-mfasettings.md#app-passwords).

Сначала установите модуль с помощью:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Не забудьте сначала подключиться, используя **Connect-MsolService**

В этом примере скрипта PowerShell включается многофакторная проверка подлинности для отдельного пользователя:

   ```PowerShell
   Import-Module MSOnline
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

PowerShell — удобный инструмент для массового включения многофакторной проверки подлинности для пользователей. Например следующий скрипт обрабатывает список пользователей и включает многофакторную проверку подлинности для их учетных записей:

   ```PowerShell
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Чтобы отключить многофакторную проверку подлинности, используйте следующий скрипт:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationMethods @()
   ```

Можете также использовать более короткий вариант скрипта:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Convert пользователей из многофакторной проверки Подлинности пользователя для условного доступа на основе многофакторной проверки Подлинности

Следующая команда PowerShell можно поможет выполнить преобразование в условного доступа на основе Azure многофакторной проверки подлинности.

```PowerShell
# Disable MFA for all users, keeping their MFA methods intact
Get-MsolUser -All | Disable-MFA -KeepMethods

# Enforce MFA for all users
Get-MsolUser -All | Set-MfaState -State Enforced

# Wrapper to disable MFA with the option to keep the MFA
# methods (to avoid having to proof-up again later)
function Disable-Mfa {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$True)]
        $User,
        [switch] $KeepMethods
    )

    Process {

        Write-Verbose ("Disabling MFA for user '{0}'" -f $User.UserPrincipalName)
        $User | Set-MfaState -State Disabled

        if ($KeepMethods) {
            # Restore the MFA methods which got cleared when disabling MFA
            Set-MsolUser -ObjectId $User.ObjectId `
                         -StrongAuthenticationMethods $User.StrongAuthenticationMethods
        }
    }
}

# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

```

## <a name="next-steps"></a>Дальнейшие действия

* Почему пользователю было предложено или не предложено выполнить MFA? Ознакомьтесь с разделом [Отчет по действиям входа Azure AD](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Чтобы настроить дополнительные параметры, например надежные IP-адреса, пользовательские голосовые сообщения и предупреждения о мошенничестве, см. статью [Настройка параметров Многофакторной идентификации Azure](howto-mfa-mfasettings.md).
* Сведения об управлении пользовательскими параметрами Многофакторной идентификации Azure можно найти в статье [Управление параметрами пользователей с помощью Многофакторной идентификации Azure в облаке](howto-mfa-userdevicesettings.md).
