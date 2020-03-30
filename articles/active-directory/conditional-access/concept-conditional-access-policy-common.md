---
title: Общие политики условного доступа - Активный каталог Azure
description: Обычно используемые политики условного доступа для организаций
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20b699f0672b49dd2f947e0cf00d0ffcef7961e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295309"
---
# <a name="common-conditional-access-policies"></a>Распространенные политики условного доступа

[По умолчанию безопасности](../fundamentals/concept-fundamentals-security-defaults.md) отлично подходят для некоторых, но многие организации нуждаются в большей гибкости, чем они предлагают. Например, многим из них нужна возможность исключить определенные учетные записи, такие как их учетные записи экстренного доступа или разбивки, из политики условного доступа, требующие многофакторной аутентификации. Для этих организаций общие политики, упомянутые в этой статье, могут быть по-своему.

![Политики условного доступа на портале Azure](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Учетные записи для аварийного доступа

Более подробную информацию о учетных записях экстренного доступа и о том, почему они важны, можно найти в следующих статьях: 

* [Управление учетными записями для аварийного доступа в Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Создание устойчивой стратегии управления доступом с помощью Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Типичные политики, развернутые организациями

* [Блокировка устаревшей аутентификации](howto-conditional-access-policy-block-legacy.md)\*
* [Требуйте МИД для администраторов](howto-conditional-access-policy-admin-mfa.md)\*
* [Требуется МИД для управления Azure](howto-conditional-access-policy-azure-management.md)\*
* [Требуется МИД для всех пользователей](howto-conditional-access-policy-all-users-mfa.md)\*

\*Эти четыре политики, когда настроены вместе, будут имитировать функциональность, включенную [по умолчанию безопасности.](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="additional-policies"></a>Дополнительные политики

* [Условный доступ на основе рисков (требуется Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Запрос на доверенное расположение для регистрации MFA](howto-conditional-access-policy-registration.md)
* [Блокирование доступа по расположению](howto-conditional-access-policy-location.md)
* [Требование использовать соответствующее устройство](howto-conditional-access-policy-compliant-device.md)
* [Блокировать доступ, за исключением конкретных приложений](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Дальнейшие действия

- [Имитировать знак в поведении с помощью инструмента "Условный доступ, если".](troubleshoot-conditional-access-what-if.md)

- [Используйте режим только для отчетов для условного доступа для определения влияния новых политических решений.](concept-conditional-access-report-only.md)
