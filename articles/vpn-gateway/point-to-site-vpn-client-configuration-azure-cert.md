---
title: 'Создание и установка файлов конфигурации VPN-клиента P2S для аутентификации на сертификате Azure: Azure'
description: Создание и установка файлов конфигурации VPN-клиента Windows, Linux, Linux (strongSwan) и Mac OS X для использования аутентификации на основе сертификата для подключения "точка — сеть".
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/12/2019
ms.author: cherylmc
ms.openlocfilehash: fb6c484e234b4641a521bd876acdfeb4df562260
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063114"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Создание и установка файлов конфигурации VPN-клиента для настройки подключений типа "точка — сеть" с использованием собственной аутентификации Azure на основе сертификата

Файлы конфигурации VPN-клиента содержатся в ZIP-файле. Эти файлы содержат параметры, требуемые при подключениях "точка — сеть" между собственными VPN-клиентами IKEv2 Windows, Linux или Mac и виртуальной сетью с использованием собственной аутентификации Azure на основе сертификата.

Файлы конфигурации клиента связаны с VPN-конфигурацией для виртуальной сети. Если вы создали файлы конфигурации VPN-клиента, а затем внесли изменения в конфигурацию VPN-подключения типа "точка — сеть" (например, изменили тип VPN-протокола или аутентификации), создайте новую конфигурацию VPN-клиента на устройствах пользователей. 

* См. дополнительные сведения о [подключениях типа "точка — сеть"](point-to-site-about.md).
* Инструкции касательно OpenVPN см. в статье [Настройка OpenVPN для подключения "точка — сеть" VPN-шлюза Azure (предварительная версия)](vpn-gateway-howto-openvpn.md) и [Настройка клиентов OpenVPN для VPN-шлюза Azure (предварительная версия)](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate"></a>Создание файлов конфигурации VPN-клиента

Перед подключением убедитесь, что на устройствах всех пользователей установлены действительные сертификаты. См. инструкции по [установке сертификата клиента](point-to-site-how-to-vpn-client-install-azure-cert.md).

Вы можете создать файлы конфигурации клиента с помощью PowerShell или с помощью портала Azure. И в первом, и во втором случае возвращается один и тот же ZIP-файл. Распакуйте файл. Отобразятся следующие папки:

  * **WindowsAmd64** и **WindowsX86**. Эти папки содержат пакеты установщика 32- и 64-разрядной версий Windows. Пакет установщика **WindowsAmd64** предназначен для всех поддерживаемых 64-разрядных клиентов Windows, не только Amd.
  * **Generic**. Эта папка содержит общие сведения для создания собственной конфигурации VPN-клиента. Эта папка доступна, если для шлюза настроены протоколы IKEv2 и SSTP+IKEv2. Если настроен только протокол SSTP, эта папка отсутствует.

### <a name="zipportal"></a>Создание файлов с помощью портала Azure

1. На портале Azure перейдите в шлюз виртуальной сети, к которой необходимо подключиться.
2. На странице шлюза виртуальной сети щелкните **Point-to-site configuration** (Конфигурации "точка — сеть").
3. В верхней части страницы Point-to-site configuration (Конфигурации "точка — сеть") щелкните **Download VPN client** (Скачать VPN-клиент). Пакет конфигурации клиента создается несколько минут.
4. В браузере появится сообщение о том, что ZIP-файл конфигурации клиента доступен. Он имеет то же имя, что и шлюз. Распакуйте файл. После этого отобразятся папки.

### <a name="zipps"></a>Создание файлов с помощью PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. При создании файлов конфигурации VPN-клиента установите для параметра AuthenticationMethod значение EapTls. Создайте конфигурацию VPN-клиента с помощью следующей команды:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Скопируйте URL-адрес в браузер, чтобы скачать ZIP-файл, а затем распакуйте файл для просмотра папок.

## <a name="installwin"></a>Windows

На каждом клиентском компьютере Windows можно использовать один и тот же пакет конфигурации VPN-клиента, если его версия соответствует архитектуре клиента. Список поддерживаемых клиентских операционных систем см. в разделе с описанием подключений типа "точка — сеть" в статье [VPN-шлюз: вопросы и ответы](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Вам потребуются права администратора для клиентского компьютера Windows, с которого устанавливается подключение.
>
>

Чтобы настроить в Windows собственный VPN-клиент для аутентификации на основе сертификата, сделайте следующее:

1. Выберите файлы конфигурации VPN-клиента, которые соответствуют архитектуре компьютера Windows. Для 64-разрядной архитектуры процессора выберите пакет установщика VpnClientSetupAmd64. Для 32-разрядной архитектуры процессора выберите пакет установщика VpnClientSetupX86. 
2. Дважды щелкните пакет, чтобы установить его. При появлении всплывающего окна SmartScreen щелкните **Дополнительно**, а затем выберите **Выполнить в любом случае**.
3. На клиентском компьютере перейдите в раздел **Параметры сети** и щелкните **VPN**. Для VPN-подключения отображается имя виртуальной сети, к которой оно устанавливается. 
4. Перед попыткой подключения убедитесь, что вы установили сертификат клиента на клиентском компьютере. Сертификат клиента требуется при использовании собственной аутентификации Azure на основе сертификата. Дополнительную информацию о создании сертификатов см. в разделе [Настройка подключения "точка — сеть" к виртуальной сети с использованием собственной аутентификации Azure на основе сертификата и портала Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Инструкции по установке сертификата клиента см. в разделе [Установка сертификата клиента для аутентификации Azure на основе сертификата при подключениях типа "точка — сеть"](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Mac (OS X)

 Необходимо вручную настроить VPN-клиент IKEv2 на каждом компьютере Mac, который будет подключаться к Azure. Azure не предоставляет файл mobileconfig для собственной аутентификации Azure на основе сертификата. В папке **Generic** содержатся все необходимые для конфигурации сведения. Если папки Generic нет в скачанных файлах, вполне вероятно, что IKEv2 не был выбран в качестве типа туннеля. Обратите внимание, что базовый SKU VPN-шлюза не поддерживает IKEv2. Выбрав IKEv2, создайте ZIP-файл еще раз для получения папки Generic.<br>Эта папка содержит следующие файлы:

* Файл **VpnSettings.xml** — содержит такие важные параметры, как адрес сервера и тип туннеля. 
* Файл **VpnServerRoot.cer** — содержит корневой сертификат, который требуется для проверки VPN-шлюза Azure при настройке подключения типа "точка — сеть".

Чтобы настроить на устройстве Mac собственный VPN-клиент для аутентификации на основе сертификата, сделайте следующее: Выполните следующие действия на каждом компьютере Mac, который будет подключаться к Azure:

1. Импортируйте корневой сертификат **VpnServerRoot** на компьютер Mac. Для этого скопируйте файл на устройство Mac и дважды щелкните его. Чтобы выполнить импорт, щелкните **Add** (Добавить).

   ![Добавление сертификата](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >При двойном щелчке сертификата диалоговое окно **добавления** может не отобразиться, но сертификат устанавливается в надлежащее хранилище. Вы можете проверить наличие сертификата в цепочке ключей входа по категории сертификатов.
    >
  
2. Убедитесь, что вы установили сертификат клиента, выданный с использованием корневого сертификата, который вы передали в Azure при настройке параметров подключения типа "точка — сеть". Это не сертификат VPNServerRoot, который вы установили на предыдущем шаге. Этот сертификат клиента требуется для аутентификации. Дополнительную информацию о создании сертификатов см. в разделе [Настройка подключения "точка — сеть" к виртуальной сети с использованием собственной аутентификации Azure на основе сертификата и портала Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Инструкции по установке сертификата клиента см. в разделе [Установка сертификата клиента для аутентификации Azure на основе сертификата при подключениях типа "точка — сеть"](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Откройте диалоговое окно **Network** (Сеть) и в разделе **Network Preferences** (Параметры сети) щелкните **+** , чтобы создать профиль подключения типа "точка — сеть" между VPN-клиентом и виртуальной сетью Azure.

   Установите следующие значения: для параметра **Interface** (Интерфейс) — VPN, для **VPN Type** (Тип VPN) — IKEv2. Укажите имя профиля в поле **Service Name** (Имя службы), а затем нажмите кнопку **Create** (Создать), чтобы создать профиль подключения VPN-клиента.

   ![Сеть](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. В папке **Generic** из файла **VpnSettings.xml** скопируйте значение тега **VpnServer**. Вставьте это значение в поля профиля **Server Address** (Адрес сервера) и **Remote ID** (Удаленный ИД).

   ![Сведения о сервере](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Щелкните **Authentication Settings** (Параметры аутентификации) и выберите **Certificate** (Сертификат). 

   ![Параметры аутентификации](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Щелкните **Select…** (Выбрать), чтобы выбрать сертификат клиента, который будет использоваться для аутентификации. Это сертификат, установленный на шаге 2.

   ![на основе сертификата.](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. В окне **Choose An Identity** (Выбор удостоверения) отобразится список доступных сертификатов. Выберите нужный сертификат, а затем щелкните **Continue** (Продолжить).

   ![удостоверение](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. В поле **Local ID** (Локальный идентификатор) укажите имя сертификата (из шага 6). В нашем примере это ikev2Client.com. Щелкните **Apply** (Применить), чтобы сохранить изменения.

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. В диалоговом окне **Network** (Сеть) щелкните **Apply** (Применить), чтобы сохранить все изменения. Затем щелкните **Connect** (Подключиться), чтобы установить подключение типа "точка — сеть" к виртуальной сети Azure.

## <a name="linuxgui"></a>Linux (графический пользовательский интерфейс strongSwan)

### <a name="installstrongswan"></a>Установка strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="genlinuxcerts"></a>Создание сертификатов

Если вы еще не создали сертификаты, выполните следующие действия.

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install"></a>Установка и настройка

Следующие инструкции были созданы в Ubuntu 18.0.4. Ubuntu 16.0.10 не поддерживает графический пользовательский интерфейс strongSwan. Использовать Ubuntu 16.0.10 можно только с помощью [командной строки](#linuxinstallcli). В зависимости от версии Linux и strongSwan указанные ниже примеры могут отличаться от экранов, которые вы видите.

1. Откройте приложение **Terminal**, чтобы установить **strongSwan** и его Network Manager, выполнив команду из примера.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Выберите **Параметры** , а затем выберите **сеть**.

   ![Изменение подключения](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Нажмите кнопку **+** , чтобы создать новое соединение.

   ![Добавление подключения](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. В меню выберите **IPSec/IKEv2 (strongSwan)** и дважды щелкните. Вы можете присвоить имя подключению на этом шаге.

   ![Выбор типа подключения](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Откройте файл **VpnSettings.xml** из папки **Generic** в скачанных файлах конфигурации клиента. Найдите тег **VpnServer** и скопируйте имя, начиная с azuregateway и заканчивая .cloudapp.net.

   ![Имя копии](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Вставьте это имя в поле **Address** (Адрес) нового VPN-подключения в разделе **Gateway** (Шлюз). Затем щелкните значок папки в конце поля **Certificate** (Сертификат), перейдите в папку **Generic** и выберите файл **VpnServerRoot**.
7. В разделе **Client** (Клиент) подключения для параметра **Authentication** (Аутентификация) выберите **Certificate/private key** (Сертификат/закрытый ключ). Для параметров **Certificate** (Сертификат ) и **Private key** (Закрытый ключ) выберите созданные ранее сертификат и закрытый ключ. В разделе **Options** (Параметры) выберите **Request an inner IP address** (Запросить внутренний IP-адрес). Нажмите кнопку **Добавить**.

   ![Запрос внутреннего IP-адреса](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. **Включите подключение.**

## <a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>Установка strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Создайте сертификаты.

Если вы еще не создали сертификаты, выполните следующие действия.

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Установка и настройка

1. Загрузите пакет VPN-клиента с портала Azure.
2. Извлеките файл.
3. Из папки **Generic** копируйте или переместите файл VpnServerRoot.cer в папку /etc/ipsec.d/cacerts.
4. Скопируйте или переместите файл cp client.p12 в папку /etc/ipsec.d/private/. Это сертификат клиента для VPN-шлюза Azure.
5. Откройте файл VpnSettings.xml и скопируйте значение `<VpnServer>`. Это значение потребуется на следующем шаге.
6. Измените значения в приведенном ниже примере, а затем добавьте этот код в файл конфигурации /etc/ipsec.conf.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
6. Добавьте следующий код в файл */etc/ipsec.secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Выполните следующие команды:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Дальнейшие действия

Вернитесь к статье, чтобы [завершить настройку подключения типа "точка — сеть"](vpn-gateway-howto-point-to-site-rm-ps.md).

Сведения об устранении неполадок с подключениями "точка — сеть" см. в следующий статьях:

  * [Устранение неполадок подключения типа "точка — сеть" Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Устранение неполадок VPN-подключений "точка — сеть" для клиентов Mac OS X](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
