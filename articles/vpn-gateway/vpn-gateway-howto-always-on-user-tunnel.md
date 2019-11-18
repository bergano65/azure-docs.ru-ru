---
title: Настройка постоянного туннеля VPN-пользователя
titleSuffix: Azure VPN Gateway
description: В этой статье описывается, как настроить туннель VPN пользователя Always On для VPN-шлюза.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: 099ed3c3c0ac2abe034388849385a45b44b32b34
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145946"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Настройка туннеля Always On VPN для пользователя

Новой функцией VPN-клиента Windows 10, Always On, является возможность поддерживать VPN-подключение. Используя Always On, активный профиль VPN может подключаться автоматически и оставаться подключенным на основе триггеров, таких как вход пользователя, изменение состояния сети или активный экран устройства.

Вы можете использовать шлюзы виртуальной сети Azure с Windows 10 Always On, чтобы установить постоянные туннели пользователей и туннели устройств в Azure. Эта статья поможет вам настроить туннель VPN-пользователя Always On.

Always On VPN-подключений включают один из двух типов туннелей:

* **Туннель устройства**: подключается к указанным VPN-серверам перед входом пользователей на устройство. Сценарии подключения перед входом и управление устройствами используют туннель устройства.

* **Туннель пользователя**. подключается только после входа пользователей на устройство. С помощью пользовательских туннелей можно получить доступ к ресурсам Организации через VPN-серверы.

Туннели устройств и пользовательские туннели работают независимо от профилей VPN. Они могут быть подключены одновременно. при необходимости они могут использовать разные методы проверки подлинности и другие параметры конфигурации VPN.

В следующих разделах вы настраиваете VPN-шлюз и туннель пользователя.

## <a name="step-1-configure-a-vpn-gateway"></a>Шаг 1. Настройка VPN-шлюза

Настройте VPN-шлюз для использования IKEv2 и аутентификацию на основе сертификата, следуя инструкциям в этой статье " [точка — сеть](vpn-gateway-howto-point-to-site-resource-manager-portal.md) ".

## <a name="step-2-configure-a-user-tunnel"></a>Шаг 2. Настройка пользовательского туннеля

1. Установите клиентские сертификаты на клиенте Windows 10, как показано в статье [клиент VPN типа "точка — сеть](point-to-site-how-to-vpn-client-install-azure-cert.md) ". Сертификат должен находиться в хранилище текущего пользователя.

1. Настройте Always On VPN-клиента с помощью PowerShell, System Center Configuration Manager или Intune, следуя инструкциям в разделе [Настройка клиента Windows 10 Always on VPN-подключения](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

### <a name="example-configuration-for-the-user-tunnel"></a>Пример конфигурации для пользовательского туннеля

После настройки шлюза виртуальной сети и установки сертификата клиента в хранилище локального компьютера на клиенте Windows 10 настройте туннель клиентского устройства, используя следующие примеры:

1. Скопируйте следующий текст и сохраните его как *усерцерт. ps1*:

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
1. Скопируйте приведенный ниже текст и сохраните его как *впнпрофиле. XML* в той же папке, что и *усерцерт. ps1*. Измените следующий текст в соответствии с вашей средой:

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
1. Откройте сеанс PowerShell от имени администратора.

1. В PowerShell перейдите в папку, где находятся *усерцерт. ps1* и *впнпрофиле. XML* , и выполните следующую команду:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![мачинецерттест](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. В разделе **параметры VPN**найдите запись **усертест** и нажмите кнопку **подключить**.

1. Если подключение установлено успешно, вы успешно настроили Always On пользовательский туннель.

## <a name="clean-up-your-resources"></a>Очистка ресурсов

Чтобы удалить профиль, выполните следующие действия.

1. Выполните следующую команду:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Отключите подключение и снимите флажок **подключиться автоматически** .

![Очистка](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Дополнительная информация

Сведения об устранении проблем с подключением, которые могут возникнуть, см. в статье [проблемы с подключением "точка — сеть](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)" в Azure.
