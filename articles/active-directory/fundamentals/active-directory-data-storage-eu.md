---
title: Хранение данных удостоверений клиентов из ЕС — Azure Active Directory | Документация Майкрософт
description: Узнайте, где Azure Active Directory хранит данные, связанные с идентификаторами клиентов из ЕС.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e82a78953c4385f7688705d4ab3f697be9c3ddbd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235165"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Хранение данных удостоверений клиентов из ЕС — Azure Active Directory
Azure AD хранит учетные данные в географическом расположении, по адресу, предоставляемую вашей организацией, при настройке подписки для службы Microsoft Online, например Office 365 и Azure. Сведения о которых хранятся данные удостоверений, можно использовать [где находятся ваши данные?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) разделе в центре управления безопасностью Майкрософт.

Клиенты, адрес в Европе Azure AD хранит большинство данных удостоверений в европейских центрах обработки данных. Этот документ содержит сведения на основе любых данных, которые хранятся вне Европа службами Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Многофакторная Идентификация Microsoft Azure (MFA)
    
- Все двухфакторной проверки подлинности, используют телефонные звонки или SMS берутся из центров обработки данных США, а также направляются глобального поставщиками.
- Push-уведомлений с помощью Microsoft Authenticator, приложение исходят от НАС центров обработки данных. Кроме того устройство поставщика определенных служб также могут поступать в Google play и эти службы может быть вне Европа.
- OATH-коды всегда проверяются в США.

Дополнительные сведения о собираемой информации пользователя, сервер многофакторной идентификации Azure (сервер MFA) и MFA в облаке Azure, см. в разделе [коллекцию данных пользователей многофакторной идентификации Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C политики конфигурации данных и контейнерами ключей хранятся в центрах обработки данных США. Они не содержат личных данных пользователя. Дополнительные сведения о конфигурациях политики см. в статье [Azure Active Directory B2C: встроенные политики](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Приглашения в Azure AD B2B хранилищ с активировать связи и перенаправление информацию URL-адреса в центрах обработки данных США. Кроме того адрес электронной почты пользователей, которые могут отменить подписку на получение приглашений B2B также хранятся в центрах обработки данных США.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure доменных служб Active Directory (Azure AD DS)

Azure AD DS хранит данные пользователей в том же расположении, где размещена виртуальная сеть Azure, выбранная клиентом. Таким образом, если сеть находится за пределами Европы, то данные реплицируются и хранятся за пределами Европы.

## <a name="other-considerations"></a>Дополнительные рекомендации

Службы и приложения, которые интегрируются с Azure AD имеют доступ к данным удостоверений. Оцените все службы и приложения, которые позволяют определить порядок обработки учетных данных с этой конкретной службы и приложения, и соответствуют ли требования к хранилищу данных вашей компании.

Дополнительные сведения о местонахождении данных служб Майкрософт см. в разделе [Где находятся ваши данные?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Центра управления безопасностью Майкрософт.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об этих функциях и возможностях, описанных выше, смотрите в следующих статьях.
- [Что такое Многофакторная идентификация Azure?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Самостоятельный сброс пароля в Azure AD для ИТ-специалистов](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Что такое Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Что такое служба совместной работы Azure AD B2B?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Доменные службы Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
