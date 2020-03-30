---
title: Устранение неполадок с помощью команды dsregcmd - Активный каталог Azure
description: Использование вывода от dsregcmd для понимания состояния устройств в Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cd782cdab625934fe60617142e5ac0baf756398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128762"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Устройства для устранения неполадок с помощью команды dsregcmd

Утилита dsregcmd/status должна быть запущена как учетная запись пользователя домена.

## <a name="device-state"></a>Состояние устройства

В этом разделе перечислены параметры состояния соединения устройства. В таблице ниже перечислены критерии, по которых устройство находится в различных состояниях соединения.

| AzureAdJoined | ПредприятиеПрисоединились | DomainJoined | Состояние устройства |
| ---   | ---   | ---   | ---   |
| YES | NO | NO | Азовое объявление присоединилось |
| NO | NO | YES | Домен, присоединенный |
| YES | NO | YES | Гибридная АД присоединилась |
| NO | YES | YES | В местных DRS присоединились |

> [!NOTE]
> Состояние Workplace Join (зарегистрировано AD Azure AD) отображается в разделе "Состояние пользователя"

- **AzureAdJoined:** - Установите "ДА", если устройство соединено с Azure AD. "НЕТ" в противном случае.
- **ПредприятиеПрисоединился:** - Установите на "ДА", если устройство соединено с находинным DRS. Устройство не может быть как EnterpriseJoined, так и AzureAdJoined.
- **DomainJoined:** - Установить "ДА", если устройство соединено с доменом (AD).
- **DomainName:** - Установите имя домена, если устройство соединено с доменом.

### <a name="sample-device-state-output"></a>Вывод состояния образца устройства

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Сведения об устройстве

Отображается только тогда, когда устройство присоединилось azure AD или к нему присоединился гибридный Azure AD (не зарегистрированный Azure AD). В этом разделе перечислены данные устройства, идентифицирующие данные, хранящиеся в облаке.

- **DeviceId:** - Уникальный идентификатор устройства в арендаторе Azure AD
- **Отпечаток пальца:** - Отпечаток пальца сертификата устройства 
- **DeviceCertificateValidity:** - Действительность сертификата устройства
- **KeyContainerId:** - ContainerId устройства частный ключ, связанный с сертификатом устройства
- **KeyProvider:** - KeyProvider (Hardware/Software), используемый для хранения закрытого ключа устройства.
- **TpmProtected:** - "ДА", если устройство частный ключ хранится в аппаратном TPM.

### <a name="sample-device-details-output"></a>Вывод деталей образца устройства

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Сведения о клиенте

Отображается только тогда, когда устройство присоединилось azure AD или к нему присоединился гибридный Azure AD (не зарегистрированный Azure AD). В этом разделе перечислены общие сведения о клиентах при присоединении устройства к Azure AD.

> [!NOTE]
> Если URL-адреса MDM в этом разделе пусты, это означает, что MDM либо не был настроен, либо текущий пользователь не находится в сфере регистрации MDM. Проверьте параметры мобильности в Azure AD, чтобы просмотреть конфигурацию MDM.

> [!NOTE]
> Даже если вы видите URL-адреса MDM, это не означает, что устройство управляется MDM. Информация отображается, если у арендатора есть конфигурация MDM для автоматической регистрации, даже если само устройство не управляется. 

### <a name="sample-tenant-details-output"></a>Пример данных о арендаторе вывод

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>состояние пользователя;

В этом разделе перечислены состояние различных атрибутов для пользователя, в настоящее время вводимых в устройство.

> [!NOTE]
> Команда должна работать в контексте пользователя для получения действительного статуса.

- **NgcSet:** - Установить "ДА", если ключ Windows Hello установлен для текущего зарегистрированного на пользователя.
- **NgcKeyId:** - Идентификатор ключа Windows Hello, если он установлен для текущего входа в систему пользователя.
- **CanReset:** - Обозначает, если ключ Windows Hello может быть спрозаирован пользователем. 
- **Возможные значения:** - DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive, или Неизвестный, если ошибка. 
- **WorkplaceJoined:** - Установите "ДА", если учетные записи зарегистрированных Azure AD были добавлены к устройству в текущем контексте NTUSER.
- **WamDefaultSet:** - Установить "ДА", если WAM по умолчанию WebAccount создан для входа в пользователя. Это поле может отображать ошибку, если dsreg /статус запущен с поднятой командой. 
- **WamDefaultAuthority:** - Установить "организации" для Azure AD.
- **WamDefaultId:** -https://login.microsoft.comВсегда " для Azure AD.
- **WAmDefaultGUID:** - GUID провайдера WAM (учетная запись Azure AD/Microsoft) для WEBM по умолчанию. 

### <a name="sample-user-state-output"></a>Вывод состояния примера пользователя

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>Состояние SSO

Этот раздел можно игнорировать для устройств, зарегистрированных ВСУ.

> [!NOTE]
> Команда должна работать в контексте пользователя, чтобы получить действительный статус для этого пользователя.

- **AzureAdPrt:** - Установите на "ДА", если PRT присутствует на устройстве для зарегистрированного пользователя.
- **AzureAdPrtUpdateTime:** - Установить время в UTC, когда PRT был последний раз обновляется.
- **AzureAdPrtExpiryTime:** - Установите время в UTC, когда срок действия ГВП истечет, если он не будет продлен.
- **AzureAdPrtAuthority:** - URL-адрес органа Azure AD
- **EnterprisePrt:** - Установить на "ДА", если устройство имеет PRT от предприимчивых ADFS. Для гибридных устройств Azure AD устройство могло иметь PRT как от Azure AD, так и с предприимченными AD одновременно. В помещениях объединенные устройства будут иметь только Корпоративный PRT.
- **EnterprisePrtUpdateTime:** - Установить время в UTC, когда предприятие PRT был последний раз обновляется.
- **EnterprisePrtExpiryTime:** - Установить время в UTC, когда ГВП истекает, если он не будет продлен.
- **EnterprisePrtAuthority:** - URL-адрес авторитета ADFS

### <a name="sample-sso-state-output"></a>Вывод состояния образца SSO

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Диагностические данные

### <a name="pre-join-diagnostics"></a>Предварительная диагностика

Этот раздел отображается только в том случае, если устройство соединено доменом и не может присоединиться к гибридной Azure AD.

Этот раздел выполняет различные тесты, чтобы помочь диагностировать сбои соединения. Этот раздел также включает в себя детали предыдущего (?). Эта информация включает в себя фазу ошибки, код ошибки, идентификатор запроса сервера, состояние ответа на сервер, сообщение об ошибке ответа на сервер.

- **Контекст пользователя:** - Контекст, в котором проходят диагностики. Возможные значения: SYSTEM, UN-ELEVATED Пользователь, ПОЛЬЗОВАТЕЛЬ ELEVATED. 

   > [!NOTE]
   > Поскольку фактическое соединение выполняется в контексте SYSTEM, запуск диагностики в контексте SYSTEM ближе всего к реальному сценарию соединения. Для выполнения диагностики в контексте SYSTEM команда dsregcmd/status должна быть запущена с поднятой оперативной выемки команды.

- **Время клиента:** - Системное время в UTC.
- **Тест подключения AD:** - Тест выполняет тест на подключение к контроллеру домена. Ошибка в этом тесте, скорее всего, приведет к ошибкам соединения на этапе предварительной проверки.
- **Тест конфигурации AD:** - Тест считывает и проверяет, правильно ли настроен объект SCP в предприимчном лесу AD. Ошибки в этом тесте, скорее всего, приведут к ошибкам соединения на этапе обнаружения с кодом ошибки 0x801c001d.
- **DRS Discovery Test:** - Тест получает конечные точки DRS от конечных точек обнаружения метаданных и выполняет запрос пользовательского царства. Ошибки в этом тесте, скорее всего, приведут к ошибкам соединения на этапе обнаружения.
- **DRS Connectivity Test:** - Тест выполняет базовый тест на подключение к конечной точке DRS.
- **Тест приобретения токенов:** - Тест пытается получить токен аутентификации Azure AD, если арендатор пользователя является федеративным. Ошибки в этом тесте, скорее всего, приведут к ошибкам соединения на фазе Auth. Если auth не удается синхронизировать соединение будет предпринята в качестве резервного, если резервное копирование явно отключенс ниже настройки ключей реестра.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Обратный возврат к Sync-Join:** - Установить "Включено", если выше реестр ключ, чтобы предотвратить резервное синхронизация, чтобы синхронизировать с сбоями auth, не присутствует. Эта опция доступна с Windows 10 1803 и позже.
- **Предыдущая регистрация:** - Время предыдущей попытки присоединения произошло. Регистрируются только неудачные попытки присоединения.
- **Этап ошибки:** - Этап соединения, в котором он был прерван. Возможные значения предварительно проверить, обнаружить, auth, присоединиться.
- **Клиент Ошибка Код:** - Код ошибки клиента вернулся (HRESULT).
- **Server ErrorCode:** - Код ошибки сервера, если запрос был отправлен на сервер и сервер ответил обратно с кодом ошибки. 
- **Сообщение сервера:** - Сообщение сервера возвращается вместе с кодом ошибки.
- **Статус https:** - Статус http, возвращенный сервером.
- **Идентификатор запроса:** - Запрос клиентаid отправлен на сервер. Полезно соотносить журналы на стороне сервера.

### <a name="sample-pre-join-diagnostics-output"></a>Образец предварительного соединения диагностики выход

В следующем примере показано, что тест диагностики не удается с ошибкой обнаружения.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

Следующий пример показывает, что тесты на диагностику проходят, но попытка регистрации не сработала с ошибкой каталога, которая, как ожидается, для синхронизации. Как только задание синхронизации Azure AD Connect завершится, устройство сможет присоединиться.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>Диагностика после соединения

В этом разделе отображается выход проверок вменяемости, выполняемых на устройстве, присоединенном к облаку.

- **AadRecoveryEnabled:** - Если "ДА", ключи, хранящиеся в устройстве, не пригодны для узла и устройство помечено для восстановления. Следующий входе вызовет поток восстановления и перерегистрирует устройство.
- **KeySignTest:** - Если "PASSED" клавиши устройства находятся в хорошем состоянии. Если KeySignTest выходит из строя, устройство обычно маркируется для восстановления. Следующий входе вызовет поток восстановления и перерегистрирует устройство. Для гибридных устройств, присоединившихся к Azure AD, восстановление проходит без звучание. В то время как Azure AD присоединился или Azure AD зарегистрирован, устройства будут побуждать пользователей к восстановлению и перерегистрации устройства в случае необходимости. **KeySignTest требует повышенных привилегий.**

#### <a name="sample-post-join-diagnostics-output"></a>Образец выходной диагностики после соединения

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>Проверка предпосылок NGC

В этом разделе выполняется проверка на соответствие для подготовки Windows Hello for Business (WHFB). 

> [!NOTE]
> Вы можете не видеть детали предварительной проверки NGC в dsregcmd /статусе, если пользователь уже успешно настроен WHFB.

- **IsDeviceJoined:** - Установите "ДА", если устройство соединено с Azure AD.
- **IsUserAzureAD:** - Установить "ДА", если зарегистрированный пользователь присутствует в Azure AD .
- **ПолитикаВ том** же, что: - Установите "ДА", если на устройстве включена политика WHFB.
- **PostLogonEnabled:** - Установить на "ДА", если регистрация WHFB вызвана родной платформы. Если он настроен на "НЕТ", это означает, что Windows Hello для бизнеса регистрации вызвано пользовательским механизмом
- **DeviceEligible:** - Установить на "ДА", если устройство отвечает аппаратным требованиям для регистрации с WHFB.
- **SessionIsNotRemote:** - Установить "ДА", если текущий пользователь входит непосредственно на устройство, а не удаленно.
- **CertРегистрация:** - Специфические для WHFB Сертификат Целевой развертывания, с указанием сертификата регистрации органа для WHFB. Установить "регистрационный орган", если источником политики WHFB является политика группы, "управление мобильными устройствами", если источником является MDM. "нет" в противном случае
- **AdfsRefreshToken:** - Специфическое развертывание СЕРТИФИКАТа WHFB Trust. Только присутствуйте, если CertEnrollment является "органом регистрации". Указывает, имеет ли устройство корпоративный PRT для пользователя.
- **AdfsRaIsReady:** - Специфическое развертывание WHFB Certificate Trust.  Только присутствуйте, если CertEnrollment является "органом регистрации". Установите на "ДА", если ADFS указал в метаданных обнаружения, что он поддерживает WHFB *и* если шаблон логон сертификата доступен.
- **LogonCertTemplateReady:** - Специфические для WHFB Сертификат Целевой развертывания. Только присутствуйте, если CertEnrollment является "органом регистрации". Установить на "ДА", если состояние шаблона сертификата логона является действительным и помогает устранить неполадки ADFS RA.
- **PreReqResult:** - Обеспечивает результат всей оценки предпосылок WHFB. Установить на "Будет положение", если WHFB регистрации будет запущен в качестве пост-логон задача, когда пользователь подписывает в следующий раз.

### <a name="sample-ngc-prerequisite-check-output"></a>Вывод предпосылок проверки предпосылок для проверки выборов NGC

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Дальнейшие действия

Ответы на вопросы можно найти в статье [Azure Active Directory device management FAQ](faq.md) (Часто задаваемые вопросы по управлению устройствами Azure Active Directory).
