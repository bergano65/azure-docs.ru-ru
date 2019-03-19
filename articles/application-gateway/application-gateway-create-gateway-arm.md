---
title: Создание шлюза приложений с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как создать шлюз приложений с помощью Azure PowerShell.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurepowershell
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 412ede9895e6684d039dcad4df4189c8b3774d76
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313826"
---
# <a name="create-an-application-gateway-using-azure-powershell"></a>Создание шлюза приложений с помощью Azure PowerShell

Используя Azure PowerShell, вы можете создавать шлюзы приложений и управлять ими из командной строки или с помощью скриптов. В этом кратком руководстве показано, как создать сетевые ресурсы, внутренние серверы и шлюз приложений.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Если вы решили установить и использовать PowerShell локально, руководством модуль Azure PowerShell версии 1.0.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Создание сетевых ресурсов 

Создайте конфигурации подсетей с помощью [New AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Создание виртуальной сети с помощью [New AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) и конфигурации подсети. И наконец, создайте общедоступный IP-адрес с помощью [New AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Эти ресурсы используются для обеспечения сетевого подключения к шлюзу приложений и связанным с ним ресурсам.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```
## <a name="create-backend-servers"></a>Создание внутренних серверов

В этом примере вы создадите две виртуальные машины, которые будут использоваться как внутренние серверы для шлюза приложений. Вы также установите службы IIS на виртуальных машинах, чтобы убедиться, что шлюз приложений успешно создан.

### <a name="create-two-virtual-machines"></a>Создание двух виртуальных машин

Создайте сетевой интерфейс с [New AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Создайте конфигурацию виртуальной машины с [New AzVMConfig](/powershell/module/az.compute/new-azvmconfig). При запуске приведенных ниже команд запрашиваются учетные данные. Введите *azureuser* в качестве имени пользователя и *Azure123456!* в качестве пароля. Создайте виртуальную машину с помощью команды [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $vnet.Subnets[1].Id
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2
  $vm = Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  $vm = Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  $vm = Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  $vm = Set-AzVMBootDiagnostics `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="create-an-application-gateway"></a>Создание шлюза приложений

### <a name="create-the-ip-configurations-and-frontend-port"></a>Создание IP-конфигураций и интерфейсного порта

Используйте [New AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) для создания конфигурации, который связывает подсети, созданный ранее с помощью шлюза приложений. Используйте [New AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) для создания конфигурации, назначающий общедоступный IP-адрес, созданный ранее на шлюзе приложений. Используйте [New AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) Чтобы назначить порт 80, который будет использоваться для доступа к шлюзу приложений.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$pip = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool"></a>Создание внутреннего пула

Используйте [New AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) создание серверного пула для шлюза приложений. Настройте параметры для серверного пула, используя [New AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Создание прослушивателя и добавление правила

Прослушиватель требуется для того, чтобы шлюз приложений правильно маршрутизировал трафик на внутренние пулы. Создайте прослушиватель, используя [New AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) с конфигурацией внешнего интерфейса и интерфейсным портом, созданными ранее. Правило требуется для того, чтобы указать прослушивателю, какой внутренний пул использовать для входящего трафика. Используйте [New AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) создать правило с именем *rule1*.

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Создание шлюза приложений

Теперь, когда вы создали необходимые вспомогательные ресурсы, используйте [New AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) укажите параметры для шлюза приложений, а затем использовать [New AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) для его создания.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Тестирование шлюза приложений

Используйте [Get AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) получить общедоступный IP-адрес шлюза приложений. Скопируйте общедоступный IP-адрес и вставьте его в адресную строку браузера.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Тестирование шлюза приложений](./media/application-gateway-create-gateway-arm/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если больше не нужен, можно использовать [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) команду, чтобы удалить группу ресурсов, шлюз приложений, и все связанные ресурсы.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали группу ресурсов, сетевые ресурсы и внутренние серверы. Затем с помощью этих ресурсов вы создали шлюз приложения. Чтобы узнать больше о шлюзах приложений и связанных с ними ресурсах, перейдите к статьям с инструкциями.

