---
title: Уведомления защиты идентификации Azure Active Directory
description: Узнайте, как уведомления помогают в изучении проблем.
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
ms.openlocfilehash: 0c83aa6e476bbd898999fb6efe490c7847a809ff
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382154"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Уведомления защиты идентификации Azure Active Directory

Azure AD Identity Protection sends two types of automated notification emails to help you manage user risk and risk detections:

- Сообщение электронной почты об обнаружении пользователей в группе риска
- сообщение электронной почты еженедельного дайджеста.

В этой статье рассматриваются оба уведомления по электронной почте.

## <a name="users-at-risk-detected-email"></a>Сообщение электронной почты об обнаружении пользователей в группе риска

При обнаружении учетной записи в группе риска служба "Защита идентификации Azure Active Directory" создает уведомление по электронной почте с темой **Users at risk detected** (Обнаружены пользователи в группе риска). В таком электронном сообщении содержится ссылка на отчет **[Пользователи, находящиеся в группе риска](../reports-monitoring/concept-user-at-risk.md)** . Мы советуем немедленно проверить пользователей в группе риска.

The configuration for this alert allows you to specify at what user risk level you want the alert to be generated. The email will be generated when the user's risk level reaches what you have specified; however, you will not receive new users at risk detected email alerts for this user after they move to this user risk level. For example, if you set the policy to alert on medium user risk and your user John moves to medium risk, you will receive the users at risk detected email for John. However, you will not receive a second user at risk detected alert if John then moves to high risk or has additional risk detections.

![Сообщение электронной почты об обнаружении пользователей в группе риска](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configure users at risk detected alerts

Как администратор вы можете настроить:

- **The user risk level that triggers the generation of this email** - By default, the risk level is set to “High” risk.
- **Получателей.** По умолчанию это письмо отправляется всем глобальным администраторам. Глобальные администраторы также могут добавить в список получателей других глобальных администраторов, администраторов безопасности и читателей сведений о безопасности.
   - Optionally you can **Add additional emails to receive alert notifications** this feature is a preview and users defined must have the appropriate permissions to view the linked reports in the Azure portal.

Configure the users at risk email in the **Azure portal** under **Azure Active Directory** > **Security** > **Identity Protection** > **Users at risk detected alerts**.

## <a name="weekly-digest-email"></a>сообщение электронной почты еженедельного дайджеста.

The weekly digest email contains a summary of new risk detections.  
Сюда входят:

- Пользователи, подверженные риску
- подозрительные действия;
- обнаруженные уязвимости;
- ссылки на связанные отчеты в службе защиты идентификации.

![сообщение электронной почты еженедельного дайджеста.](./media/howto-identity-protection-configure-notifications/400.png)

By default, recipients include all Global Admins. Глобальные администраторы также могут добавить в список получателей других глобальных администраторов, администраторов безопасности и читателей сведений о безопасности.

### <a name="configure-weekly-digest-email"></a>Configure weekly digest email

As an administrator, you can switch sending a weekly digest email on or off and choose the users assigned to receive the email.

Configure the weekly digest email in the **Azure portal** under **Azure Active Directory** > **Security** > **Identity Protection** > **Weekly digest**.

## <a name="see-also"></a>Дополнительные материалы

- [Защита идентификации Azure Active Directory.](../active-directory-identityprotection.md)
