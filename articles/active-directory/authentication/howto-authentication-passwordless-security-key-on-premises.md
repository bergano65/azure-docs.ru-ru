---
title: Ключ безопасности без пароля вход в локальные ресурсы (Предварительная версия) — Azure Active Directory
description: Включение безпарольного ключа безопасности вход в локальные ресурсы (Предварительная версия)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42c42bb1e76d854723c8a88e9c3e2c104464beb0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164865"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-preview"></a>Включение безпарольного ключа безопасности вход в локальные ресурсы (Предварительная версия)

В этом документе рассматривается включение проверки подлинности без пароля для локальных ресурсов в средах с присоединенными к **Azure AD** и гибридными устройствами Windows 10, **присоединенными к Azure** AD. Эта функция обеспечивает простой единый вход (SSO) в локальные ресурсы с помощью совместимых с Майкрософт ключей безопасности.

|     |
| --- |
| Ключи безопасности FIDO2 — это общедоступная Предварительная версия функции Azure Active Directory. См. дополнительные сведения о [дополнительных условиях использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Единый вход в локальные ресурсы с помощью ключей FIDO2

Azure Active Directory (AD) может выдавать билеты Kerberos на выдачу билетов (TGT) для одного или нескольких доменов Active Directory. Эта функция позволяет пользователям входить в Windows с помощью современных учетных данных, таких как ключи безопасности FIDO2 и доступ к традиционным ресурсам на основе Active Directory. Билеты службы Kerberos и авторизация будут по-прежнему управляться локальными контроллерами домена Active Directory.

Объект сервера Kerberos Azure AD будет создан в локальном Active Directory, а затем будет безопасно опубликован для Azure Active Directory. Объект не связан ни с одним физическим сервером. Это просто ресурс, который может использоваться Azure Active Directory для создания билетов на получение билета Kerberos (TGT) для домен Active Directory.

![Получение билета TGT и PRT из Azure AD и AD DS](./media/howto-authentication-passwordless-on-premises/fido2-tgt-exchange-process.png)

1. Пользователь входит в систему на устройстве Windows 10 с помощью ключа безопасности FIDO2 и выполняет проверку подлинности в Azure AD.
1. Azure AD проверяет каталог на наличие ключа сервера Kerberos, соответствующего локальному домену AD пользователя.
   1. Azure AD создает TGT Kerberos для локального домена AD пользователя. TGT включает только идентификатор безопасности пользователя. Данные авторизации не включаются в TGT.
1. Билет TGT возвращается клиенту вместе с основным маркером обновления Azure AD (PRT).
1. Клиентский компьютер обращается к локальному контроллеру домена AD и задействует частичный TGT для полностью сформированного TGT.
1. Теперь на клиентском компьютере имеется Azure AD PRT и полное Active Directory TGT, а также доступ к облачным и локальным ресурсам.

## <a name="requirements"></a>Требования

Организации должны выполнить действия, описанные в статье [Включение безпарольного ключа безопасности для устройств Windows 10 (Предварительная версия)](howto-authentication-passwordless-security-key.md) перед выполнением действий, описанных в этой статье.

Организации должны отвечать следующим требованиям к программному обеспечению.

- Устройства должны работать под Windows 10 Insider Build 18945 или более поздней версии
- Обновление до последней версии [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)
- Полностью исправленные контроллеры домена Windows Server 2016/2019 для выполнения локальной загрузки запроса проверки подлинности.

### <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

- Сценарий поддерживает единый вход (SSO) для обоих типов:
   - Облачные ресурсы, такие как Office 365 и другие приложения с поддержкой SAML.
   - Локальные ресурсы и встроенная в Windows проверка подлинности на веб-сайтах, включая веб-сайты и сайты SharePoint, требующие проверки подлинности IIS, и (или) ресурсы, использующие проверку подлинности NTLM.

### <a name="unsupported-scenarios"></a>Неподдерживаемые сценарии

Следующие сценарии не поддерживаются:

- Развертывание домен Active Directory служб Windows Server (AD DS) (только локальное устройство) **не поддерживается**.
- Сценарии RDP, VDI и Citrix **не поддерживаются** с помощью ключа безопасности.
- S/MIME **не поддерживается** с помощью ключа безопасности.
- "Запуск от имени" **не поддерживается** с помощью ключа безопасности.
- Вход на сервер с помощью ключа безопасности **не поддерживается**.

## <a name="create-kerberos-server-object"></a>Создание объекта сервера Kerberos

Администраторы будут использовать средства PowerShell с сервера Azure AD Connect, чтобы создать объект сервера Kerberos Azure AD в своем локальном каталоге. Эти действия необходимо выполнить в каждом домене и лесу в Организации, которые содержат пользователей Azure AD.

1. Выполните обновление до последней версии Azure AD Connect. В инструкциях предполагается, что вы уже настроили Azure AD Connect для поддержки гибридной среды.
1. На Azure AD Connect сервере откройте командную строку PowerShell с повышенными привилегиями и перейдите к папке C:\Program Files\Microsoft Azure Active Directory Коннект\азуреадкерберос\
1. Выполните следующие команды PowerShell, чтобы создать объект сервера Kerberos Azure AD в локальном домене Active Directory и Azure Active Directoryном клиенте.

> [!NOTE]
> Замените "contoso.corp.com" в следующем примере локальным доменным именем Active Directory.

```PowerShell
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

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Просмотр и проверка сервера Kerberos Azure AD

Вы можете просмотреть и проверить только что созданный сервер Kerberos AD Azure с помощью следующей команды. 

```PowerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Эта команда выводит свойства сервера Azure AD Kerberos. Можно проверить свойства, чтобы убедиться, что все находится в правильном порядке.

| Свойство | Описание |
| --- | --- |
| ИД | Уникальный идентификатор объекта контроллера домена Active Directory. Этот идентификатор иногда называют "слотом" или "ИДЕНТИФИКАТОРом ветви". |
| домаинднснаме | Доменное имя DNS домен Active Directory. |
| компутераккаунт | Объект учетной записи компьютера для объекта сервера Kerberos Azure AD (DC). |
| UserAccount | Отключенный объект учетной записи пользователя, содержащий ключ шифрования TGT сервера Kerberos Azure AD. DN этой учетной записи будет: <br> CN = krbtgt_AzureAD, CN = Users, < Domain-DN > |
| кэйверсион | Версия ключа шифрования билета TGT сервера Azure AD Kerberos. Версия назначается при создании ключа. Затем версия увеличивается при каждом повороте ключа. Приращения основано на метаданных репликации и, скорее всего, будет больше единицы. Например, начальным Кэйверсион может быть 192272. При первом повороте ключа версия может перейти на 212621. Важно убедиться, что Кэйверсион для локального объекта и Клаудкэйверсион для облачного объекта одинаковы. |
| кэйупдатедон | Дата и время обновления или создания ключа шифрования TGT сервера Kerberos Azure AD. |
| кэйупдатедфром | Контроллер домена, в котором последний раз был обновлен ключ шифрования билета TGT сервера Azure AD. |
| CloudId | ИДЕНТИФИКАТОР из объекта Azure AD. Должен соответствовать указанному выше ИДЕНТИФИКАТОРу. |
| клауддомаинднснаме | Домаинднснаме из объекта Azure AD. Должен соответствовать Домаинднснаме выше. |
| клаудкэйверсион | Кэйверсион из объекта Azure AD. Должен соответствовать Кэйверсион выше. |
| клаудкэйупдатедон | Кэйупдатедон из объекта Azure AD. Должен соответствовать Кэйупдатедон выше. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Вращение ключа сервера Kerberos Azure AD

Ключи KRBTGT для шифрования сервера Azure AD см. на регулярной основе. Рекомендуется следовать тому же расписанию, которое вы используете для смены всех остальных ключей домен Active Directory контроллера KRBTGT.

> [!WARNING]
> Существуют и другие средства, которые могут поворачивать ключи krbtgt, однако для смены ключей KRBTGT сервера Kerberos Azure AD необходимо использовать средства, упомянутые в этом документе. Это гарантирует, что ключи будут обновлены как в локальной службе AD, так и в Azure AD.

```PowerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Удаление сервера Kerberos Azure AD

Если вы хотите отменить сценарий и удалить сервер Azure AD Kerberos из локальной Active Directory и Azure Active Directory, выполните следующую команду.  

```PowerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Сценарии с несколькими лесами и несколькими доменами

Объект сервера Kerberos Azure AD представлен в Azure AD как объект Керберосдомаин. Каждый локальный домен Active Directory представляется в Azure AD как один объект Керберосдомаин.

Например, в Организации имеется Active Directory лес с двумя доменами: contoso.com и fabrikam.com. Если вы решили разрешить Azure AD выдавать билеты на выдачу билетов Kerberos (TGT) для всего леса, в Azure AD будут два объекта Керберосдомаин. Один объект Керберосдомаин для contoso.com и один для fabrikam.com. При наличии нескольких Active Directory лесов у вас будет один объект Керберосдомаин для каждого домена в каждом лесу.

Вам потребуется выполнить действия, описанные в разделе [Создание объекта сервера Kerberos](#create-kerberos-server-object) в каждом домене и лесу организации, которые содержат пользователей Azure AD.

## <a name="known-behavior"></a>Известное поведение

Если срок действия пароля истек, вход с FIDO будет заблокирован. Ожидание заключается в том, что пользователь должен сбросить свой пароль, прежде чем сможет войти в систему с помощью FIDO.

## <a name="troubleshooting-and-feedback"></a>Устранение неполадок и обратная связь

Если вы хотите поделиться отзывами или столкнуться с проблемами при предварительном просмотре этой функции, предоставьте общий доступ через приложение центра отзывов Windows.

1. Запустите **центр отзывов** и убедитесь, что вы вошли в него.
1. Отправьте отзыв по следующей категории:
   1. Категория: безопасность и конфиденциальность
   1. Подкатегория: FIDO
1. Чтобы записать журналы, используйте параметр: **повторно создать мою проблему**

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="does-this-work-in-my-on-premises-environment"></a>Работает ли это в локальной среде?

Эта функция не работает в чистой локальной среде домен Active Directory служб (AD DS).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Моей организации требуется двухфакторная проверка подлинности для доступа к ресурсам, что можно сделать для поддержки этого требования?

Ключи безопасности бывают разными конструктивными факторами. Обратитесь к изготовителю устройства, чтобы обсудить, как можно включить устройства с помощью ПИН-кода или биометрической метрики в качестве второго фактора.

### <a name="can-admins-set-up-security-keys"></a>Могут ли администраторы настраивать ключи безопасности?

Мы работаем над этой возможностью в общедоступной версии этой функции.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Где можно найти соответствующие ключи безопасности?

[Ключи безопасности FIDO2](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Что делать, если я потеряли ключ безопасности?

Вы можете удалить ключи из портал Azure, перейдя на страницу сведений о безопасности и удалив ключ безопасности.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Я не могу использовать FIDO сразу после создания гибридного компьютера, присоединенного к Azure AD

Если вы выполняете чистую установку гибридного компьютера, присоединенного к Azure AD, после присоединения к домену и перезапуска, необходимо войти в систему с помощью пароля и дождаться, пока политика будет синхронизирована, прежде чем использовать FIDO для входа.

- Проверьте текущее состояние, введя `dsregcmd /status` в командное окно и убедитесь, что `AzureAdJoined` и `DomainJoined` отображаются `YES`.
- Эта задержка является известным ограничением для устройств, присоединенных к домену, и не зависит от FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Не удается получить единый вход для сетевого ресурса NTLM после входа с помощью FIDO и получения запроса учетных данных

Убедитесь, что все контроллеры домена исправлены на время ответа на обслуживание запроса ресурсов. Чтобы проверить, доступен ли контроллер домена, на котором работает эта функция, проверьте выходные данные команды nltest/dsgetdc: contoso/кэйлист/КДК.

## <a name="next-steps"></a>Дальнейшие действия

[Дополнительные сведения о пароле](concept-authentication-passwordless.md)
