---
title: Настройка AlwaysOn VPN-туннеля для VPN-шлюза
description: Действия по настройке AlwaysOn VPN-туннеля для VPN-шлюза
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 07/09/2019
ms.author: cherylmc
ms.openlocfilehash: 81822297dcf9370fc8ce7f7ce0285689c31606ce
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695794"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Настройка туннеля Always On VPN для устройства

Одна из новых функций клиента Windows 10 виртуальной частной сети (VPN) предоставляет возможность поддерживать VPN-подключение. Always On — это функция Windows 10, которая позволяет active профиля VPN, чтобы подключаться автоматически и остаются подключенными на основе триггеров — а именно: вход пользователя, изменения состояния сети или active экран устройства.

Шлюзы виртуальной сети Azure можно использовать с Windows 10 Always On для установления туннелей пользователя постоянно, а также туннели устройства в Azure. Эта статья поможет вам настроить Always ON VPN-туннель устройства.

Всегда на VPN-подключения включают два типа туннелей.

* **Туннель устройства** подключается к указанным серверам VPN, прежде чем пользователи входят в устройства. Сценарии подключения перед входом и целей управления устройства используют туннель устройства.

* **Туннель пользователя** подключается только после входа пользователя, в устройстве. Туннель пользователя позволяет пользователям получить доступ к ресурсам организации через VPN-серверов.

Туннель устройства и пользователя туннель работают независимо друг от друга с их профилями VPN. Они подключаются в то же время и можно использовать разные методы проверки подлинности и другие параметры конфигурации VPN соответствующим образом.

## <a name="1-configure-the-gateway"></a>1. Настройка шлюза

Настройка VPN-шлюза для использования IKEv2 и проверки подлинности на основе сертификатов с помощью этого [точка сеть статье](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. Настроить туннель пользователя

1. Установка сертификатов клиента на клиенте Windows 10, как показано в следующем [статье клиента VPN между сайтами](point-to-site-how-to-vpn-client-install-azure-cert.md). Сертификат должен находиться в Store текущего пользователя
2. Настройка клиента AlwaysOn VPN с помощью PowerShell, SCCM или Intune [эти инструкции](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

## <a name="3-configure-the-device-tunnel"></a>3. Настроить туннель устройства

Для того чтобы успешно установить туннель устройства должны выполняться следующие требования.

* Устройство должно быть присоединенный к домену компьютер под управлением Windows 10 Корпоративная и образования версии 1709 или более поздней версии.
* Туннель настраивается только для встроенного решения VPN Windows и устанавливается при использовании проверки подлинности сертификата компьютера IKEv2. 
* Туннель только одно устройство можно настроить для каждого устройства.

1. Установка сертификатов клиента на клиенте Windows 10, как показано в следующем [статье клиента VPN между сайтами](point-to-site-how-to-vpn-client-install-azure-cert.md). Сертификат должен находиться в хранилище локального компьютера.
1. Используйте [эти инструкции](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) для создания профиля VPN и настроить туннель устройства в контексте локальной системной учетной записи.

### <a name="configuration-example-for-device-tunnel"></a>Пример конфигурации для устройств туннеля

После настройки шлюза виртуальной сети и сертификат клиента установлен в хранилище локального компьютера на клиентском компьютере Windows 10, используйте следующие примеры для настройки канала клиента устройства.

1. Скопируйте следующий текст и сохраните его как ***devicecert.ps1***.

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
1. Скопируйте следующий текст и сохраните его как ***VPNProfile.xml*** в той же папке, что **devicecert.ps1**. Измените следующий текст в соответствии с вашей средой.

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
1. Скачайте **PsExec** из [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) и извлеките файлы в **C:\PSTools**.
1. С привилегиями администратора CMD запустите с помощью команды PowerShell:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![PowerShell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. В PowerShell перейдите к папке где **devicecert.ps1** и **VPNProfile.xml** расположены и выполните следующую команду:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Запустите **rasphone**.

   ![Rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Найдите **MachineCertTest** запись и нажмите кнопку **Connect**.

   ![Подключение](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Если соединение успешно установлено, перезагрузите компьютер. Туннель будет подключаться автоматически.

## <a name="cleanup"></a>Очистка

Чтобы удалить профиль, выполните следующую команду:

![Очистка](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Следующие шаги

Устранение неполадок, см. в разделе [неполадок подключения между сайтами Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
