---
title: Хранение идентификационных данных для австралийских клиентов - Azure AD
description: Узнайте о том, где Azure Active Directory хранит данные, связанные с идентификацией, для своих австралийских клиентов.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dc659f3d580646c1e86e5e97940268311e5546f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460540"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Хранение идентификационных данных для австралийских и новозеландских клиентов в Active Directory Azure

Идентификационные данные хранятся Azure AD в географическом местоположении, основанном на адресе, предоставленном вашей организацией при подписке на службу Microsoft Online, такую как Office 365 и Azure. Для получения информации о том, где хранятся данные клиентов, вы можете использовать раздел [«Где находится ваши данные?»](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) в разделе Целевого центра Майкрософт.

> [!NOTE]
> Службы и приложения, интегрирующиеся с Azure AD, имеют доступ к идентификационным данным клиентов. Оцените каждую службу и приложение, которые вы используете, чтобы определить, как данные идентификационных клиентов обрабатываются этой конкретной службой и приложением, и соответствуют ли они требованиям вашей компании к хранению данных. Дополнительные сведения о местонахождении данных служб Майкрософт см. в разделе Где находятся ваши данные? Центра управления безопасностью Майкрософт.

Для клиентов, предоставивших адрес в Австралии и Новой Зеландии и использующих бесплатное издание Azure AD, Azure AD хранит данные PII в остальных центрах обработки данных в австралийских центрах обработки данных. 

Все остальные премиальные службы Azure AD хранят данные о клиентах в глобальных центрах обработки данных. Чтобы найти центр обработки данных для службы, смотрите каталог [Active - Где находятся ваши данные?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Многофакторная аутентификация Microsoft Azure (MFA)

Служба МИД в Azure AD хранит данные клиентов identity в глобальных центрах обработки данных в состоянии покоя. Чтобы узнать больше о пользовательской информации, собранной и хранящейся [Azure Multi-Factor Authentication user data collection](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)в облачных Azure MFA и Azure MFA Server, см. Если клиенты используют МИД, их данные будут храниться за пределами центров обработки данных в Австралии в состоянии покоя. 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об этих функциях и возможностях, описанных выше, смотрите в следующих статьях.
- [Что такое Многофакторная идентификация Azure?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
