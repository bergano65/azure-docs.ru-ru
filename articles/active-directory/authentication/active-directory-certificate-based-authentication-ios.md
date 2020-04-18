---
title: Проверка подлинности на основе сертификатов на iOS - Активный каталог Azure
description: Узнайте об поддерживаемых сценариях и требованиях к настройке проверки подлинности на основе сертификатов для Active Directory Azure в решениях с устройствами iOS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c5e18a0bf84e96476eafd7ff35398049f1a492
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639628"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Аутентификация на основе сертификата в Azure Active Directory на устройстве iOS

Для повышения безопасности устройства iOS могут использовать проверку подлинности на основе сертификатов (CBA) для проверки подлинности в Active Directory (Azure AD) с помощью сертификата клиента на своем устройстве при подключении к следующим приложениям или службам:

* мобильным приложениям Office, таким как Microsoft Outlook и Microsoft Word;
* клиентам Exchange ActiveSync (EAS).

Использование сертификатов устраняет необходимость ввода комбинации имени пользователя и паролей в определенные почтовые и приложения Microsoft Office на мобильном устройстве.

В этой статье подробно описаны требования и поддерживаемые сценарии настройки ЦБА на устройстве iOS. CBA для iOS доступен в общедоступных облаках Azure, Microsoft Government Cloud, Microsoft Cloud Germany и Microsoft Azure China 21Vianet.

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

## <a name="requirements"></a>Требования

Для использования CBA с iOS применяются следующие требования и соображения:

* Версия оС устройства должна быть iOS 9 или выше.
* Для приложений Office на iOS требуется Microsoft Authenticator.
* Предпочтение идентификации должно быть создано в macOS Keychain, которые включают URL-адрес проверки подлинности сервера ADFS. Для получения дополнительной информации [см.](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)

Следующие требования и соображения Федерации активных директоров (ADFS):

* Сервер ADFS должен быть включен для проверки подлинности сертификата и использования федеративной аутентификации.
* Сертификат должен использовать расширенное использование ключей (EKU) и содержать UPN пользователя в *альтернативном имени субъекта (NT Основное имя)*.

## <a name="configure-adfs"></a>Настройка AD FS

Для отзыва сертификата клиента Azure AD должен иметь следующие претензии. Azure AD добавляет эти утверждения в маркер обновления, если они доступны в токене ADFS (или любом другом токене SAML). Когда маркер обновления должен быть проверен, эта информация используется для проверки отзыва:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`- добавить серийный номер сертификата клиента
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`- добавьте строку для эмитента сертификата клиента

В качестве наилучшей практики следует также обновлять страницы ошибок ADFS организации со следующей информацией:

* Требование об установке Microsoft Authenticator на iOS.
* инструкциями о получении сертификата пользователя.

Для получения дополнительной [информации, см Настройка AD FS знак на странице](https://technet.microsoft.com/library/dn280950.aspx).

## <a name="use-modern-authentication-with-office-apps"></a>Используйте современную аутентификацию с приложениями Office

Некоторые приложения Office с современной аутентификацией позволили отправить `prompt=login` в Azure AD в своем запросе. По умолчанию Azure AD переводится `prompt=login` в запросе в ADFS как `wauth=usernamepassworduri` (просит ADFS сделать U/P Auth) и `wfresh=0` (просит ADFS игнорировать состояние SSO и сделать новую аутентификацию). Если для этих приложений требуется включить проверку подлинности на основе сертификатов, измените поведение Azure AD по умолчанию.

Чтобы обновить поведение по умолчанию, установите '*PromptLoginBehavior'* в настройках федеративного домена для *инвалидов.* Для выполнения этой задачи можно использовать [cmdlet MSOLDomainFederationSettings,](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) как показано в следующем примере:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Поддержка клиентов Exchange ActiveSync

В iOS версии 9 и выше поддерживается собственный почтовый клиент iOS. Чтобы определить, поддерживается ли эта функция для всех других приложений Exchange ActiveSync, свяжитесь с разработчиком приложения.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы настроить проверку подлинности на основе сертификатов в среде, [см.](active-directory-certificate-based-authentication-get-started.md)

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
