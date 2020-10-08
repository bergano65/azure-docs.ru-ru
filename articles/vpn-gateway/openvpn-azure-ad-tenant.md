---
title: 'VPN-шлюз: клиент Azure AD для VPN-подключений P2S: проверка подлинности Azure AD'
description: Узнайте, как настроить клиент Azure AD для проверки подлинности P2S Open VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 9ee5959c124636e64ef73b901fbc461f36cd27f9
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91817298"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Создание клиента Azure Active Directory для подключений "точка — сеть" по протоколу OpenVPN

При подключении к виртуальной сети можно использовать проверку подлинности на основе сертификатов или аутентификацию RADIUS. Однако при использовании открытого протокола VPN можно также использовать проверку подлинности Azure Active Directory. Эта статья поможет вам настроить клиент Azure AD для проверки подлинности P2S Open VPN.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Проверка клиента Azure AD

Убедитесь, что у вас есть клиент Azure AD. Если у вас нет клиента Azure AD, можно создать его, выполнив действия, описанные в статье [Создание нового клиента](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Имя организации
* Первоначальное доменное имя

Пример

   ![Новый клиент Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Создание пользователей клиента Azure AD

Клиент Azure AD должен иметь следующие учетные записи: учетную запись глобального администратора и учетную запись главного пользователя. Учетная запись главного пользователя используется как главная учетная запись внедрения (учетная запись службы). При создании учетной записи пользователя клиента Azure AD необходимо настроить роль каталога под тип пользователя, который требуется создать.

Выполните действия, описанные в [этой статье](../active-directory/fundamentals/add-users-azure-active-directory.md), чтобы создать по крайней мере двух пользователей для клиента Azure AD. Не забудьте изменить **Роль каталога**, чтобы создать типы учетных записей:

* глобальный администратор.
* Пользователь

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Включите проверку подлинности Azure AD на VPN-шлюзе.

1. Укажите идентификатор каталога, который хотите использовать для проверки подлинности. Он указан в разделе свойств страницы Active Directory.

    ![Снимок экрана, на котором показана страница "свойства каталога" с выделенным элементом "идентификатор каталога".](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Скопируйте идентификатор каталога.

3. Войдите на портал Azure как пользователь с ролью **Глобального администратора**.

4. Затем дайте согласие администратора. Скопируйте и вставьте URL-адрес, относящийся к расположению развертывания, в адресную строку браузера:

    Общедоступные

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure для государственных организаций

    ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. При появлении запроса выберите учетную запись **Глобальный администратор**.

    ![Идентификатор каталога](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Выберите **Принять** при появлении соответствующего запроса.

    ![На снимке экрана показаны разрешения на сообщения, запрошенные для вашей организации, с подробностями и параметром для принятия.](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. В Azure AD в разделе **корпоративные приложения**вы увидите список **VPN Azure** .

    ![Снимок экрана, на котором показана страница "все приложения".](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Если у вас нет работающей среды "точка — сеть", следуйте инструкциям, чтобы создать ее. См. раздел [Создание VPN-подключения](vpn-gateway-howto-point-to-site-resource-manager-portal.md) типа "точка — сеть" для создания и настройки VPN-шлюза типа "точка — сеть". 

    > [!IMPORTANT]
    > SKU уровня "Базовый" не поддерживается для OpenVPN.

9. Включите проверку подлинности Azure AD на VPN-шлюзе, перейдя к **конфигурации "точка — сеть** " и выбрав **опенвпн (SSL)** в качестве **типа туннеля**. Выберите **Azure Active Directory** в качестве **типа проверки подлинности** , а затем введите сведения в разделе **Azure Active Directory** .

    ![VPN Azure](./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png)


   > [!NOTE]
   > Убедитесь, что в конце значения включена обратная косая черта `AadIssuerUri` . В противном случае подключение может завершиться ошибкой.

10. Создайте и скачайте профиль, щелкнув ссылку **скачать VPN-клиент** .

11. Извлеките скачанный ZIP-файл.

12. Перейдите к распакованной папке "AzureVPN".

13. Запишите расположение файла "azurevpnconfig.xml". azurevpnconfig.xml содержит параметр для VPN-подключения и может быть импортирован непосредственно в клиентское приложение VPN Azure. Этот файл также можно передать всем пользователям, которым требуется подключение по электронной почте или другим средствам. Для успешного подключения пользователю понадобятся действительные учетные данные Azure AD.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы подключиться к виртуальной сети, необходимо создать и настроить профиль клиента VPN. См. раздел [Настройка VPN-клиента для P2S VPN-подключений](openvpn-azure-ad-client.md).
