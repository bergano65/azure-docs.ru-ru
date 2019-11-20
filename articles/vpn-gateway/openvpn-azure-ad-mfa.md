---
title: 'Включение MFA для VPN-пользователей: проверка подлинности Azure AD | Документация Майкрософт'
description: Включение многофакторной проверки подлинности для VPN-пользователей
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: alzam
ms.openlocfilehash: 1b506f8439deabd110daaefab36442140e20d0d4
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185359"
---
# <a name="enable-multi-factor-authentication-mfa-for-vpn-users"></a>Включение многофакторной проверки подлинности (MFA) для VPN-пользователей

Вы можете включить MFA для пользователей в клиенте Azure AD, чтобы пользователям было предложено выполнить вторую проверку подлинности перед предоставлением доступа.

> [!NOTE]
> Предварительные требования. вы настроили клиент Azure AD, как описано в документе "Настройка клиента".
>

### <a name="tenant"></a>1. Войдите в портал Azure и перейдите к **Azure Active Directory** , **всем пользователям** и щелкните **многофакторную идентификацию** .


   ![Новый клиент Azure AD](./media/openvpn-azure-ad-mfa/mfa1.jpg)

### <a name="users"></a>2. Выберите пользователей, для которых требуется включить MFA, и нажмите кнопку **включить** .

   ![Новый клиент Azure AD](./media/openvpn-azure-ad-mfa/mfa2.jpg)

### <a name="enable-authentication"></a>3. Перейдите в раздел **Azure Active Directory** , **корпоративные приложения**, **все приложения** и щелкните **Azure VPN** .


   ![Идентификатор каталога](./media/openvpn-azure-ad-mfa/user1.jpg)

### <a name="users"></a>4. Убедитесь, что **включены пользователи для входа** в систему? параметр имеет значение Да. Если вы хотите, чтобы только пользователи с разрешениями на VPN-подключение к Azure могли выполнять вход, задайте для параметра **Назначение пользователя** значение Да. в противном случае все пользователи в клиенте AD смогут успешно подключиться к VPN.

   ![Разрешения](./media/openvpn-azure-ad-mfa/user2.jpg)


## <a name="next-steps"></a>Дополнительная информация

Чтобы подключиться к виртуальной сети, необходимо создать и настроить профиль клиента VPN. См. раздел [Настройка VPN-клиента для P2S VPN-подключений](openvpn-azure-ad-client.md).
