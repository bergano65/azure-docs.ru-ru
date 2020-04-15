---
title: Включить мультифакторную аутентификацию для пользователей - Активный каталог Azure
description: Узнайте, как включить многофакторную аутентификацию для пользователей Azure, изменив состояние пользователя
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8ceaf13324864c7ec3df731c3e710815b0eba9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309779"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Включить мультифакторную аутентификацию для одного пользователя Azure Multi-Factor Authentication для обеспечения событий входного ввне

Существует два способа обеспечить безопасность событий, всданных пользователями, требуя многофакторной аутентификации в Azure AD. Первый и предпочтительный вариант заключается в настройке политики условного доступа, которая требует многофакторной аутентификации при определенных условиях. Второй вариант заключается в том, чтобы включить каждого пользователя для Azure Multi-фактор аутентификации. Когда пользователи включены по отдельности, они выполняют многофакторную аутентификацию каждый раз, когда они входят в систему (за некоторыми исключениями, например, при входе с доверенных IP-адресов или при включении функции _запоминированных устройств)._

> [!NOTE]
> Рекомендуемый подход рекомендуется включить многофакторную аутентификацию Azure с использованием политик условного доступа. Изменение состояния пользователя больше не рекомендуется, если ваши лицензии не включают условный доступ, поскольку это требует от пользователей выполнять МИД каждый раз, когда они входят в систему.
>
> Для начала использования «Условного [доступа»](tutorial-enable-azure-mfa.md)см.

## <a name="azure-multi-factor-authentication-user-states"></a>Многофакторная аутентификация Azure

Учетные записи пользователей в службе Многофакторной идентификации Azure имеют три различных состояния:

> [!IMPORTANT]
> Включение многофакторной аутентификации Azure через политику условного доступа не меняет состояние пользователя. Не пугайтесь, если пользователи отображаются отключенными. Условный доступ не меняет состояние.
>
> **Вы не должны включать или применять пользователей, если вы используете политики условного доступа.**

| Состояние | Описание | Затронутые приложения, не использующие браузер | Затронутые приложения, использующие браузер | Затронутая современная аутентификация |
|:---:| --- |:---:|:--:|:--:|
| Выключено | Состояние по умолчанию для нового пользователя, не зарегистрированного в Azure Multi-Factor Authentication. | нет | нет | нет |
| Активировано | Пользователь был зарегистрирован в Azure Multi-Factor Authentication, но не зарегистрировался. Ему будет предложено зарегистрироваться при следующем входе в систему. | Нет.  Они будут продолжать работать, пока не завершится регистрация. | Да. После истечения срока действия сеанса требуется регистрация многофакторной аутентификации Azure.| Да. После истечения срока действия маркера доступа требуется регистрация многофакторной аутентификации Azure. |
| Принудительно | Пользователь был зарегистрирован и завершил процесс регистрации azure Multi-Factor Authentication. | Да. Для приложений нужны пароли приложений. | Да. Многофакторная аутентификация Azure необходима при входе в систему. | Да. Многофакторная аутентификация Azure необходима при входе в систему. |

Состояние пользователя отражает, записал ли его админ в Azure Multi-Factor Authentication и завершил ли он процесс регистрации.

Все пользователи начинают с состояния *Отключено*. При регистрации пользователей в Azure Multi-Factor Authentication их состояние изменяется в *Enabled.* После включения пользователи входят в систему и завершают регистрацию, затем их состояние меняется на *Enforced* (Принудительно).

> [!NOTE]
> Если МИД повторно включен на объект пользователя, который уже имеет регистрационные данные, такие как телефон или электронная почта, то администраторы должны иметь, что пользователь перерегистрирует МИД через портал Azure или PowerShell. Если пользователь не перерегистрируется, его состояние МИД не переходит от *Включенного* к *принудительному* в пользовательском интерфейсе управления МИД.

## <a name="view-the-status-for-a-user"></a>Просмотр состояния пользователя

Используйте следующие действия для доступа к странице портала Azure, где можно просматривать состояния пользователей и управлять ими:

1. Вопиюсь на [портал Azure](https://portal.azure.com) в качестве администратора.
1. Поиск и выберите *Активный каталог Azure,* а затем выберите **пользователей** > **Все пользователи.**
1. Выберите **многофакторную аутентификацию**. Возможно, вам придется прокрутить вправо, чтобы увидеть этот вариант меню. Выберите скриншот приведенного ниже примера, чтобы увидеть полное окно портала Azure и расположение меню:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Выберите многофакторную аутентификацию из окна пользователей в Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Открывается новая страница, отображая состояние пользователя, как показано в следующем примере.
   ![Скриншот, отображение например, информации о состоянии пользователя для многофакторной аутентификации Azure](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Изменение состояния пользователя

Чтобы изменить состояние многофакторной аутентификации Azure для пользователя, выполните следующие действия:

1. Выполните шаги выше, чтобы перейти на страницу **пользователей** MFA.
1. Найдите пользователя, который вы хотите включить для многофакторной аутентификации Azure. Возможно, потребуется изменить представление в верхней части для **пользователей.**
   ![Выберите пользователя для изменения статуса из вкладки пользователя](./media/howto-mfa-userstates/enable1.png)
1. Проверьте поле рядом с именем (ы) пользователя (ы) для изменения состояния.
1. С правой стороны, под **быстрыми шагами,** выберите **Enable** или **Отключить.** В следующем примере пользователь *John Smith* имеет чек рядом с их ![именем и включается для использования: Включить выбранный пользователь, нажав Enable на меню быстрых шагов](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Включенные* пользователи автоматически переключаются на *принудительное* при регистрации для многофакторной аутентификации Azure. Не изменяйте состояние пользователя вручную на *принудительное.*

1. Подтвердите свой выбор во всплывающем окне, которое откроется.

После включения уведомите пользователей по электронной почте. Сообщите пользователям, что отображается запрос, чтобы попросить их зарегистрироваться при входе в систему. Кроме того, если ваша организация использует небраузерные приложения, не поддерживающие современную аутентификацию, потребуется создать пароли приложений. Для получения дополнительной информации ознакомьтесь с [руководством для конечных пользователей Azure Multi-Factor Authentication,](../user-help/multi-factor-authentication-end-user.md) чтобы помочь им начать работу.

## <a name="change-state-using-powershell"></a>Изменение состояния с помощью PowerShell

Чтобы изменить состояние пользователя с помощью [Azure AD PowerShell,](/powershell/azure/overview)вы измените `$st.State` параметр для учетной записи пользователя. Есть три возможных состояния для учетной записи пользователя:

* *Активировано*
* *Принудительно*
* *Выключено*  

Не переводите пользователей непосредственно в состояние *Применено*. Если вы сделаете это, приложения, не связанные с браузером, перестанут работать, потому что пользователь не прошел регистрацию azure Multi-Factor Authentication и получил [пароль приложения.](howto-mfa-mfasettings.md#app-passwords)

Чтобы начать работу, установите модуль *MSOnline* с помощью [Install-Module](/powershell/module/powershellget/install-module) следующим образом:

```PowerShell
Install-Module MSOnline
```

Далее, подключите с помощью [Connect-MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

Следующий пример скрипт PowerShell позволяет МИД *bsimon@contoso.com*для отдельного пользователя по имени:

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

PowerShell — удобный инструмент для массового включения многофакторной проверки подлинности для пользователей. Следующий сценарий циклов через список пользователей и позволяет МИД на их счетах. Определите учетные записи пользователя, `$users` установить его в первой строке для следующего:

   ```PowerShell
   # Define your list of users to update state in bulk
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

Чтобы отключить МИД, следующий пример получает пользователь с [Get-MsolUser](/powershell/module/msonline/get-msoluser), а затем удаляет любые *StrongAuthenticationRequirements набор* для определенного пользователя с помощью [Set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Вы также можете напрямую отключить МИД для пользователя, использующего [Set-MsolUser](/powershell/module/msonline/set-msoluser) следующим образом:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Преобразование пользователей из МИД на одного пользователя в МИД на основе условного доступа

Следующие PowerShell может помочь вам в преобразовании в условный доступ на основе Azure Multi-фактораутации.

```PowerShell
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

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Недавно мы изменили поведение и этот сценарий PowerShell. Ранее скрипт спас от методов МИД, отключил МИД и восстановил методы. Это больше не является необходимым теперь, когда поведение по умолчанию для оттачивать не очищает методы.
>
> Если МИД повторно включен на объект пользователя, который уже имеет регистрационные данные, такие как телефон или электронная почта, то администраторы должны иметь, что пользователь перерегистрирует МИД через портал Azure или PowerShell. Если пользователь не перерегистрируется, его состояние МИД не переходит от *Включенного* к *принудительному* в пользовательском интерфейсе управления МИД.

## <a name="next-steps"></a>Следующие шаги

Для настройки параметров многофакторной аутентификации Azure, таких как доверенные I-центры, пользовательские голосовые сообщения и оповещения о мошенничестве, [см.](howto-mfa-mfasettings.md) Для управления настройками пользователей для многофакторной аутентификации Azure [см. Управление настройками пользователей с помощью многофакторной аутентификации Azure.](howto-mfa-userdevicesettings.md)

Чтобы понять, почему пользователю было предложено [Azure Multi-Factor Authentication reports](howto-mfa-reporting.md#azure-ad-sign-ins-report)или не было предложено выполнить МИД, см.
