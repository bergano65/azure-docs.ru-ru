---
title: Хранение идентификационных данных для европейских клиентов - Azure AD
description: Узнайте, где Azure Active Directory хранит данные, связанные с идентификаторами клиентов из ЕС.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7360b11f41cc08c2beb3ffa227e1658881798502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422999"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Хранение данных удостоверений клиентов из ЕС — Azure Active Directory
Идентификационные данные хранятся Azure AD в географическом местоположении, основанном на адресе, предоставленном вашей организацией при подписке на службу Microsoft Online, такую как Office 365 и Azure. Для получения информации о том, где хранятся данные, вы можете использовать раздел [«Где находятся ваши данные?»](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) в разделе Целевой центр Майкрософт.

Для клиентов, предоставивших адрес в Европе, Azure AD хранит большую часть идентификационных данных в европейских центрах обработки данных. В этом документе содержится информация о любых данных, хранящихся за пределами Европы службами Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Многофакторная аутентификация Microsoft Azure (MFA)
    
- Вся двухфакторная аутентификация с помощью телефонных звонков или SMS поступает из центров обработки данных в США, а также направляется глобальными провайдерами.
- Push-уведомления с помощью приложения Microsoft Authenticator поступают из центров обработки данных в США. Кроме того, поставщик устройств конкретных услуг может также вступить в игру, и эти услуги, возможно, за пределами Европы.
- OATH-коды всегда проверяются в США. 

Для получения дополнительной информации о том, какая информация о пользователе собирается Azure Multi-Factor [Azure Multi-Factor Authentication user data collection](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection)Authentication Server (MFA Server) и облачным Azure MFA, см.

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Active Directory B2C)

Данные конфигурации политики Azure AD B2C и ключевые контейнеры хранятся в центрах обработки данных в США. Они не содержат личных данных пользователей. Дополнительные сведения о конфигурациях политики см. в статье [Azure Active Directory B2C: встроенные политики](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Active Directory B2B) 
    
Azure AD B2B хранит приглашения с помощью ссылки выкупа и перенаправлять информацию URL-адреса в центры обработки данных в США. Кроме того, адрес электронной почты пользователей, которые отписываются от получения приглашений B2B, также хранится в центрах обработки данных в США.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Доменные службы Microsoft Azure Active Directory (Azure AD DS)

Azure AD DS хранит данные пользователей в том же расположении, где размещена виртуальная сеть Azure, выбранная клиентом. Таким образом, если сеть находится за пределами Европы, то данные реплицируются и хранятся за пределами Европы.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Федерация в Microsoft Exchange Server 2013
    
- Идентификатор приложений (AppID) - уникальный номер, генерируемый системой проверки подлинности Active Directory Azure для идентификации организаций Exchange.
- Утвержденный список федеративных доменов для приложения
- Подпись маркера приложения Public Key 

Для получения дополнительной информации о федерации на сервере Microsoft Exchange см. [Federation: Exchange 2013 Help](https://docs.microsoft.com/exchange/federation-exchange-2013-help)


## <a name="other-considerations"></a>Другие замечания

Службы и приложения, интегрирующиеся с Azure AD, имеют доступ к идентификационным данным. Оцените каждую службу и приложение, которые вы используете, чтобы определить, как данные идентификационных данных обрабатываются этой конкретной службой и приложением, и соответствуют ли они требованиям вашей компании к хранению данных.

Дополнительные сведения о местонахождении данных служб Майкрософт см. в разделе [Где находятся ваши данные?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Центра управления безопасностью Майкрософт.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об этих функциях и возможностях, описанных выше, смотрите в следующих статьях.
- [Что такое Многофакторная идентификация Azure?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Сбросить пароля самообслуживания Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Что такое Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Что такое сотрудничество Azure AD B2B?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Доменные службы Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
