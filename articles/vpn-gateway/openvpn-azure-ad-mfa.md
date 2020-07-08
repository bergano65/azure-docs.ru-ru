---
title: 'Включение MFA для VPN-пользователей: проверка подлинности Azure AD'
description: Включение многофакторной проверки подлинности для VPN-пользователей
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/14/2020
ms.author: alzam
ms.openlocfilehash: 80a6b342990100b3e79cc8194722a6eb84080ef6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987295"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Включение многофакторной идентификации Azure (MFA) для VPN-пользователей

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>Включить проверку подлинности

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>Настройка параметров входа

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>Вариант 1. доступ на пользователя

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>Вариант 2. Условный доступ

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Дальнейшие шаги

Чтобы подключиться к виртуальной сети, необходимо создать и настроить профиль клиента VPN. См. раздел [Настройка VPN-клиента для P2S VPN-подключений](openvpn-azure-ad-client.md).