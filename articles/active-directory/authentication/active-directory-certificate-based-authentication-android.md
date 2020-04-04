---
title: Проверка подлинности сертификатов на основе Android - Активный каталог Azure
description: Узнайте о поддерживаемых сценариях и требованиях к настройке аутентификации на основе сертификата в решениях на устройствах Android.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e0427398d05cbe9f76249ec8f7c25568d566d5d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654379"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Аутентификация на основе сертификата в Azure Active Directory на устройстве Android

Для устройств Android можно применять аутентификацию на основе сертификатов (CBA) в Azure Active Directory. Для этого на устройстве используется сертификат клиента при подключении к:

* мобильным приложениям Office, таким как Microsoft Outlook и Microsoft Word;
* клиентам Exchange ActiveSync (EAS).

Настройка данной функции избавляет от необходимости ввода имени пользователя и пароля в определенных почтовых клиентах и приложениях Microsoft Office на мобильных устройствах.

В этой статье приведены требования и поддерживаемые сценарии для настройки CBA на устройстве iOS (Android) для пользователей клиентов в тарифных планах Office 365 корпоративный, бизнес, для образования, для US Gov организаций, Китая и Германии.

В тарифных планах Office 365 US Government Defense и Federal доступна предварительная версия этой функции.

## <a name="microsoft-mobile-applications-support"></a>Поддержка мобильных приложений Microsoft

| Приложения | Поддержка |
| --- | --- |
| Приложение Azure Information Protection |![Проверка знака, означающего поддержку этого приложения][1] |
| Корпоративный портал Intune |![Проверка знака, означающего поддержку этого приложения][1] |
| Microsoft Teams |![Проверка знака, означающего поддержку этого приложения][1] |
| OneNote |![Проверка знака, означающего поддержку этого приложения][1] |
| OneDrive |![Проверка знака, означающего поддержку этого приложения][1] |
| Outlook |![Проверка знака, означающего поддержку этого приложения][1] |
| Power BI |![Проверка знака, означающего поддержку этого приложения][1] |
| Skype для бизнеса |![Проверка знака, означающего поддержку этого приложения][1] |
| Word/Excel/PowerPoint |![Проверка знака, означающего поддержку этого приложения][1] |
| Yammer |![Проверка знака, означающего поддержку этого приложения][1] |

### <a name="implementation-requirements"></a>Требования к реализации

На устройстве должна быть установлена ОС Android версии 5.0 (Lollipop) и выше.

Необходимо настроить сервер федерации.

Чтобы служба Azure Active Directory могла отзывать сертификат клиента, маркер AD FS должен иметь следующие утверждения:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (серийный номер сертификата клиента);
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (строка для издателя сертификата клиента).

Azure Active Directory добавляет эти утверждения в маркер обновления, если они доступны в маркере AD FS (или любом другом токене SAML). Когда требуется проверить маркер обновления, эта информация используется для проверки отзыва.

Рекомендуем обновить страницы ошибок AD FS вашей организации следующими сведениями:

* требованием установки Microsoft Authenticator для Android;
* инструкциями о получении сертификата пользователя.

Дополнительные сведения см. в статье о [настройке страниц входа AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Некоторые приложения Office (с помощью современной аутентификации) отправляют *'prompt'login'* в Azure AD в своем запросе. По умолчанию Azure AD переводит *'prompt'login'* в запросе в ADFS как '*wauth'usernamepassworduri'*(просит ADFS сделать U/P Auth) и *'wfresh'0'*(просит ADFS игнорировать состояние SSO и сделать новую аутентификацию). Чтобы включить проверку подлинности на основе сертификатов для этих приложений, необходимо изменить поведение Azure AD по умолчанию. Установите '*PromptLoginBehavior*' в настройках федеративного домена на '*Инвалидов'.*
Для выполнения этой задачи можно использовать командлет [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0):

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Поддержка клиентов Exchange ActiveSync

Поддерживаются некоторые приложения Exchange ActiveSync, работающие на Android 5.0 (Lollipop) или более поздней версии. Чтобы определить, поддерживает ли почтовая программа эту функцию, обратитесь к разработчику приложения.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы настроить аутентификацию на основе сертификата в своей среде, ознакомьтесь с инструкциями в статье [Get started with certificate-based authentication in Azure Active Directory](active-directory-certificate-based-authentication-get-started.md) (Приступая к работе с аутентификацией на основе сертификата в Azure Active Directory).

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
