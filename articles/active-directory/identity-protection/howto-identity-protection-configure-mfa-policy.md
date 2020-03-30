---
title: Настройка политики регистрации МИД - Azure Active Directory Identity Protection
description: Сведения о настройке политики регистрации с многофакторной проверкой подлинности в службе "Защита идентификации Azure Active Directory".
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382144"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Как: Настройка полиса регистрации многофакторной аутентификации Azure

Azure AD Identity Protection поможет вам управлять развертыванием регистрации многофакторной аутентификации Azure ( MFA), настраивая политику условного доступа, требующую регистрации МИД независимо от того, в какое современное приложение аутентификации вы подписываетесь.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Что такое политика регистрации многофакторной аутентификации Azure?

Multi-Factor Authentication Azure предоставляет средства для проверки того, кто использует не только имя пользователя и пароль. Он обеспечивает второй уровень безопасности для пользователей, вскакиваемых. Для того, чтобы пользователи могли отвечать на запросы МИД, они должны сначала зарегистрироваться для azure Multi-Factor Authentication.

Мы рекомендуем вам потребовать многофакторную аутентификацию Azure для вхинга пользователей, поскольку это:

- Обеспечивает сильную аутентификацию с помощью ряда вариантов проверки.
- Играет ключевую роль в подготовке организации к самостоятельной исправления рисков в области защиты личных данных.

Для получения дополнительной информации о многофакторной аутентификации Azure [см.](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Настройки политики

1. Перейдите на [портал Azure](https://portal.azure.com).
1. Просмотрите политику регистрации **данных Azure Active Directory** > **Security** > **Identity Protection** > **MFA.**
   1. Под **заданиями**
      1. **Пользователи** - Выберите **всех пользователей** или **отдельных лиц и групп,** если ограничьте развертывание.
         1. По желанию можно исключить пользователей из политики.
   1. Под **контролем**
      1. Убедитесь, что флажок **Требует регистрации Azure MFA** проверен и выберите **Select.**
   1. **Обеспечить соблюдение политики** - **On**
   1. **Сохранить**

## <a name="user-experience"></a>Возможности для пользователя

Azure Active Directory Identityy Protection подскажет пользователям зарегистрироваться при следующем входе в систему в интерактивном режиме, и у них будет 14 дней для завершения регистрации. В течение этого 14-дневного периода они могут обойти регистрацию, но в конце периода они должны будут зарегистрироваться, прежде чем они смогут завершить процесс вхинга.

Общие сведения о соответствующем взаимодействии с пользователями см. в статьях:

- [Процедуры входа с защитой идентификации Azure AD](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Дальнейшие действия

- [Включить политики ввески и рисков для пользователей](howto-identity-protection-configure-risk-policies.md)

- [Включить сбросить пароля самообслуживания Azure AD](../authentication/howto-sspr-deployment.md)

- [Включение многофакторной проверки подлинности в Azure](../authentication/howto-mfa-getstarted.md)
