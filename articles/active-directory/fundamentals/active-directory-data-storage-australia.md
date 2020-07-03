---
title: Хранилище данных удостоверений для клиентов Австралии и Новой Зеландии — Azure AD
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
ms.openlocfilehash: 850298719d5636e964b0c338d7a2a4cc9bb8aece
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77370298"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Хранилище данных удостоверений для клиентов Австралии и Новой Зеландии в Azure Active Directory

Данные удостоверений хранятся в службе Azure AD в географическом расположении на основе адреса, предоставленного вашей организацией, при подписке на Microsoft Online Service, например Office 365 и Azure. Сведения о том, где хранятся данные клиентов удостоверений, можно найти в разделе [где находятся ваши данные?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) центра управления безопасностью Майкрософт.

> [!NOTE]
> Службы и приложения, которые интегрируются с Azure AD, имеют доступ к данным клиента удостоверений. Оцените каждую службу и приложение, используемые для определения способа обработки данных пользователей удостоверений с помощью конкретной службы и приложения, а также укажите, соответствуют ли они требованиям к хранению данных вашей компании. Дополнительные сведения о местонахождении данных служб Майкрософт см. в разделе Где находятся ваши данные? Центра управления безопасностью Майкрософт.

Для клиентов, которые указали адрес в Австралии или Новой Зеландии, Azure AD сохраняет данные удостоверений для этих служб в австралийских центрах обработки данных: 
- Управление каталогами Azure AD 
- Аутентификация

Все остальные службы Azure AD хранят данные клиентов в глобальных центрах обработки данных. Чтобы найти центр обработки данных для службы, см [. раздел Azure Active Directory – где находятся ваши данные?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Многофакторная проверка подлинности Microsoft Azure (MFA)

MFA хранит данные о клиентах в глобальных центрах обработки данных. Дополнительные сведения о сведениях о пользователе, собираемых и хранимых в облачных Azure MFA и сервере Azure MFA, см. в статье [сбор данных пользователей многофакторной идентификации Azure](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency).

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об этих функциях и возможностях, описанных выше, смотрите в следующих статьях.
- [Что такое Многофакторная идентификация Azure?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
