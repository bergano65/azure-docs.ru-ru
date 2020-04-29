---
title: Включить имя файла
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: feaf72de1d2c578d2b2d0df9e86ec0fbe0b49445
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371778"
---
Чтобы успешно установить туннель устройства, необходимо соблюдение следующих требований.

* Устройство должно быть присоединенным к домену компьютером под управлением Windows 10 Корпоративная или образовательных версий 1809 или более поздней версии.
* Туннель можно настроить только для встроенного решения VPN Windows и устанавливается с использованием IKEv2 с проверкой подлинности сертификата компьютера.
* Для каждого устройства можно настроить только один туннель устройства.

1. Установите клиентские сертификаты в клиенте Windows 10 с помощью [VPN-клиента "точка — сеть](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) ". Сертификат должен находиться в хранилище локального компьютера.
1. Создайте профиль VPN и настройте туннель устройства в контексте локальной системной учетной записи, выполнив [эти инструкции](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration).

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

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

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
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. В PowerShell перейдите в папку, где находятся **девицецерт. ps1** и **впнпрофиле. XML** , и выполните следующую команду:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![мачинецерттест](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. Запустите **Rasphone**.

   ![Rasphone](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. Найдите запись **мачинецерттест** и нажмите кнопку **подключить**.

   ![Подключение](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. Если подключение будет выполнено, перезагрузите компьютер. Туннель будет подключен автоматически.
