---
title: 'Azure Active Directory: политики самостоятельного сброса пароля'
description: Настройка параметров политик самостоятельного сброса пароля в Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5af0c2ecde539c9737249e4251f7eab276affb9c
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381919"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Политики и ограничения для паролей в Azure Active Directory

В этой статье описываются политики паролей и требования к сложности, связанные с учетными записями пользователей в клиенте Azure Active Directory (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Различия политики сброса администратора

**Корпорация Майкрософт применяет строгую *двухфакторную* политику сброса пароля по умолчанию для любой роли администратора Azure**. Эта политика может отличаться от политики, которую вы определили для пользователей, и она не может быть изменена. Всегда следует тестировать функции сброса пароля от имени пользователя, которому не назначена какая-либо роль администратора Azure.

При использовании двухфакторной политики у **администраторов нет возможности применять контрольные вопросы**.

Двухфакторная политика требует ввода двух фрагментов данных для аутентификации, таких как **адрес электронной почты**, **приложение Authenticator** или **номер телефона**. Двухфакторная политика применяется в следующих случаях:

* затронуты все следующие роли администратора:
  * администратор службы технической поддержки;
  * администратор службы поддержки;
  * Администратор выставления счетов
  * Служба поддержка партнеров уровня 1
  * Служба поддержка партнеров уровня 2
  * администратор Exchange;
  * администратор Skype для бизнеса;
  * Администратор пользователей
  * создатели каталогов;
  * глобальный или корпоративный администратор;
  * администратор SharePoint;
  * администратор соответствия требованиям.
  * администратор приложений;
  * Администратор безопасности
  * администратор привилегированных ролей;
  * Intune administrator
  * администратор прокси-сервера приложений;
  * Dynamics 365 administrator
  * администратор службы Power BI.
  * Authentication administrator
  * Privileged Authentication administrator

* если прошло 30 дней с момента установки пробной подписки; или
* при наличии личного домена (например, contoso.com); или
* Azure AD Connect выполняет синхронизацию удостоверений из вашего локального каталога.

### <a name="exceptions"></a>Исключения

Однофакторная политика требует ввода одного фрагмента данных для аутентификации, такого как адрес электронной почты *или* номер телефона. Однофакторная политика применяется в следующих случаях:

* еще не прошло 30 дней с момента установки пробной подписки; или
* личный домен отсутствует (*. onmicrosoft.com); и
* Azure AD Connect не выполняет синхронизацию удостоверений.

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Политики UserPrincipalName, которые применяются ко всем учетным записям пользователей

Все учетные записи пользователей, используемые для входа в Azure AD, должны иметь уникальное значение атрибута имени субъекта-пользователя (UPN), связанное с этой учетной записью. В следующей таблице описываются политики, которые применяются к локальным учетным записям пользователей Active Directory, которые синхронизируются с облаком, и к облачным учетным записям пользователей.

| Свойство | Требования UserPrincipalName |
| --- | --- |
| Допустимые символы |<ul> <li>A–Z</li> <li>a–z</li><li>0–9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Недопустимые символы |<ul> <li>Любой знак \@\", который не отделяет имя пользователя от домена.</li> <li>Не может содержать знак точки "." непосредственно перед знаком \@\".</li></ul> |
| Ограничения длины |<ul> <li>Общая длина не должна превышать 113 знаков.</li><li>Перед знаком \@\" может быть до 64 знаков.</li><li>После знака \@\" может быть до 48 знаков.</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Политики паролей, которые применяются только к облачным учетным записям пользователей.

The following table describes the password policy settings applied to user accounts that are created and managed in Azure AD:

| Свойство | Требования |
| --- | --- |
| Допустимые символы |<ul><li>A–Z</li><li>a–z</li><li>0–9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / \` ~ " ( ) ;</li> <li>blank space</li></ul> |
| Недопустимые символы | Знаки Юникода. |
| Ограничения для пароля |<ul><li>A minimum of 8 characters and a maximum of 256 characters.</li><li>Необходимо выполнить 3 из 4 следующих условий:<ul><li>строчные буквы;</li><li>прописные буквы;</li><li>числа (0–9);</li><li>символы (см. ограничения для пароля выше).</li></ul></li></ul> |
| Password expiry duration (Maximum password age) |<ul><li>Значение по умолчанию: **90** дней.</li><li>Значение можно изменить с помощью командлета `Set-MsolPasswordPolicy` из модуля Azure Active Directory для Windows PowerShell.</li></ul> |
| Password expiry notification (When are users notified of password expiration) |<ul><li>Значение по умолчанию: **14** дней (до истечения срока действия пароля).</li><li>Это значение можно настроить с помощью командлета `Set-MsolPasswordPolicy`.</li></ul> |
| Password expiry (Do passwords ever expire) |<ul><li>Значение по умолчанию: **false** дней (означает, что включен срок действия пароля).</li><li>Это значение можно настроить для каждой учетной записи пользователя с помощью командлета `Set-MsolUser`.</li></ul> |
| Журнал изменения пароля | Последний пароль *невозможно* использовать повторно, когда пользователь изменяет пароль. |
| Журнал сброса пароля | Последний пароль *можно* использовать повторно, когда пользователь сбрасывает забытый пароль. |
| Блокировка учетной записи | После 10 неудачных попыток входа (с неправильным паролем) пользователь будет заблокирован на одну минуту. Последующие неудачные попытки входа приведут к блокировке пользователя на более длительное время. [Смарт-блокировка](howto-password-smart-lockout.md) отслеживает хэши последних трех попыток неправильного ввода пароля во избежание повторного увеличения счетчика блокировки для того же пароля. Если кто-то введет тот же неверный пароль несколько раз, это не вызовет блокировку учетной записи. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Установка политик срока действия пароля в Azure AD

A global administrator or user administrator for a Microsoft cloud service can use the Microsoft Azure AD Module for Windows PowerShell to set user passwords not to expire. Можно также использовать командлеты Windows PowerShell, чтобы удалить бессрочную конфигурацию или просмотреть, какие пароли пользователей имеют неограниченный срок действия. 

Эти указания применимы к другим поставщикам, таким как Intune и Office 365, которые используют Azure AD для служб идентификации и каталогов. Срок действия пароля — это единственное, что может быть изменено в политике.

> [!NOTE]
> Неограниченный срок действия можно настроить только для паролей учетных записей пользователей, которые не синхронизируются в процессе синхронизации каталогов. Дополнительные сведения о синхронизации каталогов см. в статье [Интеграция локальных каталогов с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Задание или проверка политик пароля с помощью PowerShell

Чтобы начать работу, необходимо [скачать и установить модуль Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). После его установки вы можете выполнить следующие процедуры по настройке каждого поля.

### <a name="check-the-expiration-policy-for-a-password"></a>Проверка политики срока действия пароля

1. Connect to Windows PowerShell by using your user administrator or company administrator credentials.
1. Выполните следующие команды:

   * To see if a single user’s password is set to never expire, run the following cmdlet by using the UPN (for example, *aprilr\@contoso.onmicrosoft.com*) or the user ID of the user you want to check:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * To see the **Password never expires** setting for all users, run the following cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Задание срока действия пароля

1. Connect to Windows PowerShell by using your user administrator or company administrator credentials.
1. Выполните следующие команды:

   * To set the password of one user so that the password expires, run the following cmdlet by using the UPN or the user ID of the user:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * To set the passwords of all users in the organization so that they expire, use the following cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Установка бессрочного пароля

1. Connect to Windows PowerShell by using your user administrator or company administrator credentials.
1. Выполните следующие команды:

   * To set the password of one user to never expire, run the following cmdlet by using the UPN or the user ID of the user:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * To set the passwords of all the users in an organization to never expire, run the following cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Пароль, для которого задано `-PasswordPolicies DisablePasswordExpiration`, по-прежнему имеет срок использования, определяемый атрибутом `pwdLastSet`. Если задать для пользователей бессрочные пароли, то по прошествии 90 дней их срок действия истечет. В зависимости от атрибута `pwdLastSet`, если задать срок действия, указав `-PasswordPolicies None`, то всем пользователям, у паролей которых значение `pwdLastSet` превышает 90 дней, будет необходимо сменить пароль при следующем входе. Это изменение может повлиять на большое количество пользователей.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о сбросе пароля с помощью Azure AD см. в следующих статьях:

* [Как развернуть самостоятельный сброс пароля?](howto-sspr-deployment.md)
* [Сброс или изменение пароля](../user-help/active-directory-passwords-update-your-own-password.md).
* [Регистрация для самостоятельного сброса пароля](../user-help/active-directory-passwords-reset-register.md).
* [Требования к лицензированию самостоятельного сброса пароля в Azure AD](concept-sspr-licensing.md)
* [Какие данные используются для SSPR и какие сведения нужно указывать для пользователей](howto-sspr-authenticationdata.md)
* [Доступные пользователям методы проверки подлинности](concept-sspr-howitworks.md#authentication-methods)
* [Что такое обратная запись паролей и каково ее назначение](howto-sspr-writeback.md)
* [Как сообщать о действиях в SSPR](howto-sspr-reporting.md)
* [Обзор всех параметров SSPR и их значение](concept-sspr-howitworks.md)
* [I think something is broken. How do I troubleshoot SSPR?](active-directory-passwords-troubleshoot.md)
* [Вопросы, не вошедшие в другие статьи](active-directory-passwords-faq.md)
