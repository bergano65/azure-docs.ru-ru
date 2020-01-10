---
title: Хранилище данных удостоверений для клиентов Австралии — Azure AD
description: Узнайте, где Azure Active Directory хранит данные, связанные с удостоверениями, для клиентов Австралии.
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
ms.openlocfilehash: cbe4befa824990c2f8b2627e7709913f564c0486
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751423"
---
# <a name="identity-data-storage-for-australian-customers-in-azure-active-directory"></a>Хранилище данных удостоверений для клиентов Австралии в Azure Active Directory

Данные удостоверений хранятся в службе Azure AD в географическом расположении на основе адреса, предоставленного вашей организацией, при подписке на Microsoft Online Service, например Office 365 и Azure. Сведения о том, где хранятся данные клиентов удостоверений, можно найти в разделе [где находятся ваши данные?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) центра управления безопасностью Майкрософт.

> [!NOTE]
> Службы и приложения, которые интегрируются с Azure AD, имеют доступ к данным клиента удостоверений. Оцените каждую службу и приложение, используемые для определения способа обработки данных пользователей удостоверений с помощью конкретной службы и приложения, а также укажите, соответствуют ли они требованиям к хранению данных вашей компании. Дополнительные сведения о местонахождение данных служб Майкрософт см. в разделе где находятся ваши данные? Центра управления безопасностью Майкрософт.

Для клиентов, которые указали адрес в Австралии, Azure AD сохраняет данные удостоверений для этих служб в австралийских центрах обработки данных: 
- Управление каталогами Azure AD 
- Проверка подлинности

Все остальные службы Azure AD хранят данные клиентов в глобальных центрах обработки данных. Чтобы найти центр обработки данных для службы, см [. раздел Azure Active Directory – где находятся ваши данные?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Многофакторная проверка подлинности Microsoft Azure (MFA)

MFA хранит данные о клиентах в глобальных центрах обработки данных. Дополнительные сведения о сведениях о пользователе, собираемых и хранимых в облачных Azure MFA и сервере Azure MFA, см. в статье [сбор данных пользователей многофакторной идентификации Azure](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency).

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об этих функциях и возможностях, описанных выше, смотрите в следующих статьях.
- [Что такое Многофакторная идентификация Azure?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
