---
title: 'VPN-шлюз: клиент Azure AD для VPN-подключений P2S: проверка подлинности Azure AD'
description: VPN-подключение P2S можно использовать для подключения к виртуальной сети с помощью проверки подлинности Azure AD.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: alzam
ms.openlocfilehash: 6df8a9448873b418dc312ba572ba15d2da69bf32
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74822725"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Создание клиента Azure Active Directory для подключений по протоколу P2S Опенвпн

При подключении к виртуальной сети можно использовать проверку подлинности на основе сертификатов или аутентификацию RADIUS. Однако при использовании открытого протокола VPN можно также использовать проверку подлинности Azure Active Directory. Эта статья поможет вам настроить клиент Azure AD для проверки подлинности P2S Open VPN.

> [!NOTE]
> Аутентификация Azure AD поддерживается только для подключений по протоколу Опенвпн®.
>

## <a name="tenant"></a>1. Создайте клиент Azure AD.

Создайте клиент Azure AD, выполнив действия, описанные в статье [Создание нового клиента](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Название организации
* Имя первоначального домена

Пример:

   ![Новый клиент Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="users"></a>2. Создание пользователей клиента Azure AD

Затем создайте две учетные записи пользователей. Создайте одну учетную запись глобального администратора и одну учетную запись главного пользователя. Учетная запись главного пользователя используется как учетная запись главного внедрения (учетная запись службы). При создании учетной записи пользователя клиента Azure AD необходимо настроить роль каталога для типа пользователя, который требуется создать.

Выполните действия, описанные в [этой статье](../active-directory/fundamentals/add-users-azure-active-directory.md) , чтобы создать по крайней мере двух пользователей для вашего клиента Azure AD. Не забудьте изменить **роль каталога** , чтобы создать типы учетных записей:

* глобальный администратор.
* Пользователь

## <a name="enable-authentication"></a>3. Включите проверку подлинности Azure AD на VPN-шлюзе.

1. Укажите идентификатор каталога, который вы хотите использовать для проверки подлинности. Он указан в разделе Свойства страницы Active Directory.

    ![Идентификатор каталога](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Скопируйте идентификатор каталога.

3. Войдите в портал Azure в качестве пользователя, которому назначена роль **глобального администратора** .

4. Затем предоставьте администратору согласие. Скопируйте и вставьте URL-адрес, относящийся к расположению развертывания, в адресной строке браузера:

    Общедоступные

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. При появлении запроса выберите учетную запись **глобального администратора** .

    ![Идентификатор каталога](./media/openvpn-create-azure-ad-tenant/pick.png)

6. При появлении запроса выберите **принять** .

    ![Принять](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. В Azure AD в разделе **корпоративные приложения**вы увидите список **VPN Azure** .

    ![VPN Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Включите проверку подлинности Azure AD на VPN-шлюзе, выполнив следующие команды, и вы обязательно измените команду, чтобы она отражала собственную среду:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/"
    ```

9. Создайте и скачайте профиль, выполнив следующие команды. Измените значения-Ресауркграупнаме и-Name в соответствии со своими собственными.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

10. После выполнения команд вы увидите результат, аналогичный приведенному ниже. Скопируйте полученный URL-адрес в браузер, чтобы скачать его.

    ![VPN Azure](./media/openvpn-create-azure-ad-tenant/profile.png)

11. Извлеките скачанный ZIP-файл.

12. Перейдите к распакованной папке "AzureVPN".

13. Запишите расположение файла "азуревпнконфиг. XML". Азуревпнконфиг. XML содержит параметр для VPN-подключения и может быть импортирован непосредственно в клиентское приложение VPN Azure. Этот файл также можно передать всем пользователям, которым требуется подключение по электронной почте или другим средствам. Для успешного подключения пользователю понадобятся действительные учетные данные Azure AD.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы подключиться к виртуальной сети, необходимо создать и настроить профиль клиента VPN. См. статью [Настройка аутентификации Azure AD для подключения "точка — сеть" к Azure](virtual-wan-point-to-site-azure-ad.md).
