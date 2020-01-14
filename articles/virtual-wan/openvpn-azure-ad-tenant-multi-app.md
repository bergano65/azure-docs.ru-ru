---
title: 'VPN-шлюз: клиент Azure AD для разных групп пользователей: проверка подлинности Azure AD'
description: VPN-подключение P2S можно использовать для подключения к виртуальной сети с помощью проверки подлинности Azure AD.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: b390b5f8b00f61994db820a3af7bce26a3e0a30d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934977"
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

## <a name="site"></a>6. Создание новой конфигурации P2S

Конфигурации подключения "точка — сеть" определяет параметры для подключения удаленных клиентов.

1. Установите следующие переменные, заменив по мере необходимости значения в вашей среде.

   ```powershell
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Чтобы создать конфигурацию, выполните следующие команды:

   ```powershell
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
> [!NOTE]
> Do not use the Azure VPN client's application ID in the commands above as it will grant all users access to the VPN gateway. Use the ID of the application(s) you registered.

## <a name="hub"></a>7. Edit hub assignment

1. Navigate to the **Hubs** blade under the virtual WAN.
2. Select the hub that you want to associate the vpn server configuration to and click the ellipsis (...).

   ![new site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Click **Edit virtual hub**.
4. Check the **Include point-to-site gateway** check box and pick the **Gateway scale unit** that you want.

   ![new site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Enter the **Address pool** from which the VPN clients will be assigned IP addresses.
6. Click **Confirm**.
7. The operation will can take up to 30 minutes to complete.

## <a name="device"></a>8. Download VPN profile

Use the VPN profile to configure your clients.

1. On the page for your virtual WAN, click **User VPN configurations**.
2. At the top of the  page, click **Download user VPN config**.
3. Once the file has finished creating, you can click the link to download it.
4. Use the profile file to configure the VPN clients.

4. Extract the downloaded zip file.

5. Browse to the unzipped “AzureVPN” folder.

6. Make a note of the location of the “azurevpnconfig.xml” file. The azurevpnconfig.xml contains the setting for the VPN connection and can be imported directly into the Azure VPN Client application. You can also distribute this file to all the users that need to connect via e-mail or other means. The user will need valid Azure AD credentials to connect successfully.
## Configure user VPN clients

To connect, you need to download the Azure VPN Client (Preview) and import the VPN client profile that was downloaded in the previous steps on every computer that wants to connect to the VNet.

> [!NOTE]
> Azure AD authentication is supported only for OpenVPN® protocol connections.
>

#### To download the Azure VPN client

Use this [link](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) to download the Azure VPN Client (Preview).

#### <a name="import"></a>To import a client profile

1. On the page, select **Import**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Browse to the profile xml file and select it. With the file selected, select **Open**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Specify the name of the profile and select **Save**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Select **Connect** to connect to the VPN.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Once connected, the icon will turn green and say **Connected**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>To delete a client profile

1. Select the ellipsis (...) next to the client profile that you want to delete. Then, select **Remove**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Select **Remove** to delete.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnose connection issues

1. To diagnose connection issues, you can use the **Diagnose** tool. Select the ellipsis (...) next to the VPN connection that you want to diagnose to reveal the menu. Then select **Diagnose**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. On the **Connection Properties** page, select **Run Diagnosis**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Sign in with your credentials.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. View the diagnosis results.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>View your virtual WAN

1. Navigate to the virtual WAN.
2. On the Overview page, each point on the map represents a hub. Hover over any point to view the hub health summary.
3. In the Hubs and connections section, you can view hub status, site, region, VPN connection status, and bytes in and out.

## <a name="viewhealth"></a>View your resource health

1. Navigate to your WAN.
2. On your WAN page, in the **SUPPORT + Troubleshooting** section, click **Health** and view your resource.


## <a name="cleanup"></a>Clean up resources

When you no longer need these resources, you can use [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) to remove the resource group and all of the resources it contains. Replace "myResourceGroup" with the name of your resource group and run the following PowerShell command:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Виртуальной глобальной сети см. в [этой статье](virtual-wan-about.md).