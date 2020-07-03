---
title: 'VPN-шлюз: клиент Azure AD для разных групп пользователей: проверка подлинности Azure AD'
description: VPN-подключение P2S можно использовать для подключения к виртуальной сети с помощью проверки подлинности Azure AD.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77485722"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Создание клиента Azure Active Directory для подключений по протоколу P2S Опенвпн

При подключении к виртуальной сети можно использовать проверку подлинности на основе сертификатов или аутентификацию RADIUS. Однако при использовании открытого протокола VPN можно также использовать проверку подлинности Azure Active Directory. Если вы хотите, чтобы другой набор пользователей мог подключаться к разным VPN-шлюзам, можно зарегистрировать несколько приложений в AD и связать их с разными VPN-шлюзами. Эта статья поможет вам настроить клиент Azure AD для проверки подлинности P2S Опенвпн и создать и зарегистрировать несколько приложений в Azure AD, чтобы разрешить разные права доступа для разных пользователей и групп.

> [!NOTE]
> Проверка подлинности Azure AD поддерживается только для подключений по протоколу OpenVPN®.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Включение проверки подлинности на шлюзе

На этом шаге вы включите проверку подлинности Azure AD на VPN-шлюзе.

1. Включите проверку подлинности Azure AD на VPN-шлюзе, выполнив следующие команды. Не забудьте изменить команды в соответствии со своей средой:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Не используйте идентификатор приложения клиента VPN Azure в приведенных выше командах. он предоставит всем пользователям доступ к VPN-шлюзу. Используйте идентификатор зарегистрированного приложения.

2. Создайте и скачайте профиль, выполнив следующие команды. Измените значения-Ресауркграупнаме и-Name в соответствии со своими собственными.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. После выполнения команд вы увидите результат, аналогичный приведенному ниже. Скопируйте полученный URL-адрес в браузер, чтобы скачать его.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Извлеките скачанный ZIP-файл.

5. Перейдите к распакованной папке "AzureVPN".

6. Запишите расположение файла "азуревпнконфиг. XML". Азуревпнконфиг. XML содержит параметр для VPN-подключения и может быть импортирован непосредственно в клиентское приложение VPN Azure. Этот файл также можно передать всем пользователям, которым требуется подключение по электронной почте или другим средствам. Для успешного подключения пользователю понадобятся действительные учетные данные Azure AD.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы подключиться к виртуальной сети, необходимо создать и настроить профиль клиента VPN. См. раздел [Настройка VPN-клиента для P2S VPN-подключений](openvpn-azure-ad-client.md).
