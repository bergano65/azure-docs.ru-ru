---
title: Безпадонный ключ безопасности, вскакиваемый на локтевые ресурсы (предварительный просмотр) - Активный каталог Azure
description: Узнайте, как включить доступ к ключу безопасности без паролей в доступе к ресурсам с помощью Active Directory Azure (предварительный просмотр)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b29f84931c169ffe1c2c81d5e32201cbc63fc88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942874"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Включить включение ключа безопасности без паролей в локтевые ресурсы с помощью Active Directory Azure (предварительный просмотр)

Этот документ фокусируется на предоставлении безпачной аутентификации для локтевых ресурсов для сред с **присоединенной Azure AD** и **гибридных azure AD, присоединившихся к** Windows 10. Эта функция обеспечивает бесшовные единого ввоза (SSO) для местных ресурсов с использованием microsoft-совместимых ключей безопасности.

|     |
| --- |
| Ключи безопасности FIDO2 являются общедоступной функцией предварительного просмотра Active Directory Azure. Для получения дополнительной информации о предварительных просмотрах [см.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>SSO для локтевых ресурсов с использованием ключей FIDO2

Активный каталог Azure (AD) может выдавать билеты Kerberos, предоставляющие билеты (TGT) для одного или нескольких доменов Active Directory. Эта функциональность позволяет пользователям войти в Windows с современными учетными данными, такими как ключи безопасности FIDO2, и получить доступ к традиционным ресурсам active Directory. Билеты и авторизация Kerberos по-прежнему контролируются вашими контроллерами домена Active Directory.

Объект Azure AD Kerberos Server создается в вашем предприимчивом Active Directory, а затем надежно публикуется в Active Directory Azure. Объект не связан с физическими серверами. Это просто ресурс, который может быть использован Active Directory Azure для генерации TGT Kerberos для вашего домена Active Directory.

![Получение билета (TGT) от Azure AD и AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. Пользователь вписывается в свое устройство Windows 10 с ключом безопасности FIDO2 и проверяет подлинность в Azure AD.
1. Azure AD проверяет каталог для ключа сервера Kerberos, соответствующего домену AD пользователя.
   1. Azure AD генерирует Kerberos TGT для доменa AD пользователя. TGT включает в себя только SID пользователя. Данные авторизации не включены в TGT.
1. TGT возвращается клиенту вместе с токеном Azure AD Primary Refresh (PRT).
1. Клиентская машина контактирует с контроллером домена AD и торгует частичным TGT для полностью сформированного TGT.
1. Клиентская машина теперь имеет PRT Azure AD и полный Active Directory TGT и может получить доступ как к облачным, так и к ресурсам.

## <a name="requirements"></a>Требования

Организации должны выполнить шаги по [обеспечению беспадней безопасности на устройствах Windows 10 (предварительный просмотр)](howto-authentication-passwordless-security-key.md) перед завершением шагов в этой статье.

Организации также должны соответствовать следующим требованиям к программному обеспечению.

- Устройства должны работать под управлением Windows 10 Insider Build 18945 или новее.
- Вы должны иметь версию 1.4.32.0 или более позднюю часть [Azure AD Connect.](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)
  - Для получения дополнительной информации о доступных опциях гибридной аутентификации Azure AD [см. Выберите правильный метод проверки подлинности для решения гибридной идентификации Azure Active Directory](../../security/fundamentals/choose-ad-authn.md) и [выберите тип установки для подключения Azure AD](../hybrid/how-to-connect-install-select-installation.md)Connect.
- Контроллеры домена Windows Server должны устанавливать следующие патчи:
    - Для Windows Server 2016 -https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Для Windows Server 2019 -https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Поддерживаемые сценарии

Сценарий поддерживает одиночный входе (SSO) в обоих следующих сценариях:

- Для облачных ресурсов, таких как Office 365 и других приложений с поддержкой SAML.
- Для ресурсов на местах и интегрированной аутентификации Windows для веб-сайтов. Ресурсы могут включать веб-сайты и сайты SharePoint, требующие аутентификации IIS, и/или ресурсы, которые используют аутентификацию NTLM.

### <a name="unsupported-scenarios"></a>Неподдерживаемые сценарии

Следующие сценарии не поддерживаются:

- Домен Active Directory Domain Services (AD DS) Windows Server Active Directory Domain Services (AD DS) присоединился к развертыванию (на территории только устройств).
- Сценарии RDP, VDI и Citrix с использованием ключа безопасности.
- S/MIME с помощью ключа безопасности.
- "Беги как" с помощью ключа безопасности.
- Вход на сервер с помощью ключа безопасности.

## <a name="create-kerberos-server-object"></a>Создание объекта сервера Kerberos

Администраторы используют инструменты PowerShell с сервера Azure AD Connect для создания объекта Azure AD Kerberos Server в их каталоге. Выполнить следующие шаги в каждом домене и лесу в вашей организации, которые содержат пользователей Azure AD:

1. Обновление до последней версии Azure AD Connect. Инструкции предполагают, что вы уже настроили Azure AD Connect для поддержки гибридной среды.
1. На сервере Azure AD Connect, откройте повышенный запрос PowerShell и перейдите к`C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Запустите следующие команды PowerShell для создания нового объекта сервера Azure AD Kerberos как в домене Active Directory, так и в арендаторе Active Directory Azure Active.

> [!NOTE]
> Замените `contoso.corp.com` в следующем примере доменное имя Active Directory.

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Просмотр и проверка сервера Azure AD Kerberos Server

Вы можете просматривать и проверять недавно созданный Azure AD Kerberos Server с помощью следующей команды:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Эта команда выводит свойства сервера Azure AD Kerberos Server. Вы можете просмотреть свойства, чтобы убедиться, что все в порядке.

| Свойство | Описание |
| --- | --- |
| ID | Уникальный идентификатор объекта AD DS DC. Этот идентификатор иногда называют "слот" или это "идентификатор филиала". |
| DomainDnsName | Доменное имя DNS домена Active Directory Domain. |
| ComputerAccount | Объект компьютерной учетной записи объекта Azure AD Kerberos Server (DC). |
| UserAccount | Объект учетной записи отключенного пользователя, в мещавском ключе шифрования Azure AD Kerberos Server TGT. DN этой учетной записи`CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| KeyVersion | Ключевая версия ключа шифрования Azure AD Kerberos Server TGT. Версия назначается при создании ключа. Затем версия приращена каждый раз, когда ключ вращается. Приращения основаны на мета-данных репликации и, вероятно, превышают один. Например, начальная *KeyVersion* может быть *192272*. В первый раз ключ вращается, версия может перейти к *212621*. Важно проверить, что *KeyVersion* для объекта на месте и *CloudKeyVersion* для облачного объекта одинаковы. |
| KeyUpdatedon | Дата и время обновления или создания ключа шифрования Azure AD Kerberos Server TGT. |
| KeyUpdatedFrom | DC, где последний раз обновлялся ключ шифрования Azure AD Kerberos Server TGT. |
| Облачный ид | Идентификатор объекта Azure AD. Должен соответствовать Идентификатору выше. |
| CloudDomainDnsName | *DomainDnsName* от объекта Azure AD. Должен соответствовать *DomainDnsName* выше. |
| CloudKeyVersion | *Клавиатура* из объекта Azure AD. Должен соответствовать *KeyVersion* выше. |
| CloudKeyUpdatedon | *KeyUpdatedOn* с объекта Azure AD. Должен соответствовать *KeyUpdatedOn* выше. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Вращение ключа Azure AD Kerberos Server

Ключи шифрования Azure AD Kerberos Server должны вращаться на регулярной основе. Рекомендуется следовать тому же графику, который используется для поворота всех других ключей контроллера домен active Directory controller krbtgt.

> [!WARNING]
> Есть и другие инструменты, которые могут вращать ключи krbtgt, однако, вы должны использовать инструменты, упомянутые в этом документе, чтобы повернуть ключи krbtgt вашего сервера Azure AD Kerberos Server. Это гарантирует, что ключи обновляются как в помещениях AD, так и в Azure AD.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Удаление сервера Azure AD Kerberos

Если вы хотите вернуть сценарий и удалить Сервер Azure AD Kerberos Server из как внутреннего Active Directory, так и из Active Directory Azure, запустите следующую команду:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Многолесные и многодоменные сценарии

Объект сервера Azure AD Kerberos представлен в Azure AD как объект *KerberosDomain.* Каждый домен Active Directory представлен как единый объект *KerberosDomain* в Azure AD.

Например, ваша организация имеет active Directory forest `contoso.com` `fabrikam.com`с двумя доменами и . Если вы решите разрешить Azure AD выдавать TGT sKerberos TGT для всего леса, в Azure AD есть два объекта *KerberosDomain.* Один *объект KerberosDomain* для `contoso.com` `fabrikam.com`, и один для . Если у вас есть несколько лесов Active Directory, есть один объект *KerberosDomain* для каждого домена в каждом лесу.

Необходимо запустить шаги для [создания объекта сервера Kerberos](#create-kerberos-server-object) в каждом домене и в лесу в вашей организации, в которых содержатся пользователи Azure AD.

## <a name="known-behavior"></a>Известное поведение

Восподписание с FIDO блокируется, если срок действия пароля истек. Ожидается, что пользователь сбросить свой пароль, прежде чем иметь возможность войти в систему с помощью FIDO.

## <a name="troubleshooting-and-feedback"></a>Устранение неполадок и обратная связь

Если вы хотите поделиться обратной связью или столкнуться с проблемами при просмотре этой функции, поделитесь через приложение Windows Feedback Hub, используя следующие шаги:

1. Запустите **концентратор обратной связи** и убедитесь, что вы подписаны.
1. Отправить обратную связь под следующей категоризацией:
   - Категория: Безопасность и конфиденциальность
   - Подкатегория: FIDO
1. Чтобы захватить журналы, используйте опцию, чтобы **воссоздать мою проблему**

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="does-this-work-in-my-on-premises-environment"></a>Работает ли это в моей среде?

Эта функция не работает для чистой на территории Active Directory Domain Services (AD DS) среды.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Для доступа к ресурсам моей организации требуется двухфакторная аутентификация. Что я могу сделать, чтобы поддержать это требование?

Ключи безопасности бывают различных форм-факторов. Свяжитесь с интересующее производителем устройств, чтобы обсудить, как их устройства могут быть включены с PIN-кодом или биометрическим в качестве второго фактора.

### <a name="can-admins-set-up-security-keys"></a>Могут ли админы настроить ключи безопасности?

Мы работаем над этой возможностью для общей доступности (GA) этой функции.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Куда я могу обратиться, чтобы найти совместимые ключи безопасности?

[Ключи безопасности FIDO2](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Что делать, если я потеряю ключ безопасности?

Вы можете удалить ключи с портала Azure, перейдя на страницу **информации о безопасности** и удалив ключ безопасности.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Я не могу использовать FIDO сразу после создания гибридной машины Azure AD

При чистой установке гибридной машины Azure AD после присоединения домена и перезагрузки процесса необходимо войти в систему с паролем и дождаться синхронизации политики, прежде чем сможете использовать FIDO для вхинга.

- Проверьте текущее `dsregcmd /status` состояние, введя в окно команды, и проверьте, что как *AzureAdJoined,* так и *DomainJoined* показывают *ДА.*
- Эта задержка является известным ограничением для устройств, объединенных доменами, и не специфична для FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Я не могу получить SSO на мой сетевой ресурс NTLM после вступления в FIDO и получить запрос на учетные данные

Убедитесь, что достаточно контроллеров доменов исправлены, чтобы ответить вовремя, чтобы обслуживать ваш запрос ресурса. Чтобы проверить, можно ли увидеть контроллер домена, который `nltest /dsgetdc:contoso /keylist /kdc`управляет функцией, просмотрите вывод.

## <a name="next-steps"></a>Дальнейшие действия

[Узнайте больше о безпарочке](concept-authentication-passwordless.md)
