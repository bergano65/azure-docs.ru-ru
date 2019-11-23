---
title: Manage users and devices Azure MFA - Azure Active Directory
description: How can administrators change user settings such as forcing the users to do the proof-up process again.
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
ms.openlocfilehash: 5fd03ea807e48f6f0e287bb4497e4d20268995db
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404162"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Управление параметрами пользователей с помощью Многофакторной идентификации Azure в облаке

Как администратор вы можете управлять следующими параметрами пользователей и устройств.

* Параметр "Требовать у выбранных пользователей сообщать о способах связи еще раз"
* Удалять пароли приложений.
* Требовать Многофакторную идентификацию на всех доверенных устройствах.

## <a name="manage-authentication-methods"></a>Manage authentication methods

As an administrator assigned the Authentication Administrator role you can require users to reset their password, re-register for MFA, or revoke existing MFA sessions from their user object.

![Manage authentication methods from the Azure portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. Войдите на [портале Azure](https://portal.azure.com).
1. Слева выберите **Azure Active Directory** > **Пользователи** > **Все пользователи**.
1. Choose the user you wish to perform an action on and select **Authentication methods**.
   - **Reset Password** will reset the user's password and assign a temporary password that must be changed on the next sign in.
   - **Require Re-register MFA** will make it so that when the user signs in next time, they will be requested to setup a new MFA authentication method.
   - **Revoke MFA Sessions** clears the user's remembered MFA sessions and requires them to perform MFA the next time it is required by the policy on the device.

## <a name="delete-users-existing-app-passwords"></a>Удалять существующие пароли приложений пользователей

Этот параметр удаляет все созданные пользователем пароли приложений. Работа не использующих браузер приложений, связанных с этими паролями, приостанавливается, пока не будут созданы новые пароли. Global administrator permissions are required to perform this action.

### <a name="how-to-delete-users-existing-app-passwords"></a>Как удалить существующие пароли приложений пользователей

1. Войдите на [портале Azure](https://portal.azure.com).
2. Слева выберите **Azure Active Directory** > **Пользователи** > **Все пользователи**.
3. Справа на панели инструментов выберите **Многофакторная идентификация**. Откроется страница Многофакторной идентификации.
4. Установите флажок рядом с именем пользователя или пользователей, которыми требуется управлять. A list of quick step options appears on the right.
5. Выберите **Управление параметрами пользователя**.
6. Установите флажок **Удалить все существующие пароли приложений, созданные выбранными пользователями**.
   ![Delete all existing app passwords](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Щелкните **Сохранить**.
8. Щелкните **Закрыть**.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения см. в разделе о [сохранении данных проверки подлинности для устройств, которым доверяют пользователи](howto-mfa-mfasettings.md).
- Если пользователям требуется помощь, попросите их ознакомиться со статьей [Что для меня означает Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md)
