---
title: 'VPN-шлюз: клиент Azure AD для разных групп пользователей: проверка подлинности Azure AD'
description: VPN-подключение P2S можно использовать для подключения к виртуальной сети с помощью проверки подлинности Azure AD.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: alzam
ms.openlocfilehash: 73a47b20010be0b4eadec790dd8fb254f912ff2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477178"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Создание клиента Azure Active Directory для подключений по протоколу P2S Опенвпн

При подключении к виртуальной сети можно использовать проверку подлинности на основе сертификатов или аутентификацию RADIUS. Однако при использовании открытого протокола VPN можно также использовать проверку подлинности Azure Active Directory. Если вы хотите, чтобы другой набор пользователей мог подключаться к разным VPN-шлюзам, можно зарегистрировать несколько приложений в AD и связать их с разными VPN-шлюзами. Эта статья поможет вам настроить клиент Azure AD для проверки подлинности P2S Опенвпн и создать и зарегистрировать несколько приложений в Azure AD, чтобы разрешить разные права доступа для разных пользователей и групп.

> [!NOTE]
> Проверка подлинности Azure AD поддерживается только для подключений по протоколу OpenVPN®.
>

## <a name="tenant"></a>1. Создайте клиент Azure AD.

Создайте клиент Azure AD, выполнив действия, описанные в статье [Создание нового клиента](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Название организации
* Первоначальное доменное имя

Пример:

   ![Новый клиент Azure AD](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2. Создание пользователей клиента Azure AD

Затем создайте две учетные записи пользователей. Создайте одну учетную запись глобального администратора и одну учетную запись главного пользователя. Учетная запись главного пользователя используется как учетная запись главного внедрения (учетная запись службы). При создании учетной записи пользователя клиента Azure AD необходимо настроить роль каталога для типа пользователя, который требуется создать.

Выполните действия, описанные в [этой статье](../active-directory/fundamentals/add-users-azure-active-directory.md) , чтобы создать по крайней мере двух пользователей для вашего клиента Azure AD. Не забудьте изменить **роль каталога** , чтобы создать типы учетных записей:

* глобальный администратор.
* Пользователь

## <a name="enable-authentication"></a>3. Регистрация VPN-клиента Azure в клиенте Azure AD

1. Укажите идентификатор каталога, который вы хотите использовать для проверки подлинности. Он указан в разделе Свойства страницы Active Directory.

    ![Идентификатор каталога](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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

    ![Идентификатор каталога](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. При появлении запроса выберите **принять** .

    ![Принять](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. В Azure AD в разделе **корпоративные приложения**вы увидите список **VPN Azure** .

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4. регистрация дополнительных приложений для различных пользователей и групп

1. В Azure Active Directory щелкните **Регистрация приложений** а затем **+ Новая регистрация** .

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. В колонке **Регистрация приложения** введите **имя** и выберите требуемые **Поддерживаемые типы учетных записей** и нажмите кнопку **зарегистрировать** .

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. После регистрации нового приложения щелкните **открыть API** в колонке приложения.

4. Щелкните **+ Добавить область**
5. Оставьте **URI идентификатора приложения** по умолчанию и нажмите кнопку **сохранить и продолжить** .

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. Заполните обязательные поля и убедитесь, что **состояние** **включено**. Нажмите кнопку **Добавить область** .

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. Щелкните **открыть API** , а затем **+ добавить клиентское приложение**.  В поле **идентификатор клиента**введите следующие значения в зависимости от облака.
    -   Введите **41b23e61-6c1e-4545-b367-cd054e0ed4b4** для **общедоступной** службы Azure
    -   Введите **51bb15d4-3a4f-4ebf-9dca-40096fe32426** для Azure для **государственных организаций**
    -   Введите **538ee9e6-310A-468d-Afef-ea97365856a9** для Azure для **Германии**
    -   Ввод **49f817b6-84ae-4cc0-928c-73f27289b3aa** для Azure для **Китая**


8. Нажмите кнопку **Добавить приложение** .

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. Скопируйте **идентификатор приложения (Client)** на странице **обзора** . Он понадобится для настройки VPN-шлюзов.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Повторите действия, описанные в этом разделе (4), чтобы создать столько приложений, сколько требуется для требований безопасности. Каждое приложение будет связано с VPN-шлюзом и может иметь другой набор пользователей. К шлюзу может быть привязано только одно приложение.

## <a name="enable-authentication"></a>5. Назначение пользователей для приложений

1. В разделе Azure AD, **корпоративные приложения**выберите только что зарегистрированное приложение и щелкните **свойства**. Убедитесь, что **требуется назначение пользователей?** значение **Да**. Нажмите кнопку **Сохранить**

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. На странице приложение щелкните **Пользователи и группы** , а затем — **Добавить пользователя** .

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. В разделе **Добавление назначения**щелкните **Пользователи и группы**. Выберите пользователей, которым будет предоставлен доступ к этому приложению VPN. Нажмите кнопку **Выбрать**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="enable-authentication"></a>6. Включение проверки подлинности Azure AD на VPN-шлюзе

1. Включите проверку подлинности Azure AD на VPN-шлюзе, выполнив следующие команды, и вы обязательно измените команду, чтобы она отражала собственную среду:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
> [!NOTE]
> Не используйте идентификатор приложения клиента VPN Azure в командах выше, так как он предоставит всем пользователям доступ к VPN-шлюзу. Используйте идентификатор зарегистрированного приложения.

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
