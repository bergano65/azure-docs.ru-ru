---
title: 'Включение MFA для VPN-пользователей: проверка подлинности Azure AD'
description: Включение многофакторной проверки подлинности для VPN-пользователей
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: b22581d012b2c69081bc7b4eee093227c060b4c2
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169711"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Включение многофакторной идентификации Azure (MFA) для VPN-пользователей

Если вы хотите, чтобы пользователи запрашивают второй фактор проверки подлинности перед предоставлением доступа, вы можете настроить многофакторную идентификацию Azure (MFA) для вашего клиента Azure AD. Действия, описанные в этой статье, помогут включить двухфакторную проверку подлинности.

## <a name="prereq"></a>Готовности к установке

Необходимым условием для этой конфигурации является настроенный клиент Azure AD, выполнив действия, описанные в разделе [Настройка клиента](openvpn-azure-ad-tenant.md).

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="enablesign"></a>Настройка параметров входа

На странице **Azure VPN — свойства** настройте параметры входа.

1. Задайте для параметра **включить вход пользователей** значение **Да**. Это позволит всем пользователям в клиенте AD успешно подключаться к VPN.
2. Задать **Назначение пользователя?** чтобы ограничить вход только пользователями, имеющими разрешения на VPN-подключение к Azure, выберите **"Да"** .
3. Сохраните изменения.

   ![Разрешения](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Дальнейшие действия

Чтобы подключиться к виртуальной сети, необходимо создать и настроить профиль клиента VPN. См. раздел [Настройка VPN-клиента для P2S VPN-подключений](openvpn-azure-ad-client.md).
