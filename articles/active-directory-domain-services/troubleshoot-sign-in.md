---
title: Устранение неполадок при входе в доменные службы Azure AD | Документация Майкрософт
description: Узнайте, как устранять общие проблемы и ошибки входа пользователей в доменных службах Azure Active Directory.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: aa03e388019bf696324ea7af6062ec98386df5fa
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827054"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Устранение неполадок при входе в систему с помощью управляемого домена доменных служб Azure AD

Наиболее распространенными причинами для учетной записи пользователя, которая не может войти в управляемый домен AD DS Azure, являются следующие сценарии.

* [Учетная запись еще не синхронизирована с Azure AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [AD DS Azure не имеет хэшей паролей, чтобы разрешить вход в учетную запись.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [Учетная запись заблокирована.](#the-account-is-locked-out)

> [!TIP]
> AD DS Azure не может синхронизировать учетные данные для учетных записей, которые являются внешними для клиента Azure AD. Внешние пользователи не могут войти в управляемый домен AD DS Azure.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Учетная запись еще не синхронизирована с Azure AD DS

В зависимости от размера каталога может потребоваться некоторое время, чтобы учетные записи пользователей и хэши учетных данных были доступны в AD DS Azure. Для больших каталогов Эта начальная односторонняя синхронизация из Azure AD может занять несколько часов и до одного дня или двух. Убедитесь, что вы ожидаете достаточно времени, прежде чем повторять проверку подлинности.

Для гибридных сред, которые пользователь Azure AD Connect для синхронизации локальных данных каталога с Azure AD, убедитесь, что вы используете последнюю версию Azure AD Connect и [настроили Azure AD Connect для выполнения полной синхронизации после включения Azure AD DS][azure-ad-connect-phs]. Если вы отключаете AD DS Azure, а затем включаете его повторно, необходимо выполнить эти действия еще раз.

Если по-прежнему возникают проблемы с учетными записями, которые не синхронизируются с помощью Azure AD Connect, перезапустите службу Azure AD Sync. На компьютере с установленным Azure AD Connect откройте окно командной строки и выполните следующие команды:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>AD DS Azure не имеет хэшей паролей

Пока вы не включите Azure AD DS для клиента, Azure AD не будет создавать и хранить хэши паролей в формате, требуемом для аутентификации NTLM или Kerberos. Из соображений безопасности Azure AD никогда не хранит пароли в виде открытого текста. Поэтому Azure AD не может автоматически создать хэши паролей в формате NTLM или Kerberos по уже существующим учетным данным пользователей.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Гибридные среды с локальной синхронизацией

Для гибридных сред, использующих Azure AD Connect для синхронизации из локальной AD DS среды, вы можете локально создавать и синхронизировать необходимые хэши паролей NTLM или Kerberos в Azure AD. После создания управляемого домена Azure AD DS [включите синхронизацию хэшей паролей для Azure Active Directory доменных служб][azure-ad-connect-phs]. Без завершения этого шага синхронизации хэша паролей вы не сможете войти в учетную запись с помощью Azure AD DS. Если вы отключаете AD DS Azure, а затем включаете его повторно, необходимо выполнить эти действия еще раз.

Дополнительные сведения см. в статье [как работает синхронизация хэша паролей для Azure AD DS][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Облачные среды без локальной синхронизации

Azure AD DS управляемые домены без локальной синхронизации, только учетные записи в Azure AD, а также обязательное создание хэшей паролей NTLM или Kerberos. Если учетная запись, доступная только для облака, не может войти в систему, для учетной записи после включения Azure AD DS был успешно выполнен процесс изменения пароля?

* **Нет, пароль не был изменен.**
    * [Измените пароль для учетной записи][enable-user-accounts] , чтобы создать необходимые хэши паролей, а затем подождите 15 минут, прежде чем пытаться войти снова.
    * Если вы отключаете AD DS Azure, а затем включаете его повторно, каждая учетная запись должна следовать инструкциям, чтобы изменить пароль и создать необходимые хэши паролей.
* **Да, пароль изменен.**
    * Попробуйте выполнить вход с использованием формата *имени участника-пользователя* , например `driley@contoso.com`, вместо формата *SamAccountName* , такого как `CONTOSO\deeriley`.
    * *SamAccountName* может автоматически создаваться для пользователей, чей префикс имени участника-пользователя слишком длинный или совпадает с другим пользователем в управляемом домене. Формат *имени участника-пользователя* гарантированно уникален в пределах клиента Azure AD.

## <a name="the-account-is-locked-out"></a>Учетная запись заблокирована

Учетная запись пользователя в Azure AD DS заблокирована, если достигнуто заданное пороговое значение неудачных попыток входа. Это поведение блокировки учетной записи предназначено для защиты от повторных попыток входа методом подбора, которые могут указывать на автоматическую цифровую атаку.

По умолчанию при наличии 5 попыток ввода пароля в течение 2 минут учетная запись блокируется в течение 30 минут.

Дополнительные сведения и способы устранения проблем с блокировкой учетной записи см. [в статье Устранение неполадок блокировки учетных записей в Azure AD DS][troubleshoot-account-lockout].

## <a name="next-steps"></a>Дополнительная информация

Если у вас по-прежнему возникли проблемы при присоединении виртуальной машины к управляемому домену Azure AD DS, [найдите справку и откройте запрос в службу поддержки для Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
