---
title: 'Клиент Azure AD для VPN-подключений пользователей: проверка подлинности Azure AD'
description: Для подключения к виртуальной сети с помощью проверки подлинности Azure AD можно использовать VPN-подключение пользователя виртуальной глобальной сети Azure ("точка-сеть").
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 76c65d194d03dd1b7ff4cc2f3b45d84ff7909968
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753354"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Создание клиента Azure Active Directory для подключений по протоколу VPN Опенвпн User

При подключении к виртуальной сети можно использовать проверку подлинности на основе сертификатов или аутентификацию RADIUS. Однако при использовании открытого протокола VPN можно также использовать проверку подлинности Azure Active Directory. Эта статья поможет вам настроить клиент Azure AD для VPN-подключения пользователя виртуальной глобальной сети (точка-сеть) с открытой проверкой подлинности VPN.

> [!NOTE]
> Аутентификация Azure AD поддерживается только для &reg; подключений по протоколу опенвпн.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Создание клиента Azure AD

Создайте клиента Azure AD, выполнив действия, описанные в статье [Создание нового клиента](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Имя организации
* Первоначальное доменное имя

Пример

   ![Новый клиент Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Создание пользователей клиента Azure AD

Затем создайте две учетные записи пользователей. Создайте одну учетную запись глобального администратора и одну учетную запись главного пользователя. Учетная запись главного пользователя используется как главная учетная запись внедрения (учетная запись службы). При создании учетной записи пользователя клиента Azure AD необходимо настроить роль каталога под тип пользователя, который требуется создать.

Выполните действия, описанные в [этой статье](../active-directory/fundamentals/add-users-azure-active-directory.md), чтобы создать по крайней мере двух пользователей для клиента Azure AD. Не забудьте изменить **Роль каталога**, чтобы создать типы учетных записей:

* глобальный администратор.
* Пользователь

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Включите проверку подлинности Azure AD на VPN-шлюзе.

1. Укажите идентификатор каталога, который хотите использовать для проверки подлинности. Он указан в разделе свойств страницы Active Directory.

    ![Идентификатор каталога](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Скопируйте идентификатор каталога.

3. Войдите на портал Azure как пользователь с ролью **Глобального администратора**.

4. Затем дайте согласие администратора. Скопируйте и вставьте URL-адрес, относящийся к расположению развертывания, в адресную строку браузера:

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

5. При появлении запроса выберите учетную запись **Глобальный администратор**.

    ![Идентификатор каталога](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Выберите **Принять** при появлении соответствующего запроса.

    ![Принять](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. В Azure AD в разделе **корпоративные приложения**вы увидите список **VPN Azure** .

    ![VPN Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Настройте аутентификацию Azure AD для VPN пользователя и назначьте ее виртуальному концентратору, выполнив действия, описанные в статье [Настройка проверки подлинности Azure AD для подключения типа "точка — сеть" к Azure](virtual-wan-point-to-site-azure-ad.md) .

## <a name="next-steps"></a>Дальнейшие шаги

Чтобы подключиться к виртуальной сети, необходимо создать и настроить профиль VPN-клиента и связать его с виртуальным концентратором. См. статью [Настройка аутентификации Azure AD для подключения "точка — сеть" к Azure](virtual-wan-point-to-site-azure-ad.md).
