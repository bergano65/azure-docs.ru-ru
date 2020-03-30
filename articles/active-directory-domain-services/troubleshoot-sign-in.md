---
title: Регистрация проблем в неполадке в службах домена Azure AD (ru) Документы Майкрософт
description: Узнайте, как устранить проблемы и ошибки в службах домена Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 0585ced3bc53f216ab203b4686b5800b5e14bbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612743"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Проблемы с устранением проблем со счетом регистрации с доменом Azure AD Domain

Наиболее распространенные причины, по которым учетная запись пользователя не может войти в управляемый домен Azure AD DS, включают следующие сценарии:

* [Учетная запись еще не синхронизирована с Azure AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [У Azure AD DS нет хэшов паролей, чтобы впустить учетную запись.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [Учетная запись заблокирована.](#the-account-is-locked-out)

> [!TIP]
> DD Azure AD не может синхронизировать сярворные учетные данные для учетных записей, которые являются внешними для арендатора Azure AD. Внешние пользователи не могут войти в управляемый домен Azure AD DS.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Учетная запись еще не синхронизирована с Azure AD DS

В зависимости от размера каталога в DS Azure AD может потребоваться некоторое время. Для больших каталогов эта начальная односторонней синхронизации с Azure AD может занять несколько часов, а до дня или двух. Убедитесь, что вы ждете достаточно долго, прежде чем повторить проверку подлинности.

Для гибридных сред, которые пользователь Azure AD Connect для синхронизации данных каталога в Azure AD, убедитесь, что вы запустите последнюю версию Azure AD Connect и [настроили Azure AD Connect для выполнения полной синхронизации после включения Azure AD DS.][azure-ad-connect-phs] Если вы отключите Azure AD DS, а затем повторно включить, вы должны следовать этим шагам снова.

Если у вас по-прежнему возникают проблемы с учетными записями, не синхронизирующимися с Azure AD Connect, перезапустите службу синхронизации Azure AD. С компьютера с установленным Azure AD Connect откройте окно оперативного управления и запустите следующие команды:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS не имеет хэши паролей

Пока вы не включите Azure AD DS для клиента, Azure AD не будет создавать и хранить хэши паролей в формате, требуемом для аутентификации NTLM или Kerberos. Из соображений безопасности Azure AD никогда не хранит пароли в виде открытого текста. Поэтому Azure AD не может автоматически создать хэши паролей в формате NTLM или Kerberos по уже существующим учетным данным пользователей.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Гибридные среды с персоналом синхронизации

Для гибридных сред, использующих Azure AD Connect для синхронизации с локальной средой AD DS, можно локально генерировать и синхронизировать необходимые хэши пароля NTLM или Kerberos в Azure AD. После создания домена Azure AD DS [можно синхронизировать хэш-карты с помощью служб домена Active Directory.][azure-ad-connect-phs] Не выполнив этап синхронизации хэша пароля, вы не сможете войти в учетную запись с помощью Azure AD DS. Если вы отключите Azure AD DS, а затем повторно включить, вы должны следовать этим шагам снова.

Для получения дополнительной информации см. [Как синхронизация хэша паролей работает для Azure AD DS][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Среды только для облаков без синхронизации

Управляемые доменами Azure AD DS без синхронизации, только учетные записи в Azure AD, также необходимо для создания необходимых хэшов пароля NTLM или Kerberos. Если учетная запись только для облака не может войти в систему, успешно ли завершен процесс изменения пароля для учетной записи после включения Azure AD DS?

* **Нет, пароль не был изменен.**
    * [Измените пароль для учетной записи для][enable-user-accounts] создания необходимых хэшей паролей, а затем подождите 15 минут, прежде чем вы попытаетесь войти снова.
    * Если вы отключите Azure AD DS, а затем повторно включить, каждая учетная запись должна следовать шагам снова, чтобы изменить свой пароль и создать необходимые хэши паролей.
* **Да, пароль был изменен.**
    * Попробуйте войти в формат *UPN,* `driley@aaddscontoso.com`например, вместо формата `AADDSCONTOSO\deeriley` *SAMAccountName.*
    * *SAMAccountName* может быть автоматически сгенерирован для пользователей, чья префикс UPN слишком длинна или такая же, как и другой пользователь управляемого домена. Формат *UPN* гарантированно уникален в рамках ad-арендатора Azure AD.

## <a name="the-account-is-locked-out"></a>Учетная запись заблокирована

Учетная запись пользователя в Azure AD DS заблокирована при соблюдении определенного порога для неудачных попыток входной связи. Такое поведение блокировки учетной записи предназначено для защиты от неоднократных попыток входной системы грубой силы, которые могут указывать на автоматизированную цифровую атаку.

По умолчанию, если есть 5 неудачных попыток пароля в течение 2 минут, учетная запись заблокирована в течение 30 минут.

Для получения дополнительной информации и путей решения проблем блокировки учетной записи смотрите [проблемы блокировки учетной записи Troubleshoot в Azure AD DS.][troubleshoot-account-lockout]

## <a name="next-steps"></a>Дальнейшие действия

Если у вас по-прежнему возникли проблемы с присоединением ВМ к домену Azure AD DS, [найдите справку и откройте билет поддержки для Active Directory Azure.][azure-ad-support]

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
