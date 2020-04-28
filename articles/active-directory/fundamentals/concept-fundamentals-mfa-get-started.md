---
title: Многофакторная идентификация Azure для вашей организации — Azure Active Directory
description: Сведения о доступных функциях многофакторной идентификации Azure для вашей организации в зависимости от модели лицензии
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79530399"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Обзор многофакторной идентификации Azure для вашей организации

Существует несколько способов включить многофакторную идентификацию Azure для пользователей Azure Active Directory (AD) на основе лицензий, которыми владеет ваша организация. 

![Исследование сигналов и принудительное применение MFA при необходимости](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Исходя из наших исследований, при использовании многофакторной идентификации (MFA) ваша учетная запись более чем на 99,9% менее подвержена опасности.

Итак, как ваша организация включает MFA даже бесплатно, прежде чем стать статистикой?

## <a name="free-option"></a>Бесплатный вариант

Клиенты, которые используют бесплатные преимущества Azure AD, могут использовать [Параметры безопасности по умолчанию](../fundamentals/concept-fundamentals-security-defaults.md) для включения многофакторной проверки подлинности в своей среде.

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 Business Premium, E3 или «е»

Для клиентов с Office 365 существует два варианта:

* Многофакторная идентификация Azure может быть включена или отключена для всех пользователей для всех событий входа. Невозможно включить многофакторную проверку подлинности только для подмножества пользователей или только в определенных сценариях. Управление осуществляется через портал Office 365. 
* Для повышения удобства работы пользователей выполните обновление до Azure AD Premium P1 или P2 и используйте условный доступ. Дополнительные сведения см. в статье Защита ресурсов Office 365 с помощью многофакторной проверки подлинности.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Для клиентов с Azure AD Premium P1 или аналогичными лицензиями, которые включают такие функции, как Enterprise Mobility + Security E3, Microsoft 365 F1 или Microsoft 365 E3: 

Используйте [Условный доступ Azure AD](../conditional-access/overview.md) , чтобы запрашивать у пользователей многофакторную проверку подлинности в определенных сценариях или событиях в соответствии с бизнес-требованиями.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Для клиентов с Azure AD Premium P2 или аналогичных лицензий, которые включают такие функции, как Enterprise Mobility + Security/или Microsoft 365: 

Обеспечивает наивысшую степень безопасности и улучшает взаимодействие с пользователем. Добавляет [Условный доступ на основе рисков](../conditional-access/howto-conditional-access-policy-risk.md) к функциям Azure AD Premium P1, которые адаптируются к шаблонам пользователей и сокращают запросы многофакторной проверки подлинности.

## <a name="authentication-methods"></a>Методы проверки подлинности

|   | Параметры безопасности по умолчанию | Все остальные методы |
| --- | --- | --- |
| Уведомление в мобильном приложении | X | X |
| Код проверки из мобильного приложения или токен оборудования |   | X |
| SMS на телефон |   | X |
| Звонок на телефон |   | X |

## <a name="next-steps"></a>Дальнейшие действия

Чтобы приступить к работе, см. Руководство по [защите событий входа пользователей с помощью многофакторной идентификации Azure](../authentication/tutorial-enable-azure-mfa.md).

Дополнительные сведения о лицензировании см. в статье [функции и лицензии для службы многофакторной идентификации Azure](../authentication/concept-mfa-licensing.md).
