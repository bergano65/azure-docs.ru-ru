---
title: Требования к данным Azure AD SSPR - Активный каталог Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74964040"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Развертывание сброса пароля без регистрации пользователя

Для развертывания функции самостоятельного сброса пароля (SSPR) в Azure Active Directory (Azure AD) нужно указать данные проверки подлинности. В некоторых организациях пользователи вводят данные проверки подлинности самостоятельно. Другие организации предпочитают синхронизировать с данными, которые уже существуют в Active Directory. Эти синхронизированные данные доступны Azure AD и SSPR без необходимости взаимодействия с пользователем, если вы отвечаете следующим требованиям:

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

После того, как пользователь проверяет свой номер мобильного телефона, поле *телефона* под **контактной информацией аутентификации** в Azure AD также заполняется этим номером.

## <a name="authentication-contact-info"></a>Контактная информация для проверки подлинности

На странице **«Методы аутентификации»** для пользователя Azure AD на портале Azure Глобальный администратор может вручную настроить контактную информацию аутентификации, как показано на следующем примере скриншота:

![Контактная информация о проверке подлинности пользователя в Azure AD][Contact]

* Если поле **телефона** заполнено и **мобильный телефон** включен в политике SSPR, пользователь видит этот номер на странице регистрации сбросил пароля и во время рабочего процесса сбросила пароля.
* **Поле Альтернативного телефона** не используется для сбоя пароля.
* Если поле **электронной почты** заполнено и **электронная почта** включена в политике SSPR, пользователь видит эту электронную почту на странице регистрации сбросил аролей и во время рабочего процесса сбросила пароля.
* Если поле **альтернативной электронной почты** заполнено и **электронная почта** включена в политике SSPR, пользователь **не увидит** эту электронную почту на странице регистрации сбросил пароля, но он будет видеться во время рабочего процесса сбросил аролей.

## <a name="security-questions-and-answers"></a>Контрольные вопросы и ответы на них

Контрольные вопросы и ответы на них надежно хранятся в клиенте Azure AD. Эти данные доступны пользователям только на [портале регистрации SSPR](https://aka.ms/ssprsetup). Администраторы не могут видеть, настраивать или изменять содержимое вопросов и ответов других пользователей.

## <a name="what-happens-when-a-user-registers"></a>Что происходит, когда пользователь проходит регистрацию?

Когда пользователь регистрируется, на странице регистрации будут заполнены следующие поля:

* **Телефон аутентификации**
* **Адрес электронной почты для проверки подлинности**
* **Контрольные вопросы и ответы на них**

Если вы предоставили значение для **мобильного телефона** или **альтернативной электронной почты,** пользователи могут немедленно использовать эти значения для сбросить свои пароли, даже если они не зарегистрировались для службы. Кроме того, эти значения будут отображаться для пользователей при первой регистрации, и они смогут изменить их при необходимости. После успешной регистрации эти значения сохраняются в полях **телефонной проверки** и **аутентификации,** соответственно.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Установка и чтение данных проверки подлинности с помощью PowerShell

С помощью PowerShell можно заполнить следующие поля:

* **Альтернативная электронная почта**
* **Мобильный телефон**
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
* [Сбросить или изменить пароль](../user-help/active-directory-passwords-update-your-own-password.md)
* [Регистрация для сбросить пароли самообслуживания](../user-help/active-directory-passwords-reset-register.md)
* [У вас есть вопрос лицензирования?](concept-sspr-licensing.md)
* [Доступные пользователям методы проверки подлинности](concept-sspr-howitworks.md#authentication-methods)
* [Параметры политики для SSPR](concept-sspr-policy.md)
* [Что такое обратная запись паролей и каково ее назначение](howto-sspr-writeback.md)
* [Как сообщать о действиях в SSPR](howto-sspr-reporting.md)
* [Обзор всех параметров SSPR и их значение](concept-sspr-howitworks.md)
* [Я думаю, что-то сломано. Как устранить неполадки SSPR?](active-directory-passwords-troubleshoot.md)
* [Вопросы, не вошедшие в другие статьи](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Глобальные администраторы могут изменять контактные данные для аутентификации пользователя"
