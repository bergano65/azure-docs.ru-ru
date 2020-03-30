---
title: 'Арендатор Azure AD для VPN-соединений пользователя: аутентификация Azure AD'
description: Для подключения к VNet можно использовать vpn Virtual WAN User (точка к сайту) с помощью аутентификации Azure AD
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 74347ce969b6a5ffd57f5ca8396517e78590f3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059460"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Создание наниматель аниме Active Directory Azure для подключения к протоколу user VPN OpenVPN

При подключении к VNet можно использовать проверку подлинности на основе сертификатов или проверку подлинности RADIUS. Однако при использовании протокола Open VPN можно также использовать аутентификацию Azure Active Directory. Эта статья поможет вам настроить арендатор Azure AD для виртуального VPN ПОЛЬЗОВАТЕЛЯ WAN (точка к сайту) Открытая проверка подлинности VPN.

> [!NOTE]
> Аутентификация Azure AD&reg; поддерживается только для подключений к протоколам OpenVPN.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Создание арендатора Azure AD

Создайте aD-арендатора Azure, используя шаги в новой статье [для арендатора:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Организационное название
* Первоначальное доменное имя

Пример

   ![Новый арендатор Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Создание пользователей-арендаторов Azure AD

Затем создайте две учетные записи пользователей. Создайте одну учетную запись Global Admin и одну учетную запись пользователя. Главная учетная запись пользователя используется в качестве основной учетной записи встраивания (учетная запись обслуживания). При создании учетной записи пользователя-арендатора Azure AD можно настроить роль каталога для типа пользователя, который вы хотите создать.

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Выберите учетную запись **Глобального адиме,** если это вызвано.

    ![Идентификатор каталога](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Выберите **Принять** при запросе.

    ![Принять](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. В Azure AD в **корпоративных приложениях**вы видите в списке **VPN Azure.**

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Настройте аутентификацию Azure AD для VPN пользователя и присвоите ее виртуальному концентратору, выявив шаги в [настройке аутентации Azure AD для подключения к сайту Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>Дальнейшие действия

Для подключения к виртуальной сети необходимо создать и настроить профиль КЛИЕНТА VPN и связать его с Virtual Hub. Смотрите [настройку аутентификации Azure AD для подключения к сайту от точки к сайту с Azure.](virtual-wan-point-to-site-azure-ad.md)
