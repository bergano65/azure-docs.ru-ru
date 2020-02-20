---
title: 'Включение MFA для VPN-пользователей: проверка подлинности Azure AD'
description: Включение многофакторной проверки подлинности для VPN-пользователей
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: alzam
ms.openlocfilehash: 34ef1b73b06870fd4eaabe88147cd98b281c1f11
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472355"
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

Чтобы подключиться к виртуальной сети, необходимо создать и настроить профиль клиента VPN. См. раздел [Настройка VPN-клиента для P2S VPN-подключений](openvpn-azure-ad-client.md).