---
title: 'VPN-шлюз: VPN-клиент для подключений по протоколу P2S Опенвпн: проверка подлинности Azure AD'
description: VPN-подключение P2S можно использовать для подключения к виртуальной сети с помощью проверки подлинности Azure AD.
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: alzam
ms.openlocfilehash: c14d300e2c09316e0665ece3f3e15f7036d5b9a1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525081"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory проверка подлинности: Настройка VPN-клиента для подключений по протоколу P2S Опенвпн

Эта статья поможет настроить VPN-клиент для подключения к виртуальной сети с помощью VPN типа "точка — сеть" и Azure Active Directory проверки подлинности. Прежде чем можно будет подключиться и пройти проверку подлинности с помощью Azure AD, необходимо сначала настроить клиент Azure AD. Дополнительные сведения см. в статье [Настройка клиента Azure AD](openvpn-azure-ad-tenant.md).

> [!NOTE]
> Проверка подлинности Azure AD поддерживается только для подключений по протоколу OpenVPN®.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Работа с профилями клиентов

Для подключения необходимо скачать VPN-клиент Azure и настроить профиль VPN-клиента на каждом компьютере, который хочет подключиться к виртуальной сети. Можно создать клиентский профиль на компьютере, экспортировать его, а затем импортировать на дополнительные компьютеры.

### <a name="to-download-the-azure-vpn-client"></a>Загрузка VPN-клиента Azure

Используйте эту [ссылку](https://go.microsoft.com/fwlink/?linkid=2117554), чтобы скачать VPN-клиент Azure. Убедитесь, что VPN-клиент Azure имеет разрешение на запуск в фоновом режиме. Чтобы проверить или включить разрешение, выполните следующие действия:

1. Последовательно выберите пункты Пуск и параметры > конфиденциальность > фоновые приложения.
2. В разделе фоновые приложения убедитесь, что **приложение разрешить запуск в фоновом режиме** включено.
3. В разделе Выберите приложения **, которые могут**выполняться в фоновом режиме, а затем включите параметры для VPN-клиента Azure.

  ![разрешение](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Создание профиля клиента на основе сертификата

При работе с профилем на основе сертификата убедитесь, что на клиентском компьютере установлены соответствующие сертификаты. Дополнительные сведения о сертификатах см. в разделе [Установка сертификатов клиента](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Создание профиля клиента RADIUS

  ![radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Секрет сервера можно экспортировать в профиле VPN-клиента P2S.  Инструкции по экспорту клиентского профиля можно найти [здесь](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Экспорт и распространение клиентского профиля

Если у вас есть рабочий профиль и его необходимо распространить другим пользователям, его можно экспортировать, выполнив следующие действия.

1. Выделите профиль VPN-клиента, который требуется экспортировать, щелкните.. **.**, а затем выберите **Экспорт**.

    ![экспорт](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Выберите расположение, в которое нужно сохранить этот профиль, оставьте имя файла, а затем нажмите кнопку **сохранить** , чтобы сохранить XML-файл.

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

1. Щелкните многоточие рядом с удаляемым клиентским профилем. Затем щелкните **Remove** (Удалить).

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Выберите **Remove** (Удалить), чтобы выполнить удаление.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Создание подключения

1. На странице выберите **+** , а затем **+ Добавить**.

    ![соединение](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Заполните сведения о подключении. Если вы не уверены в этих значениях, обратитесь к администратору. После заполнения значений выберите **сохранить**.

    ![соединение](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Выберите **Connect** (Подключиться), чтобы подключиться к VPN.

    ![соединение](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Выберите правильные учетные данные, а затем нажмите кнопку **продолжить**.

    ![соединение](./media/openvpn-azure-ad-client/create/create4.jpg)

5. После успешного подключения значок будет зеленым и, например, **подключенным**.

    ![соединение](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Автоматическое подключение

Эти действия помогут настроить подключение для автоматического подключения с параметром Always on.

1. На домашней странице VPN-клиента выберите **параметры VPN**.

    ![авто](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Выберите **Да** в диалоговом окне переключение приложений.

    ![авто](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Убедитесь, что подключение, которое вы хотите установить, еще не подключено, затем выделите профиль и установите флажок " **автоматически устанавливать подключение** ".

    ![авто](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Выберите **Подключиться** , чтобы инициировать VPN-подключение.

    ![авто](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnose connection issues (Диагностика проблем с подключением)

1. Для диагностики проблем с подключением можно использовать средство **Diagnose** (Диагностика). Щелкните **...** рядом с VPN-подключением, которое нужно диагностировать, чтобы открыть меню. Затем выберите **Diagnose** (Диагностика).

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. На странице **Connection Properties** (Свойства подключения) выберите **Run Diagnosis** (Выполнить диагностику).

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Войдите с помощью своих учетных данных.

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Просмотр результатов диагностики.

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>часто задаваемые вопросы

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Разделы справки добавить DNS-суффиксы к VPN-клиенту?

Вы можете изменить скачанный XML-файл профиля и добавить ** \<dnssuffixes> \<dnssufix> \</dnssufix> \</dnssuffixes> ** Теги.

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

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Разделы справки добавить пользовательские DNS-серверы в VPN-клиент?

Вы можете изменить скачанный XML-файл профиля и добавить ** \<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers> ** Теги.

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
> Клиент Azure AD Опенвпн использует записи DNS таблица политики разрешения имен (NRPT). Это означает, что DNS-серверы не будут перечислены в выходных данных `ipconfig /all` . Чтобы подтвердить использование параметров DNS, обратитесь к разделу [Get-днсклиентнрптполици](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) в PowerShell.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Разделы справки добавить настраиваемые маршруты к VPN-клиенту?

Вы можете изменить скачанный XML-файл профиля и добавить ** \<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes> ** Теги.

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

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Разделы справки блокировать (исключить) маршруты от VPN-клиента?

Вы можете изменить скачанный XML-файл профиля и добавить ** \<excluderoutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</excluderoutes> ** Теги.

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

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Можно ли импортировать профиль из командной строки?

Вы можете импортировать профиль из командной строки, поместив скачанный файл **azurevpnconfig.xml** в папку **%усерпрофиле%\аппдата\локал\паккажес\микрософт. AzureVpn_8wekyb3d8bbwe \локалстате** и выполнив следующую команду:

```
azurevpn -i azurevpnconfig.xml 
```
чтобы принудительно выполнить импорт, используйте параметр **-f** .


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в статье [Создание клиента Azure Active Directory для P2S открытых VPN-подключений, использующих проверку подлинности Azure AD](openvpn-azure-ad-tenant.md).
