---
title: 'VPN Шлюз: арендатор Azure AD для различных групп пользователей: аутентификация Azure AD'
description: Вы можете использовать P2S VPN для подключения к VNet с помощью аутентификации Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485722"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Создание для подключения протоколов P2S OpenVPN для создания элемента «Активный каталог» для

При подключении к VNet можно использовать проверку подлинности на основе сертификатов или проверку подлинности RADIUS. Однако при использовании протокола Open VPN можно также использовать аутентификацию Azure Active Directory. Если вы хотите, чтобы различные пользователи могли подключиться к различным VPN шлюзам, вы можете зарегистрировать несколько приложений в AD и связать их с различными VPN шлюзами. Эта статья поможет настроить наемние Azure AD для проверки подлинности P2S OpenVPN и создать и зарегистрировать несколько приложений в Azure AD для обеспечения разного доступа для разных пользователей и групп.

> [!NOTE]
> Проверка подлинности Azure AD поддерживается только для подключений по протоколу OpenVPN®.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Включить аутентификацию на шлюзе

На этом этапе вы включите аутентификацию Azure AD на шлюзе VPN.

1. Включите аутентификацию Azure AD на шлюзе VPN, запустив следующие команды. Не забудьте изменить команды, чтобы отразить вашу собственную среду:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Не используйте идентификатор приложения клиента Azure VPN в приведенных выше командах: он предоставит всем пользователям доступ к VPN шлюзу. Используйте идентификатор зарегистрированного приложения.

2. Создайте и загрузите профиль, запустив следующие команды. Измените значения -ResourcGroupName и -Name, чтобы соответствовать вашим собственным.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. После выполнения команд, вы видите результат похож на один ниже. Копируйте URL-адрес результата в браузер, чтобы загрузить файл застежки-молнии профиля.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Извлеките загруженный файл застежки-молнии.

5. Просмотрите незасеченную папку "AzureVPN".

6. Обратите внимание на расположение файла "azurevpnconfig.xml". Azurevpnconfig.xml содержит настройки для VPN-соединения и может быть импортирован непосредственно в приложение Azure VPN Client. Вы также можете распространить этот файл среди всех пользователей, которым необходимо подключиться по электронной почте или другими способами. Пользователю понадобятся действительные учетные данные Azure AD для успешного подключения.

## <a name="next-steps"></a>Дальнейшие действия

Для подключения к виртуальной сети необходимо создать и настроить профиль VPN-клиента. Смотрите [Налаживание VPN-клиента для VPN-соединений P2S.](openvpn-azure-ad-client.md)
