---
title: Хранение данных удостоверений клиентов из ЕС — Azure Active Directory | Документация Майкрософт
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
ms.openlocfilehash: 33704c3392726ccd12ca0fd74f26465a54697e11
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722143"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Хранение данных удостоверений клиентов из ЕС — Azure Active Directory
Данные удостоверений хранятся в службе Azure AD в географическом расположении на основе адреса, предоставленного вашей организацией, при подписке на Microsoft Online Service, например Office 365 и Azure. Сведения о том, где хранятся данные удостоверений, можно найти в разделе [где находятся ваши данные?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) центра управления безопасностью Майкрософт.

Для клиентов, которые указали адрес в Европе, Azure AD хранит большую часть данных удостоверений в европейских центрах данных. В этом документе содержатся сведения о любых данных, хранящихся за пределами Европы службами Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Многофакторная проверка подлинности Microsoft Azure (MFA)
    
- Вся двухфакторная проверка подлинности, использующая телефонные звонки или SMS-сообщения, поступает из центров обработки данных США и направляется глобальными поставщиками.
- Push-уведомления, использующие приложение Microsoft Authenticator, берутся из центров обработки данных США. Кроме того, службы, связанные с поставщиками устройств, также могут играть в игру, а эти службы, возможно, вне Европы.
- OATH-коды всегда проверяются в США. 

Дополнительные сведения о том, какие сведения о пользователях собираются сервером многофакторной идентификации Azure (сервером MFA) и облачным Azure MFA, см. в статье [сбор данных пользователей многофакторной идентификации Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Данные конфигурации политики Azure AD B2C и контейнеры ключей хранятся в центрах обработки данных США. Они не содержат персональных данных пользователя. Дополнительные сведения о конфигурациях политики см. в статье [Azure Active Directory B2C: встроенные политики](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B хранит приглашения со ссылками для активации и URL-адресом перенаправления в центрах обработки данных США. Кроме того, адреса электронной почты пользователей, которые отменяют подписываться на получение приглашений B2B, также хранятся в центрах обработки данных США.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Доменные службы Microsoft Azure Active Directory (Azure AD DS)

Azure AD DS хранит данные пользователей в том же расположении, где размещена виртуальная сеть Azure, выбранная клиентом. Таким образом, если сеть находится за пределами Европы, то данные реплицируются и хранятся за пределами Европы.

## <a name="other-considerations"></a>Дополнительные рекомендации

Службы и приложения, которые интегрируются с Azure AD, имеют доступ к данным удостоверений. Оцените каждую службу и приложение, используемые для определения того, как данные удостоверений обрабатываются этой конкретной службой и приложением, а также соответствуют ли они требованиям к хранению данных вашей компании.

Дополнительные сведения о местонахождении данных служб Майкрософт см. в разделе [Где находятся ваши данные?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Центра управления безопасностью Майкрософт.

## <a name="next-steps"></a>Следующие шаги
Дополнительные сведения об этих функциях и возможностях, описанных выше, смотрите в следующих статьях.
- [Что такое Многофакторная идентификация Azure?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Самостоятельный сброс пароля в Azure AD для ИТ-специалистов](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Что такое Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Что такое служба совместной работы Azure AD B2B?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Доменные службы Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
