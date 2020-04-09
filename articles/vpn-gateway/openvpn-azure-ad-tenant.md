---
title: 'VPN Шлюз: Арендатор Azure AD для VPN-соединений P2S: аутентификация Azure AD'
description: Вы можете использовать P2S VPN для подключения к VNet с помощью аутентификации Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: alzam
ms.openlocfilehash: 6f7558a7d382d13c822862b881e310d3a5aaad0f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879417"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Создание для подключения протоколов P2S OpenVPN для создания элемента «Активный каталог» для

При подключении к VNet можно использовать проверку подлинности на основе сертификатов или проверку подлинности RADIUS. Однако при использовании протокола Open VPN можно также использовать аутентификацию Azure Active Directory. Эта статья поможет настроить арендаторAz AD для проверки подлинности P2S Open VPN.

> [!NOTE]
> Проверка подлинности Azure AD поддерживается только для подключений по протоколу OpenVPN®.
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Проверить арендатора Azure AD

Убедитесь, что у вас есть арендатор Azure AD. Если у вас нет арендатора Azure AD, можно создать его, используя шаги в новой статье [арендатора:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Организационное название
* Первоначальное доменное имя

Пример

   ![Новый арендатор Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Создание пользователей-арендаторов Azure AD

Вашему атмеру Azure AD нужны следующие учетные записи: учетная запись Глобального админина и главная учетная запись пользователя. Главная учетная запись пользователя используется в качестве основной учетной записи встраивания (учетная запись обслуживания). При создании учетной записи пользователя-арендатора Azure AD можно настроить роль каталога для типа пользователя, который вы хотите создать.

Используйте шаги в [этой статье,](../active-directory/fundamentals/add-users-azure-active-directory.md) чтобы создать по крайней мере двух пользователей для арендатора Azure AD. Обязательно измените **роль каталога** для создания типов учетных записей:

* глобальный администратор.
* Пользователь

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Включить аутентификацию Azure AD на VPN шлюзе

1. Найдите идентификатор каталога, который вы хотите использовать для проверки подлинности. Он указан в разделе свойств страницы Active Directory.

    ![Идентификатор каталога](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Скопируйте идентификатор каталога.

3. Вопиюсь на портал Azure в качестве пользователя, которым назначена роль **глобального администратора.**

4. Далее, дать согласие админа. Копируйте и вставьте URL-адрес, относящихся к местоположению развертывания в адресной строке браузера:

    Public

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure для государственных организаций

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Выберите учетную запись **Глобального адиме,** если это вызвано.

    ![Идентификатор каталога](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Выберите **Принять** при запросе.

    ![Принять](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. В Azure AD в **корпоративных приложениях**вы видите в списке **VPN Azure.**

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Если у вас нет работающей среды "точка — сеть", следуйте инструкциям, чтобы создать ее. См. дополнительные сведения о [создании и настройке VPN-подключения "точка — сеть" с использованием аутентификации на основе собственного сертификата Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 

    > [!IMPORTANT]
    > SKU уровня "Базовый" не поддерживается для OpenVPN.

9. Включите аутентификацию Azure AD на шлюзе VPN, запустив следующие команды, будучи уверенным в изменении команды, чтобы отразить собственную среду:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>/" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > Убедитесь, что вы включаете задний `AadIssuerUri` слэш в конце значения. В противном случае команда выйдет из строя.

10. Создайте и загрузите профиль, запустив следующие команды. Измените значения -ResourceGroupName и -Name, чтобы соответствовать вашим собственным.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. После выполнения команд, вы видите результат похож на один ниже. Копируйте URL-адрес результата в браузер, чтобы загрузить файл застежки-молнии профиля.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. Извлеките загруженный файл застежки-молнии.

13. Просмотрите незасеченную папку "AzureVPN".

14. Обратите внимание на расположение файла "azurevpnconfig.xml". Azurevpnconfig.xml содержит настройки для VPN-соединения и может быть импортирован непосредственно в приложение Azure VPN Client. Вы также можете распространить этот файл среди всех пользователей, которым необходимо подключиться по электронной почте или другими способами. Пользователю понадобятся действительные учетные данные Azure AD для успешного подключения.

## <a name="next-steps"></a>Дальнейшие действия

Для подключения к виртуальной сети необходимо создать и настроить профиль VPN-клиента. Смотрите [Налаживание VPN-клиента для VPN-соединений P2S.](openvpn-azure-ad-client.md)
