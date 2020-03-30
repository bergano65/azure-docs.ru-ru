---
title: Многофакторная аутентификация Azure для вашей организации - Активный каталог Azure
description: Узнайте о доступных функциях многофакторной аутентификации Azure для вашей организации на основе модели лицензии
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: febdb708c637ac322c0ca884eae627da9bd5904c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530399"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Обзор многофакторной аутентификации Azure для вашей организации

Существует несколько способов включения мультифакторной аутентификации Azure для пользователей Active Directory (AD) на основе лицензий, которыми владеет ваша организация. 

![Исследуйте сигналы и при необходимости применять МИД](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Основываясь на наших исследованиях, ваша учетная запись более чем на 99,9% меньше вероятность быть скомпрометированы, если вы используете многофакторную аутентификацию (MFA).

Так как же ваша организация включить МИД даже бесплатно, прежде чем стать статистикой?

## <a name="free-option"></a>Бесплатный вариант

Клиенты, которые используют бесплатные преимущества Azure AD, могут использовать [по умолчанию для](../fundamentals/concept-fundamentals-security-defaults.md) включения многофакторной аутентификации в своей среде.

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 Business Premium, E3 или E5

Для клиентов с Office 365 существует два варианта:

* Multi-факторная аутентификация Azure включена или отключена для всех пользователей для всех событий, вскакиваемых в систему. Не существует возможности включить многофакторную аутентификацию только для подмножества пользователей или только при определенных сценариях. Управление ведется через портал Office 365. 
* Для улучшения пользовательского опыта продикируйтесь до Azure AD Premium P1 или P2 и используйте Условный доступ. Для получения дополнительной информации см.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Для клиентов с Azure AD Premium P1 или аналогичных лицензий, которые включают в себя эту функциональность, такие как Корпоративная мобильность и безопасность E3, Microsoft 365 F1, или Microsoft 365 E3: 

Используйте [Azure AD Conditional Access](../conditional-access/overview.md) для запроса пользователей для многофакторной аутентификации во время определенных сценариев или событий в соответствии с вашими бизнес-требованиями.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Для клиентов с Azure AD Premium P2 или аналогичных лицензий, которые включают в себя эту функциональность, такие как Корпоративная мобильность и безопасность E5 или Microsoft 365 E5: 

Обеспечивает наиболее сильное положение безопасности и улучшенный пользовательский опыт. Добавляет [риск-ориентированный условный доступ](../conditional-access/howto-conditional-access-policy-risk.md) к функциям Azure AD Premium P1, которые адаптируются к шаблонам пользователя и минимизируют многофакторные запросы аутентификации.

## <a name="authentication-methods"></a>Методы проверки подлинности

|   | Параметры безопасности по умолчанию | Все остальные методы |
| --- | --- | --- |
| Уведомление в мобильном приложении | X | X |
| Код проверки из мобильного приложения или токен оборудования |   | X |
| SMS на телефон |   | X |
| Звонок на телефон |   | X |

## <a name="next-steps"></a>Дальнейшие действия

Чтобы начать работу, смотрите учебник для [обеспечения безопасности событий, вскакиваемых пользователем, с помощью Azure Multi-Factor Authentication.](../authentication/tutorial-enable-azure-mfa.md)

Для получения дополнительной информации о лицензировании [см.](../authentication/concept-mfa-licensing.md)
