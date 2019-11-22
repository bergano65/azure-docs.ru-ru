---
title: Настройка VPN-подключения "точка — сеть" (P2S) в Windows для использования с Файлами Azure | Документация Майкрософт
description: Как настроить VPN-подключения "точка — сеть" (P2S) в Windows для использования с файлами Azure
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 527ab905997d18433d1dba5c16ee67c8146f5afa
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73126460"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Настройка VPN-подключения "точка — сеть" (P2S) в Windows для использования с файлами Azure
Вы можете использовать VPN-подключение "точка — сеть" (P2S) для подключения файловых ресурсов Azure по протоколу SMB вне Azure, не открывая порт 445. VPN-подключение "точка — сеть" — это VPN-подключение между Azure и отдельным клиентом. Чтобы использовать VPN-подключение P2S к службе "Файлы Azure", необходимо настроить это подключение для каждого клиента, которому требуется подключиться. Если у вас много клиентов, которым требуется подключение к общим файловым ресурсам Azure из локальной сети, вместо подключения "точка — сеть" для каждого клиента можно использовать VPN-подключение "сеть — сеть" (S2S). Дополнительные сведения см. в статье [Настройка VPN-подключения "сеть — сеть" для использования с Файлами Azure](storage-files-configure-s2s-vpn.md).

Мы настоятельно рекомендуем ознакомиться со статей [Azure Files networking considerations](storage-files-networking-overview.md) (рекомендации по сети для службы "Файлы Azure"), чтобы подробно ознакомиться с возможностями сети, доступными для службы "Файлы Azure", перед продолжением работы с этой статьей.

В статье подробно описано, как настроить VPN-подключение "точка — сеть" в Windows (клиент Windows и Windows Server) для непосредственного локального подключения общих файловых ресурсов Azure. Если вы хотите направить трафик Синхронизации файлов Azure через VPN, см. статью [Параметры брандмауэра и прокси-сервера Синхронизации файлов Azure](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Предварительные требования
- Последняя версия модуля Azure PowerShell. Дополнительные сведения об установке Azure PowerShell см. в статье [Установка Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) и выберите операционную систему. Если вы предпочитаете использовать Azure CLI в Windows, обратите внимание, что приведенные ниже инструкции представлены для Azure PowerShell.

- Частная зона DNS в модуле Azure PowerShell. В настоящее время она не распространяется в составе модуля Azure PowerShell, поэтому ее можно установить с помощью следующего метода:
    ```PowerShell
    if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
        Install-Module -Name Az.PrivateDns -AllowClobber -AllowPrerelease
    } else {
        Install-Module -Name Az.PrivateDns -RequiredVersion "0.1.3"
    }

    Import-Module -Name Az.PrivateDns
    ```  

- Общая папка Azure, которую вы хотите подключить локально. Вы можете использовать общую папку категории [Стандартный](storage-how-to-create-file-share.md) или [Премиум](storage-how-to-create-premium-fileshare.md) с VPN-подключением "точка — сеть".

## <a name="deploy-a-virtual-network"></a>развертывать виртуальную сеть;
Чтобы получить доступ к общей папке Azure и другим ресурсам Azure из локальной среды через VPN-подключение "точка — сеть", необходимо создать виртуальную сеть или VNet. VPN-подключение P2S, которое будет создано автоматически, — это мост между локальным компьютером Windows и этой виртуальной сетью Azure.

В следующем примере PowerShell создает виртуальную сеть Azure с тремя подсетями: одна для конечной точки службы учетной записи хранения, одна для частной конечной точки учетной записи хранения, которая необходима для доступа к учетной записи хранения в локальной среде без создания пользовательской маршрутизации для общедоступного IP-адреса учетной записи хранения, которая может измениться, и одна для шлюза виртуальной сети, предоставляющего службу VPN. 

Не забудьте заменить `<region>`, `<resource-group>` и `<desired-vnet-name>` соответствующими значениями из своей среды.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="restrict-the-storage-account-to-the-virtual-network"></a>Ограничение учетной записи хранения виртуальной сетью
По умолчанию при создании учетной записи хранения доступ к ней можно получить из любой точки мира при наличии средств проверки подлинности запроса (например, с помощью идентификатора Active Directory или ключа учетной записи хранения). Чтобы ограничить доступ к этой учетной записи хранения только что созданной виртуальной сети, необходимо создать набор сетевых правил, который разрешает доступ в виртуальной сети и запрещает все остальные права доступа.

Для ограничения доступа к учетной записи хранения для виртуальной сети требуется использовать конечную точку службы. Конечная точка службы — это сетевая конструкция, с помощью которой можно настроить доступ к общедоступному DNS- или IP-адресу для работы только в пределах виртуальной сети. Так как не гарантировано, что общедоступный IP-адрес останется прежним, мы в конечном счете хотим использовать для учетной записи хранилища частную конечную точку, а не конечную точку службы, однако ограничить учетную запись хранилища можно только в том случае, если доступ к конечной точке службы также будет предоставлен.

Не забудьте заменить `<storage-account-name>` учетной записью хранения, к которой требуется получить доступ.

```PowerShell
$storageAccountName = "<storage-account-name>"

$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName

$networkRule = Add-AzStorageAccountNetworkRule `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -VirtualNetworkResourceId $serviceEndpointSubnet.Id

Update-AzStorageAccountNetworkRuleSet `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Bypass AzureServices `
    -DefaultAction Deny `
    -VirtualNetworkRule $networkRule | Out-Null
``` 

## <a name="create-a-private-endpoint-preview"></a>Создание частной конечной точки (предварительная версия)
При создании частной конечной точки для учетной записи хранения она получает IP-адрес в пространстве IP-адресов виртуальной сети. При подключении общей папки Azure из локальной среды с помощью этого частного IP-адреса правила маршрутизации, автоматически определяемые установкой VPN, будут маршрутизировать запрос на подключение к учетной записи хранения через VPN. 

```PowerShell
$internalVnet = Get-AzResource `
    -ResourceId $virtualNetwork.Id `
    -ApiVersion "2019-04-01"

$internalVnet.Properties.subnets[1].properties.privateEndpointNetworkPolicies = "Disabled"
$internalVnet | Set-AzResource -Force | Out-Null

$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
    -Name "myConnection" `
    -PrivateLinkServiceId $storageAccount.Id `
    -GroupId "file"

$privateEndpoint = New-AzPrivateEndpoint `
    -ResourceGroupName $resourceGroupName `
    -Name "$storageAccountName-privateEndpoint" `
    -Location $region `
    -Subnet $privateEndpointSubnet `
    -PrivateLinkServiceConnection $privateEndpointConnection

$zone = Get-AzPrivateDnsZone -ResourceGroupName $resourceGroupName
if ($null -eq $zone) {
    $zone = New-AzPrivateDnsZone `
        -ResourceGroupName $resourceGroupName `
        -Name "privatelink.file.core.windows.net"
} else {
    $zone = $zone[0]
}

$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName $resourceGroupName `
    -ZoneName $zone.Name `
    -Name ($virtualNetwork.Name + "-link") `
    -VirtualNetworkId $virtualNetwork.Id

$internalNic = Get-AzResource `
    -ResourceId $privateEndpoint.NetworkInterfaces[0].Id `
    -ApiVersion "2019-04-01"

foreach($ipconfig in $internalNic.Properties.ipConfigurations) {
    foreach($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.', 2)[0]
        $dnsZone = $fqdn.split('.', 2)[1]
        New-AzPrivateDnsRecordSet `
            -ResourceGroupName $resourceGroupName ` 
            -Name $recordName `
            -RecordType A `
            -ZoneName $zone.Name `
            -Ttl 600 `
            -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
                -IPv4Address $ipconfig.properties.privateIPAddress) | Out-Null
    }
}
```

## <a name="create-root-certificate-for-vpn-authentication"></a>Создание корневого сертификата для проверки подлинности VPN
Чтобы VPN-подключения с локальных компьютеров Windows прошли проверку подлинности для доступа к виртуальной сети, необходимо создать два сертификата: корневой сертификат, который будет предоставляться шлюзу виртуальной машины, и сертификат клиента, который будет подписываться с помощью корневого сертификата. Следующий пример PowerShell создает корневой сертификат; сертификат клиента будет создан после создания шлюза виртуальной сети Azure с помощью данных со шлюза. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Развертывание шлюза виртуальной сети
Шлюз виртуальной сети — это служба, к которой будут подключаться ваши локальные компьютеры Windows. Развертывание этой службы требует двух основных компонентов: общедоступный IP-адрес, который будет определять шлюз для ваших клиентов, где бы они ни находились, и корневой сертификат, созданный вами ранее, который будет использоваться для аутентификации клиентов.

Не забудьте заменить `<desired-vpn-name-here>` именем, которое вы хотели бы использовать для этих ресурсов.

> [!Note]  
> Развертывание шлюза виртуальной сети Azure может длиться до 45 минут. При развертывании ресурса этот сценарий PowerShell будет заблокирован для завершения развертывания. Это ожидаемое поведение.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName ` 
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>Создание сертификата клиента
Сертификат клиента создается с универсальным кодом ресурса (URI) шлюза виртуальной сети. Этот сертификат подписан с помощью корневого сертификата, созданного ранее.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>Настройка VPN-клиента
Шлюз виртуальной сети Azure создаст загружаемый пакет с файлами конфигурации, необходимыми для инициализации VPN-подключения на локальном компьютере с Windows. Мы настроим VPN-подключение с помощью функции [Always On VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/) Windows 10/Windows Server 2016+. Этот пакет также содержит исполняемые пакеты, которые будут настраивать устаревший VPN-клиент Windows, если это необходимо. В этом разделе используется "Always On VPN", а не устаревший VPN-клиент Windows, так как VPN-клиент "Always On" позволяет конечным пользователям подключаться и отключаться от VPN Azure без прав администратора. 

Следующий сценарий установит сертификат клиента, необходимый для проверки подлинности в шлюзе виртуальной сети, скачает и установит пакет VPN. Не забудьте заменить `<computer1>` и `<computer2>` на нужные компьютеры. Этот сценарий можно выполнить на любом количестве компьютеров, добавив дополнительные сеансы PowerShell в массив `$sessions`. Используемая учетная запись должна иметь права администратора на каждом из этих компьютеров. Если один из этих компьютеров является локальным компьютером, из которого выполняется сценарий, необходимо запустить сценарий из сеанса PowerShell с повышенными привилегиями. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer ` 
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Подключение файлового ресурса Azure
Теперь, когда вы настроили VPN-подключение "точка — сеть", его можно использовать для подключения общей папки Azure на компьютерах, которые вы настраиваете с помощью PowerShell. В следующем примере выполняется подключение общей папки, перечисление корневого каталога общего ресурса для подтверждения того, что общая папка подключена, и отключение общей папки. К сожалению, постоянно подключать общую папку через удаленное взаимодействие PowerShell невозможно. Сведения о постоянном подключении см. в статье [Использование общей папки Azure в Windows](storage-how-to-use-files-windows.md). 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>См. также
- [Рекомендации по работе с сетями для обеспечения прямого доступа к общей папке](storage-files-networking-overview.md)
- [Настройка VPN-подключения "точка — сеть" (P2S) в Linux для использования с Файлами Azure](storage-files-configure-p2s-vpn-linux.md)
- [Настройка VPN-подключения "сеть — сеть" (S2S) для использования с Файлами Azure](storage-files-configure-s2s-vpn.md)