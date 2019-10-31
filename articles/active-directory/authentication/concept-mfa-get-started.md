---
title: Включение многофакторной идентификации для Организации — Azure Active Directory
description: Включение Azure MFA для вашей организации на основе лицензии
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98bf62dff3e6c642f5de775f2d5bf8682ecea169
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164930"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>Включение многофакторной идентификации для Организации

Существует несколько способов включить многофакторную идентификацию Azure (MFA) для пользователей Azure Active Directory (AD) на основе лицензий, которыми владеет ваша организация. 

![Исследование сигналов и принудительное применение MFA при необходимости](./media/concept-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

На основе наших исследований ваша учетная запись более чем на 99,9% менее вероятно будет скомпрометирована при использовании MFA.

Итак, как ваша организация включает многофакторную проверку подлинности даже бесплатно, прежде чем стать статистикой?

## <a name="free-option"></a>Бесплатный вариант

Клиенты, которые используют бесплатные преимущества Azure AD, могут использовать [Параметры безопасности по умолчанию](../conditional-access/concept-conditional-access-security-defaults.md) для включения многофакторной проверки подлинности в своей среде.

## <a name="office-365"></a>Office 365

Для клиентов с Office 365 существует два варианта:

- [Параметры безопасности по умолчанию](../conditional-access/concept-conditional-access-security-defaults.md) можно включить в Azure AD, чтобы защитить всех пользователей с помощью многофакторной идентификации Azure.
- Если организация требует более детального использования многофакторной проверки подлинности, лицензии Office включают возможности [многопользовательского MFA](../authentication/howto-mfa-userstates.md) . Многопользовательский MFA включается и применяется для каждого пользователя по отдельности администраторами.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Для клиентов с Azure AD Premium P1 или аналогичными лицензиями, которые включают такие функции, как Enterprise Mobility + Security E3, Microsoft 365 F1 или Microsoft 365 E3: 

Рекомендуется использовать [политики условного доступа](../conditional-access/concept-conditional-access-policy-common.md) для наилучшего взаимодействия с пользователем.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Для клиентов с Azure AD Premium P2 или аналогичных лицензий, которые включают такие функции, как Enterprise Mobility + Security/или Microsoft 365: 

Рекомендуется использовать [политики условного доступа](../conditional-access/concept-conditional-access-policy-common.md) вместе с политиками [защиты идентификации](../identity-protection/overview-v2.md) для наилучшего взаимодействия с пользователем и обеспечения гибкости применения.

## <a name="authentication-methods"></a>Методы проверки подлинности

|   | Параметры безопасности по умолчанию | Все остальные методы |
| --- | --- | --- |
| Уведомление в мобильном приложении | X | X |
| Код проверки из мобильного приложения или токен оборудования |   | X |
| SMS на телефон |   | X |
| Звонок на телефон |   | X |
| Пароли приложений |   | X * * |

\* * Пароли приложений доступны только в многопользовательский MFA с устаревшими сценариями проверки подлинности, только если они включены администраторами.

## <a name="next-steps"></a>Дальнейшие действия

[Страница цен на Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
