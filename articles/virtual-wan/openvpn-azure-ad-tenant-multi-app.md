---
title: 'VPN-шлюз: клиент Azure AD для разных групп пользователей: проверка подлинности Azure AD'
description: VPN-подключение P2S можно использовать для подключения к виртуальной сети с помощью проверки подлинности Azure AD.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: 90244b9dcf30c2ef01d4e57c9d8e35fa1d71f434
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985651"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Создание клиента Azure Active Directory для подключений по протоколу P2S Опенвпн

При подключении к виртуальной сети можно использовать проверку подлинности на основе сертификатов или аутентификацию RADIUS. Однако при использовании открытого протокола VPN можно также использовать проверку подлинности Azure Active Directory. Если вы хотите, чтобы другой набор пользователей мог подключаться к разным VPN-шлюзам, можно зарегистрировать несколько приложений в AD и связать их с разными VPN-шлюзами. Эта статья поможет вам настроить клиент Azure AD для проверки подлинности P2S Опенвпн и создать и зарегистрировать несколько приложений в Azure AD, чтобы разрешить разные права доступа для разных пользователей и групп.

> [!NOTE]
> Проверка подлинности Azure AD поддерживается только для подключений по протоколу OpenVPN®.
>

## <a name="tenant"></a>1. Создайте клиент Azure AD.

Создайте клиент Azure AD, выполнив действия, описанные в статье [Создание нового клиента](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Название организации
* Исходное доменное имя

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

1. В разделе Azure AD, **корпоративные приложения**выберите только что зарегистрированное приложение и щелкните **свойства**. Убедитесь, что **требуется назначение пользователей?** значение **Да**. Щелкните **Сохранить**.

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
   ```

> [!NOTE]
> Не используйте идентификатор приложения клиента VPN Azure в командах выше, так как он предоставит всем пользователям доступ к VPN-шлюзу. Используйте идентификатор зарегистрированного приложения.

## <a name="hub"></a>7. изменение назначения концентратора

1. Перейдите в колонку **Hubs** (Концентраторы) в виртуальной глобальной сети.
2. Выберите концентратор, с которым вы хотите связать конфигурацию vpn-сервера, и нажмите кнопку с многоточием (...).

   ![новый сайт](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Нажмите **Изменение виртуального концентратора**.
4. Установите флажок **Включить шлюз "точка — сеть"** и выберите нужную **единицу масштабирования шлюза**.

   ![новый сайт](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Введите **пул адресов**, с которого VPN клиентам будут назначаться IP адреса.
6. Щелкните **Confirm** (Подтвердить).
7. Выполнение операции может занять до 30 минут.

## <a name="device"></a>8. скачивание профиля VPN

Используйте профиль VPN для настройки клиентов.

1. На странице Виртуальной глобальной сети щелкните **Конфигурация VPN пользователя**.
2. В верхней части страницы щелкните **Загрузить конфигурацию пользователя VPN**.
3. После завершения создания файла вы можете скачать его, щелкнув ссылку.
4. Используйте файл профиля для настройки VPN-клиента.

4. Извлеките скачанный ZIP-файл.

5. Перейдите к распакованной папке "AzureVPN".

6. Запишите расположение файла "азуревпнконфиг. XML". Азуревпнконфиг. XML содержит параметр для VPN-подключения и может быть импортирован непосредственно в клиентское приложение VPN Azure. Этот файл также можно передать всем пользователям, которым требуется подключение по электронной почте или другим средствам. Для успешного подключения пользователю понадобятся действительные учетные данные Azure AD.
## <a name="configure-user-vpn-clients"></a>Настройка VPN-клиентов пользователя

Для подключения необходимо скачать VPN-клиент Azure (Предварительная версия) и импортировать профиль VPN-клиента, скачанный на предыдущих шагах на каждый компьютер, который нужно подключить к виртуальной сети.

> [!NOTE]
> Проверка подлинности Azure AD поддерживается только для подключений по протоколу OpenVPN®.
>

#### <a name="to-download-the-azure-vpn-client"></a>Загрузка VPN-клиента Azure

Используйте эту [ссылку](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab), чтобы скачать VPN-клиент Azure (Предварительная версия).

#### <a name="import"></a>Импорт профиля клиента

1. На странице выберите **Import** (Импорт).

    ![импорт](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Перейдите к XML-файлу профиля и выберите его. Выбрав файл, выберите **Open** (Открыть).

    ![импорт](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Укажите имя профиля и выберите **Save** (Сохранить).

    ![импорт](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Выберите **Connect** (Подключиться), чтобы подключиться к VPN.

    ![импорт](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. После подключения значок станет зеленым и выдаст **Connected** (Подключено).

    ![импорт](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>Удаление профиля клиента

1. Нажмите кнопку с многоточием (...) рядом с удаляемым профилем клиента. Затем щелкните **Remove** (Удалить).

    ![удалить](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Выберите **Remove** (Удалить), чтобы выполнить удаление.

    ![удалить](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnose connection issues (Диагностика проблем с подключением)

1. Для диагностики проблем с подключением можно использовать средство **Diagnose** (Диагностика). Нажмите кнопку с многоточием (...) рядом с VPN-подключением, которое нужно диагностировать, чтобы открыть меню. Затем выберите **Diagnose** (Диагностика).

    ![диагностика](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. На странице **Connection Properties** (Свойства подключения) выберите **Run Diagnosis** (Выполнить диагностику).

    ![диагностика](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Войдите с помощью своих учетных данных.

    ![диагностика](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Просмотр результатов диагностики.

    ![диагностика](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>Просмотр виртуальной глобальной сети

1. Перейдите к виртуальной глобальной сети.
2. На странице обзора каждая точка на карте представляет собой концентратор. Наведите курсор на любую точку, чтобы просмотреть сводку о работоспособности концентратора.
3. В разделе концентраторов и подключений можно просмотреть сведения о состоянии концентратора, сайте, регионе, состоянии VPN-подключения, приеме и передаче байтов.

## <a name="viewhealth"></a>Просмотр состояния работоспособности ресурса

1. Перейдите к своей глобальной сети.
2. На странице глобальной сети в разделе **Поддержка и устранение неполадок** щелкните **Работоспособность** и просмотрите сведения о своем ресурсе.


## <a name="cleanup"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы с помощью командлета [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Замените myResourceGroup на имя вашей группы ресурсов и выполните следующую команду PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Виртуальной глобальной сети см. в [этой статье](virtual-wan-about.md).
