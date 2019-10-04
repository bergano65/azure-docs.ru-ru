---
title: Настройка VPN-туннеля Always On для VPN-шлюза
description: Действия по настройке VPN-туннеля Always On пользователя для VPN-шлюза
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: dc0abf12c60f845fde0d16bd874a1436aef3b7ab
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846476"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Настройка туннеля VPN-пользователя Always On

Одной из новых возможностей клиента виртуальной частной сети (VPN) Windows 10 является возможность поддерживать VPN-подключение. Always On является компонентом Windows 10, который позволяет автоматически подключаться к активному профилю VPN и оставаться подключенным на основе триггеров — а именно, для входа пользователя, изменения состояния сети или активного экрана устройства.

Шлюзы виртуальной сети Azure можно использовать с Windows 10 Always On для создания постоянных пользовательских туннелей и туннелей устройств в Azure. Эта статья поможет вам настроить туннель VPN-пользователя Always On.

Always On VPN-подключения включают два типа туннелей:

* **Туннель устройства** подключается к указанным VPN-серверам, прежде чем пользователи смогут войти в устройство. Сценарии подключения до входа в систему и в целях управления устройствами используют туннель устройства.

* **Туннель пользователя** подключается только после входа пользователя на устройство. Пользовательский туннель позволяет пользователям получать доступ к ресурсам Организации через VPN-серверы.

Туннельное устройство и туннель пользователя работают независимо от их профилей VPN. Они могут быть подключены одновременно и могут использовать разные методы проверки подлинности и другие параметры конфигурации VPN.

## <a name="1-configure-the-gateway"></a>1. Настройка шлюза

Настройка VPN-шлюза для использования IKEv2 и аутентификации на основе сертификата с помощью этой [статьи "точка — сеть](vpn-gateway-howto-point-to-site-resource-manager-portal.md)".

## <a name="2-configure-the-user-tunnel"></a>2. Настройка пользовательского туннеля

1. Установите клиентские сертификаты в клиенте Windows 10, как показано в [статье клиент VPN типа "точка — сеть](point-to-site-how-to-vpn-client-install-azure-cert.md)". Сертификат должен находиться в хранилище текущего пользователя
2. Настройте Always On VPN-клиент с помощью PowerShell, SCCM или Intune, используя [эти инструкции](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

### <a name="configuration-example-for-user-tunnel"></a>Пример конфигурации для пользовательского туннеля

После настройки шлюза виртуальной сети и установки сертификата клиента в хранилище локального компьютера на клиенте Windows 10 используйте следующие примеры для настройки туннеля клиентского устройства.

1. Скопируйте приведенный ниже текст и сохраните его как ***усерцерт. ps1***.

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
1. Скопируйте приведенный ниже текст и сохраните его как ***впнпрофиле. XML*** в той же папке, что и **усерцерт. ps1**. Измените следующий текст в соответствии с вашей средой.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
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
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. Запустите PowerShell от имени администратора.

1. В PowerShell перейдите в папку, где находятся **усерцерт. ps1** и **впнпрофиле. XML** , и выполните следующую команду:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![мачинецерттест](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. Просмотрите параметры VPN.

1. Найдите запись **усертест** и нажмите кнопку **подключить**.

1. Если подключение установлено успешно, вы успешно настроили пользовательский туннель Always-on.

## <a name="cleanup"></a>Очистка

Чтобы удалить профиль, выполните следующую команду:

1. Отключите подключение и снимите флажок "подключиться автоматически".

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

![Очистка](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Следующие шаги

Сведения об устранении неполадок см. в статье проблемы с подключением " [точка — сеть" в Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) .
