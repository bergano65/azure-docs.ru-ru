---
title: Требования к данным Azure AD SSPR — Azure Active Directory
description: Требования к данным для самостоятельного сброса пароля Azure AD и информация о том, как их выполнить
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a14338e552250ac63c344365099a16f20616ea9a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964040"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Развертывание сброса пароля без регистрации пользователя

Для развертывания функции самостоятельного сброса пароля (SSPR) в Azure Active Directory (Azure AD) нужно указать данные проверки подлинности. В некоторых организациях пользователи вводят данные проверки подлинности самостоятельно. Другие организации предпочитают выполнять синхронизацию с данными, которые уже существуют в Active Directory. Эти синхронизированные данные становятся доступными для Azure AD и SSPR без вмешательства пользователя, если вы отвечаете следующим требованиям.

* правильно отформатировать данные в локальном каталоге;
* настроить [Azure AD Connect, используя стандартные параметры](../hybrid/how-to-connect-install-express.md).

Для правильной работы номера телефонов должны быть указаны в формате *+код_страны номер_телефона*. Например, +1 4255551234.

> [!NOTE]
> Между кодом страны и номером телефона должен быть пробел.
>
> Функция сброса пароля не поддерживает добавочные номера. Даже добавочные номера в формате +1 4255551234X12345 будут удаляться.

## <a name="fields-populated"></a>Заполненные поля

Если вы используете параметры по умолчанию, в Azure AD Connect выполняются следующие сопоставления.

| Локальная служба Active Directory | Azure AD |
| --- | --- |
| TelephoneNumber | Рабочий телефон |
| mobile | Мобильный телефон |

После того как пользователь проверит свой номер мобильного телефона, в поле *Телефон* в разделе **Контактная информация для проверки подлинности** в Azure AD также заносится этот номер.

## <a name="authentication-contact-info"></a>Контактная информация для проверки подлинности

На странице **методы проверки подлинности** для пользователя Azure AD в портал Azure глобальный администратор может вручную задать контактную информацию для проверки подлинности, как показано на следующем примере снимка экрана:

![Контактная информация для проверки подлинности пользователя в Azure AD][Contact]

* Если поле " **Телефон** " заполнено и в политике SSPR включен **мобильный телефон** , он увидит это число на странице регистрации сброса пароля и в ходе рабочего процесса сброса пароля.
* Поле **дополнительного телефона** не используется для сброса пароля.
* Если поле **электронной почты** заполнено и в политике SSPR включена **Электронная почта** , пользователь увидит это сообщение на странице регистрации сброса пароля и в ходе рабочего процесса сброса пароля.
* Если поле " **дополнительный адрес электронной почты** " заполнено и в политике SSPR включена **Электронная почта** , пользователь **не** увидит это сообщение на странице регистрации сброса пароля, но он увидит его во время рабочего процесса сброса пароля.

## <a name="security-questions-and-answers"></a>Контрольные вопросы и ответы на них

Контрольные вопросы и ответы на них надежно хранятся в клиенте Azure AD. Эти данные доступны пользователям только на [портале регистрации SSPR](https://aka.ms/ssprsetup). Администраторы не могут видеть, настраивать или изменять содержимое вопросов и ответов других пользователей.

## <a name="what-happens-when-a-user-registers"></a>Что происходит, когда пользователь проходит регистрацию?

Когда пользователь регистрируется, на странице регистрации будут заполнены следующие поля:

* **Телефон для проверки подлинности**;
* **Адрес электронной почты для проверки подлинности**;
* **Security Questions and Answers** (Контрольные вопросы и ответы на них).

Если вы указали значения для полей **Мобильный телефон** или **Запасной адрес электронной почты**, пользователи могут использовать их для сброса паролей, даже если они еще не прошли регистрацию в службе. Кроме того, эти значения будут отображаться для пользователей при первой регистрации, и они смогут изменить их при необходимости. После успешной регистрации эти значения сохраняются в полях **Телефон для проверки** подлинности и **адрес электронной почты для проверки подлинности** соответственно.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Установка и чтение данных проверки подлинности с помощью PowerShell

С помощью PowerShell можно заполнить следующие поля:

* **Запасной адрес электронной почты**;
* **Мобильный телефон**;
* **Рабочий телефон** — его можно указать, только если это значение не синхронизируется с локальным каталогом.

### <a name="use-powershell-version-1"></a>Использование PowerShell версии 1

Чтобы начать работу, необходимо [скачать и установить модуль Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). После его установки вы можете выполнить следующие шаги по настройке каждого поля.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Настройка данных проверки подлинности с помощью PowerShell версии 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Считывание данных проверки подлинности с помощью PowerShell версии 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Считывание параметров "Телефон для проверки подлинности" и "Адрес электронной почты для проверки подлинности"

Для считывания **телефона для проверки подлинности** и **адреса электронной почты для проверки подлинности** при использовании PowerShell версии 1 выполните следующие команды:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Использование PowerShell версии 2

Чтобы начать работу, необходимо [скачать и установить модуль Azure AD PowerShell версии 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). После его установки вы можете выполнить следующие шаги по настройке каждого поля.

Чтобы быстро установить одну из последних версий PowerShell, поддерживающую Install-Module, выполните приведенные ниже команды. (Первая строка проверяет, установлен ли модуль.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Настройка данных проверки подлинности с помощью PowerShell версии 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Считывание данных проверки подлинности с помощью PowerShell версии 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Дальнейшие действия

* [Как развернуть самостоятельный сброс пароля?](howto-sspr-deployment.md)
* [Сброс или изменение пароля](../user-help/active-directory-passwords-update-your-own-password.md)
* [Регистрация для самостоятельного сброса пароля](../user-help/active-directory-passwords-reset-register.md)
* [Требования к лицензированию самостоятельного сброса пароля в Azure AD](concept-sspr-licensing.md)
* [Доступные пользователям методы проверки подлинности](concept-sspr-howitworks.md#authentication-methods)
* [Параметры политики для SSPR](concept-sspr-policy.md)
* [Что такое обратная запись паролей и каково ее назначение](howto-sspr-writeback.md)
* [Как сообщать о действиях в SSPR](howto-sspr-reporting.md)
* [Обзор всех параметров SSPR и их значение](concept-sspr-howitworks.md)
* [Думаю, что что-то не так. Разделы справки устранить неполадки SSPR?](active-directory-passwords-troubleshoot.md)
* [Вопросы, не вошедшие в другие статьи](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Глобальные администраторы могут изменять контактные данные для аутентификации пользователя"
