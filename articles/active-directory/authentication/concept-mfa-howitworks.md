---
title: How it works Azure MFA - Azure Active Directory
description: Многофакторная идентификация Azure защищает доступ к вашим данным и приложениям, а также предоставляет пользователям простой вход в систему.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1036d7e8aef29e3185452d5088e660d474726e4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381968"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Как это работает: служба Многофакторной идентификации Azure

Безопасность двухшаговой проверки подлинности заключается в многоуровневом подходе. Нарушения нескольких факторов проверки подлинности представляет нетривиальную задачу для злоумышленников. Даже если злоумышленнику удастся получить пароль пользователя, им нельзя воспользоваться без дополнительного метода проверки подлинности. Служба требует использования двух и более методов проверки подлинности следующего характера:

* Что-то, что известно вам (обычно это пароль).
* Что-то, что у вас есть (доверенное устройство, которое непросто дублируется, такое как телефон).
* Что-то, относящееся непосредственно к вам (биометрические данные).

<center>

![Conceptual authentication methods image](./media/concept-mfa-howitworks/methods.png)</center>

Служба Многофакторной идентификации Azure помогает защитить доступ к данным и приложениям, обеспечивая при этом удобство работы пользователей. Эта служба предлагает дополнительную защиту за счет дополнительного способа аутентификации и, используя ряд простых [методов проверки подлинности](concept-authentication-methods.md), обеспечивает строгую проверку. Направление пользователям запросов о необходимости Многофакторной идентификации зависит от настроек, заданных администратором.

## <a name="how-to-get-multi-factor-authentication"></a>Как получить службу Многофакторной идентификации Azure?

Служба Многофакторной идентификации Azure доступна в составе следующих предложений:

* **Azure Active Directory Premium** or **Microsoft 365 Business** - Full featured use of Azure Multi-Factor Authentication using Conditional Access policies to require multi-factor authentication.

* **Azure AD Free** or standalone **Office 365** licenses - Use pre-created [Conditional Access baseline protection policies](../conditional-access/concept-baseline-protection.md) to require multi-factor authentication for your users and administrators.

* **Глобальные администраторы Azure Active Directory**. Для защиты учетных записей глобальных администраторов доступны некоторые возможности службы Многофакторной идентификации Azure.

> [!NOTE]
> С 1 сентября 2018 года новые клиенты больше не могут приобрести Многофакторную идентификацию Azure как отдельное предложение. Многофакторная аутентификация по-прежнему будет предоставляться как функция в лицензиях Azure AD Premium.

## <a name="supportability"></a>Возможности поддержки

Так как большинство пользователей привыкло использовать для аутентификации только пароли, важно проинформировать всех пользователей в организации об этом процессе. Это поможет снизить вероятность того, что пользователи будут обращаться в службу технической поддержки для устранения незначительных проблем с MFA. Однако бывают ситуации, когда необходимо временно отключить MFA. Вот несколько рекомендаций по действиям для таких случаев.

* Обучите сотрудников службы технической поддержки тому, как действовать в случаях, когда пользователь не может войти в систему, так как не имеет доступа к своим методам проверки подлинности или они неправильно работают.
   * Using Conditional Access policies for Azure MFA Service, your support staff can add a user to a group that is excluded from a policy requiring MFA.
* Consider using Conditional Access named locations as a way to minimize two-step verification prompts. With this functionality, administrators can bypass two-step verification for users that are signing in from a secure trusted network location such as a network segment used for new user onboarding.
* Deploy [Azure AD Identity Protection](../active-directory-identityprotection.md) and trigger two-step verification based on risk detections.

## <a name="next-steps"></a>Дальнейшие действия

- [Step-by-step Azure Multi-Factor Authentication deployment](howto-mfa-getstarted.md)
