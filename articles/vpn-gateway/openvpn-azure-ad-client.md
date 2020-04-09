---
title: 'VPN Gateway: VPN-клиент для P2S-соединений протокола OpenVPN: аутентификация Azure AD'
description: Вы можете использовать P2S VPN для подключения к VNet с помощью аутентификации Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: alzam
ms.openlocfilehash: 7bc28a03476e773325d14808e1c7ac99103b2d5d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879451"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Настройка VPN-клиента для подключений к протоколу P2S OpenVPN: аутентификация Azure AD

Эта статья поможет настроить VPN-клиент для подключения к виртуальной сети с помощью VPN-сайта и проверки подлинности Active Directory. Прежде чем подключиться и проверить подлинность с помощью Azure AD, необходимо сначала настроить арендатора Azure AD. Для получения дополнительной информации [см.](openvpn-azure-ad-tenant.md)

> [!NOTE]
> Проверка подлинности Azure AD поддерживается только для подключений по протоколу OpenVPN®.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Работа с профилями клиентов

Чтобы подключиться, необходимо загрузить Azure VPN Client и настроить профиль VPN клиента на каждом компьютере, который хочет подключиться к VNet. Вы можете создать профиль клиента на компьютере, экспортировать его, а затем импортировать его на дополнительные компьютеры.

### <a name="to-download-the-azure-vpn-client"></a>Загрузка VPN-клиента Azure

Используйте эту [ссылку](https://go.microsoft.com/fwlink/?linkid=2117554), чтобы скачать VPN-клиент Azure. Пожалуйста, убедитесь, что клиент VPN Azure имеет разрешение на запуск в фоновом режиме. Чтобы проверить/включить разрешение, следуйте нижеследующим шагам:

1. Перейти к запуску, а затем выберите Настройки > конфиденциальности > фоновых приложений.
2. Под фоновыми приложениями убедитесь, что **приложения Let apps будут работать в фоновом режиме.**
3. При выборе приложений, которые могут работать в фоновом режиме, переверните настройки для Azure VPN Client **в On.**

  ![разрешение](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Создание профиля клиента на основе сертификатов

При работе с профилем на основе сертификатов убедитесь, что соответствующие сертификаты установлены на клиентском компьютере. Для получения дополнительной информации о сертификатах, [см.](point-to-site-how-to-vpn-client-install-azure-cert.md)

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Создание профиля клиента RADIUS

  ![radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Секрет сервера можно экспортировать в профиле клиента P2S VPN.  Инструкции о том, как экспортировать профиль клиента можно найти [здесь](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Экспорт и распространение профиля клиента

После того, как у вас есть рабочий профиль и вам нужно распространять его среди других пользователей, вы можете экспортировать его, используя следующие шаги:

1. Выделите профиль клиента VPN, который вы хотите экспортировать, выберите **...**, затем выберите **Экспорт.**

    ![экспорт](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Выберите место, в которое вы хотите сохранить этот профиль, оставьте имя файла как есть, а затем выберите **Сохранить,** чтобы сохранить файл xml.

    ![экспорт](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Импорт профиля клиента

1. На странице выберите **Import** (Импорт).

    ![импорт](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Перейдите к XML-файлу профиля и выберите его. Выбрав файл, выберите **Open** (Открыть).

    ![импорт](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Укажите имя профиля и выберите **Save** (Сохранить).

    ![импорт](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Выберите **Connect** (Подключиться), чтобы подключиться к VPN.

    ![импорт](./media/openvpn-azure-ad-client/import/import4.jpg)

5. После подключения значок станет зеленым и выдаст **Connected** (Подключено).

    ![импорт](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Удаление профиля клиента

1. Выберите эллипсы рядом с профилем клиента, который вы хотите удалить. Затем щелкните **Remove** (Удалить).

    ![удалить](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Выберите **Remove** (Удалить), чтобы выполнить удаление.

    ![удалить](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Создание соединения

1. На странице, **+** выберите, затем **добавить**.

    ![соединение](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Заполните информацию о подключении. Если вы не уверены в значениях, свяжитесь с администратором. После заполнения значений выберите **Сохранить**.

    ![соединение](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Выберите **Connect** (Подключиться), чтобы подключиться к VPN.

    ![соединение](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Выберите правильные учетные данные, а затем выберите **Продолжить**.

    ![соединение](./media/openvpn-azure-ad-client/create/create4.jpg)

5. После успешного подключения, значок станет зеленым и сказать **Connected**.

    ![соединение](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Автоматическое подключение

Эти шаги помогут настроить соединение для автоматического подключения к всегда.

1. На главной странице для вашего VPN клиента выберите **VPN Настройки.**

    ![авто](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Выберите **Да** на диалоговом окне приложений переключения.

    ![авто](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Убедитесь, что соединение, которое вы хотите установить, еще не подключено, затем подчеркните профиль и проверьте автоматическичек проверки **Connect.**

    ![авто](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Выберите **Connect,** чтобы инициировать VPN соединение.

    ![авто](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnose connection issues (Диагностика проблем с подключением)

1. Для диагностики проблем с подключением можно использовать средство **Diagnose** (Диагностика). Выберите **...** Затем выберите **Diagnose** (Диагностика).

    ![диагностика](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. На странице **Connection Properties** (Свойства подключения) выберите **Run Diagnosis** (Выполнить диагностику).

    ![диагностика](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Войдите с помощью своих учетных данных.

    ![диагностика](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Просмотр результатов диагностики.

    ![диагностика](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Как добавить суффиксы DNS в VPN-клиент?

Вы можете изменить загруженный профиль XML файл и добавить ** \<dnssuffixes>\<dnssufix> \</ dnssufix>\</ dnssuffixes>** теги

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Как добавить пользовательские DNS-серверы в VPN-клиент?

Вы можете изменить загруженный профиль XML файл и добавить ** \<dnsservers \<>dnsserver> \</ dnsserver>\</ dnsservers>** теги

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> Клиент OpenVPN Azure AD использует записи таблицы политики разрешения имен DNS (NRPT), что `ipconfig /all`означает, что DNS-серверы не будут перечислены в рамках вывода. Чтобы подтвердить настройки DNS, пожалуйста, обратитесь к [Get-DnsClientNrptPolicy](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) в PowerShell.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Как добавить пользовательские маршруты к VPN-клиенту?

Вы можете изменить загруженный файл XML профиля и добавить ** \<includeroutes>\<маршрут>\<назначения>\<маски> \</назначения>\</маска>\</маршрут>\</включитьмаршруты>** теги

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Как заблокировать (исключить) маршруты из VPN-клиента?

Вы можете изменить загруженный файл XML профиля и добавить ** \< \<маршруты>назначения \<>>\< \< \<маску> \</назначение>\</маска>/маршрут>/excluderoutes>** теги

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Могу ли я импортировать профиль из запроса командной строки?

Вы можете импортировать профиль из запроса командной строки, разместив загруженный файл **azurevpnconfig.xml** в **%userprofile AzureVpn_8wekyb3d8bbwe%**

```
azurevpn -i azurevpnconfig.xml 
```
заставить импорт использовать **-f** переключатель, а также


## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации [см. Создать наниматель Active Directory Azure для VPN-соединений P2S Open, которые используют аутентификацию Azure AD.](openvpn-azure-ad-tenant.md)
