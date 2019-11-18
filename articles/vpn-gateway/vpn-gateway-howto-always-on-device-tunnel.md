---
title: Настройка постоянного VPN-туннеля
titleSuffix: Azure VPN Gateway
description: Действия по настройке VPN-туннеля Always On для VPN-шлюза
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: f22b29cfcaf1d4c4ce28b2b0557d70b281b6891f
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146396"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Настройка туннеля Always On VPN для устройства

Одной из новых возможностей клиента виртуальной частной сети (VPN) Windows 10 является возможность поддерживать VPN-подключение. Always On является компонентом Windows 10, который позволяет автоматически подключаться к активному профилю VPN и оставаться подключенным на основе триггеров — а именно, для входа пользователя, изменения состояния сети или активного экрана устройства.

Шлюзы виртуальной сети Azure можно использовать с Windows 10 Always On для создания постоянных пользовательских туннелей и туннелей устройств в Azure. Эта статья поможет вам настроить туннель VPN-устройства AlwaysOn.

Always On VPN-подключения включают два типа туннелей:

* **Туннель устройства** подключается к указанным VPN-серверам, прежде чем пользователи смогут войти в устройство. Сценарии подключения до входа в систему и в целях управления устройствами используют туннель устройства.

* **Туннель пользователя** подключается только после входа пользователя на устройство. Пользовательский туннель позволяет пользователям получать доступ к ресурсам Организации через VPN-серверы.

Туннельное устройство и туннель пользователя работают независимо от их профилей VPN. Они могут быть подключены одновременно и могут использовать разные методы проверки подлинности и другие параметры конфигурации VPN.

## <a name="1-configure-the-gateway"></a>1. Настройка шлюза

Настройка VPN-шлюза для использования IKEv2 и аутентификации на основе сертификата с помощью этой [статьи "точка — сеть](vpn-gateway-howto-point-to-site-resource-manager-portal.md)".

## <a name="2-configure-the-device-tunnel"></a>2. Настройка туннеля устройства

Чтобы успешно установить туннель устройства, необходимо соблюдение следующих требований.

* Устройство должно быть присоединенным к домену компьютером под управлением Windows 10 Корпоративная или образовательных версий 1709 или более поздней версии.
* Туннель можно настроить только для встроенного решения VPN Windows и устанавливается с использованием IKEv2 с проверкой подлинности сертификата компьютера. 
* Для каждого устройства можно настроить только один туннель устройства.

1. Установите клиентские сертификаты в клиенте Windows 10, как показано в [статье клиент VPN типа "точка — сеть](point-to-site-how-to-vpn-client-install-azure-cert.md)". Сертификат должен находиться в хранилище локального компьютера.
1. Используйте [эти инструкции](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) для создания профиля VPN и настройки туннеля устройства в контексте локальной системной учетной записи.

### <a name="configuration-example-for-device-tunnel"></a>Пример конфигурации для туннеля устройства

После настройки шлюза виртуальной сети и установки сертификата клиента в хранилище локального компьютера на клиенте Windows 10 используйте следующие примеры для настройки туннеля клиентского устройства.

1. Скопируйте приведенный ниже текст и сохраните его как ***девицецерт. ps1***.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Скопируйте приведенный ниже текст и сохраните его как ***впнпрофиле. XML*** в той же папке, что и **девицецерт. ps1**. Измените следующий текст в соответствии с вашей средой.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Скачайте программу **PsExec** из [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) и извлеките файлы в **к:\пстулс**.
1. В командной строке администратора запустите PowerShell, выполнив следующую команду:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![оболочк](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. В PowerShell перейдите в папку, где находятся **девицецерт. ps1** и **впнпрофиле. XML** , и выполните следующую команду:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![мачинецерттест](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Запустите **Rasphone**.

   ![Rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Найдите запись **мачинецерттест** и нажмите кнопку **подключить**.

   ![Подключение](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Если подключение будет выполнено, перезагрузите компьютер. Туннель будет подключен автоматически.

## <a name="cleanup"></a>Очистка

Чтобы удалить профиль, выполните следующую команду:

![Очистка](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Дополнительная информация

Сведения об устранении неполадок см. [в статье проблемы с подключением "точка — сеть" в Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) .
