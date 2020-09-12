---
title: Хранилище данных удостоверений для европейских клиентов — Azure AD
description: Узнайте, где Azure Active Directory хранит данные, связанные с идентификаторами клиентов из ЕС.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 874df2cc17e291cb6811ca07d01237f01b523860
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565044"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Хранение данных удостоверений клиентов из ЕС — Azure Active Directory
Данные удостоверений хранятся в службе Azure AD в географическом расположении на основе адреса, предоставленного вашей организацией, при подписке на службу Microsoft Online Service, например Microsoft 365 и Azure. Сведения о том, где хранятся данные удостоверений, можно найти в разделе [где находятся ваши данные?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) центра управления безопасностью Майкрософт.

Для клиентов, которые указали адрес в Европе, Azure AD хранит большую часть данных удостоверений в европейских центрах данных. В этом документе содержатся сведения о любых данных, хранящихся за пределами Европы службами Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Многофакторная проверка подлинности Microsoft Azure (MFA)
    
- Вся двухфакторная проверка подлинности, использующая телефонные звонки или SMS-сообщения, поступает из центров обработки данных США и направляется глобальными поставщиками.
- Push-уведомления, использующие приложение Microsoft Authenticator, формируются в центрах обработки данных в США. Кроме того, службы, связанные с поставщиками устройств, также могут играть в игру, а эти службы, возможно, вне Европы.
- OATH-коды всегда проверяются в США. 

Дополнительные сведения о том, какие сведения о пользователе собираются в Azure сервер Многофакторной идентификации (сервер MFA) и в облачной службе Azure MFA, см. в статье [сбор данных пользователей многофакторной идентификации Azure](../authentication/howto-mfa-reporting-datacollection.md).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Active Directory B2C)

Данные конфигурации политики Azure AD B2C и контейнеры ключей хранятся в центрах обработки данных США. Они не содержат персональных данных пользователя. Дополнительные сведения о конфигурациях политики см. в статье [Azure Active Directory B2C: встроенные политики](../../active-directory-b2c/user-flow-overview.md).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Active Directory B2B) 
    
Azure AD B2B хранит приглашения со ссылками для активации и URL-адресом перенаправления в центрах обработки данных США. Кроме того, адреса электронной почты пользователей, которые отменяют подписываться на получение приглашений B2B, также хранятся в центрах обработки данных США.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Доменные службы Microsoft Azure Active Directory (Azure AD DS)

Azure AD DS хранит данные пользователей в том же расположении, где размещена виртуальная сеть Azure, выбранная клиентом. Таким образом, если сеть находится за пределами Европы, то данные реплицируются и хранятся за пределами Европы.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Федерация в Microsoft Exchange Server 2013
    
- Идентификатор приложения (AppID) — уникальный номер, созданный системой проверки подлинности Azure Active Directory для идентификации организаций Exchange.
- Список утвержденных федеративных доменов для приложения
- Открытый ключ подписи маркера приложения 

Дополнительные сведения о Федерации в Microsoft Exchange Server см. в статье [интеграция: Exchange 2013](/exchange/federation-exchange-2013-help) .


## <a name="other-considerations"></a>Другие замечания

Службы и приложения, которые интегрируются с Azure AD, имеют доступ к данным удостоверений. Оцените каждую службу и приложение, используемые для определения того, как данные удостоверений обрабатываются этой конкретной службой и приложением, а также соответствуют ли они требованиям к хранению данных вашей компании.

Дополнительные сведения о местонахождении данных служб Майкрософт см. в разделе [Где находятся ваши данные?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Центра управления безопасностью Майкрософт.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об этих функциях и возможностях, описанных выше, смотрите в следующих статьях.
- [Что такое Многофакторная идентификация Azure?](../authentication/concept-mfa-howitworks.md)

- [Самостоятельный сброс пароля в Azure AD](../authentication/concept-sspr-howitworks.md)

- [Что такое Azure Active Directory B2C?](../../active-directory-b2c/overview.md)

- [Что такое служба совместной работы Azure AD B2B?](../external-identities/what-is-b2b.md)

- [Доменные службы Azure Active Directory (AD)](../../active-directory-domain-services/overview.md)