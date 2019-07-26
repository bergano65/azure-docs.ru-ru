---
title: Настройка политики регистрации с многофакторной проверкой подлинности в службе "Защита идентификации Azure Active Directory" | Документация Майкрософт
description: Сведения о настройке политики регистрации с многофакторной проверкой подлинности в службе "Защита идентификации Azure Active Directory".
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27ff7512bb3f9422ed4c8edd7ab50fce23f0ed07
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499562"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Как Настройка политики регистрации многофакторной идентификации Azure

Защита идентификации Azure AD помогает управлять развертыванием регистрации многофакторной проверки подлинности (MFA), настраивая политику условного доступа, чтобы требовать регистрацию MFA независимо от того, в какое приложение для проверки подлинности вы входите в систему. В этой статье объясняется, как можно использовать политику и как ее настроить.



## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Что такое политика регистрации многофакторной идентификации Azure?

Многофакторная идентификация Azure предоставляет средства для проверки того, кто использует не только имя пользователя и пароль. Он обеспечивает второй уровень безопасности при входе пользователей в систему. Чтобы пользователи могли отвечать на запросы MFA, они должны сначала зарегистрироваться для использования многофакторной идентификации Azure.

Мы рекомендуем требовать многофакторную идентификацию Azure для входа пользователей, так как это:

- обеспечивает строгую аутентификацию с помощью ряда простых параметров проверки;
- Играет ключевую роль в подготовке Организации к защите и восстановлению событий риска в защите идентификации.

Дополнительные сведения о MFA см. в статье [что такое многофакторная идентификация Azure?](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>Разделы справки получить доступ к политике регистрации?

Политика регистрации с многофакторной проверкой подлинности указывается в разделе **Настройка** страницы [Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![Политика MFA](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Параметры политики

При настройке политики регистрации MFA необходимо внести следующие изменения в конфигурацию:

- Пользователи и группы, к которым применяется политика. Не забудьте исключить учетные [записи аварийного доступа](../users-groups-roles/directory-emergency-access.md)Организации.

    ![Пользователи и группы](./media/howto-mfa-policy/11.png)

- Элемент управления, который требуется применить. **требуется регистрация Azure MFA**

    ![Доступ](./media/howto-mfa-policy/12.png)

- Для параметра включить политику следует задать значение **вкл**.

    ![Принудительное применение политики](./media/howto-mfa-policy/14.png)

- **Сохранение** политики

## <a name="user-experience"></a>Взаимодействие с пользователем

Защита идентификации Azure Active Directory предложит пользователям регистрироваться при следующем входе в интерактивном режиме.

Общие сведения о соответствующем взаимодействии с пользователями см. в статьях:

- [Регистрация с многофакторной проверкой подлинности](flows.md#multi-factor-authentication-registration).  
- [Процедуры входа с защитой идентификации Azure AD](flows.md).  

## <a name="next-steps"></a>Следующие шаги

См. дополнительные сведения о [защите идентификации Azure AD](overview.md).
