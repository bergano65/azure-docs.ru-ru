---
title: 'Включение MFA для VPN-пользователей: проверка подлинности Azure AD'
description: Включение многофакторной проверки подлинности для VPN-пользователей
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: alzam
ms.openlocfilehash: 13f012af95bb2b6098317e59e5293fb72804a6a6
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471591"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Включение многофакторной идентификации Azure (MFA) для VPN-пользователей

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enableauth"></a>Включить проверку подлинности

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="enablesign"></a>Настройка параметров входа

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="peruser"></a>Вариант 1. доступ на пользователя

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="conditional"></a>Вариант 2. Условный доступ

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Следующие шаги

Чтобы подключиться к виртуальной сети, необходимо создать и настроить профиль клиента VPN. См. статью [Настройка аутентификации Azure AD для подключения "точка — сеть" к Azure](virtual-wan-point-to-site-azure-ad.md).
