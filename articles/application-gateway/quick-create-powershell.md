---
title: Краткое руководство. Направление веб-трафика с помощью шлюза приложений Azure в Azure PowerShell | Документация Майкрософт
description: Узнайте, как с помощью Azure PowerShell создать шлюз приложений Azure, который направляет веб-трафик к виртуальным машинам в серверном пуле.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/11/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d41480def95137e1dc938c845f8cec1d59e26036
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521790"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-powershell"></a>Краткое руководство. Направление веб-трафика с помощью Шлюза приложений Azure в Azure PowerShell

В этом кратком руководстве показано, как быстро создать шлюз приложений с помощью Azure PowerShell.  Создав шлюз приложений, протестируйте его, чтобы убедиться в том, что он работает правильно. Шлюз приложений Azure позволяет направлять веб-трафик приложения к определенным ресурсам. Для этого портам назначаются прослушиватели, создаются определенные правила и в серверный пул добавляются соответствующие ресурсы. Чтобы упростить восприятие, в этой статье используется простая настройка с открытым интерфейсным IP-адресом, базовый прослушиватель для размещения одного сайта на этом шлюзе приложений, две виртуальные машины, используемые для серверного пула, и базовое правило маршрутизации запросов.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Предварительные требования

### <a name="azure-powershell-module"></a>модуль Azure PowerShell;

Чтобы установить и использовать Azure PowerShell локально для работы с этим руководством, вам понадобится модуль Azure PowerShell 1.0.0 или более поздней версии.

1. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). 
2. Чтобы создать подключение к Azure, выполните команду `Login-AzAccount`.

### <a name="resource-group"></a>Группа ресурсов

В Azure выделите связанные ресурсы группе ресурсов. Вы можете выбрать существующую группу ресурсов или создать новую. В этом примере мы создадим новую группу ресурсов с помощью командлета [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup). 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

### <a name="required-network-resources"></a>Необходимые сетевые ресурсы

В Azure для обмена между создаваемыми ресурсами необходима виртуальная сеть.  Подсеть шлюза приложений может содержать только шлюзы приложений. Другие ресурсы запрещены.  Вы можете создать новую подсеть для шлюза приложений или использовать уже существующую. В этом примере вы создаете две подсети: одну — для шлюза приложений, а вторую — для внутренних серверов. Вы можете настроить интерфейсный IP-адрес как общедоступный или частный, в зависимости от варианта использования шлюза приложений. В этом примере мы будем использовать общедоступный интерфейсный IP-адрес.

1. Создайте конфигурации подсетей, вызвав [New-AzVirtualNetworkSubnetConfig](/powershell/module/Az.network/new-Azvirtualnetworksubnetconfig).
2. Создайте виртуальную сеть с конфигурациями подсетей, вызвав [New-AzVirtualNetwork](/powershell/module/Az.network/new-Azvirtualnetwork). 
3. Создайте общедоступный IP-адрес, вызвав [New-AzPublicIpAddress](/powershell/module/Az.network/new-Azpublicipaddress). 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```
### <a name="backend-servers"></a>Внутренние серверы

Серверы могут состоять из сетевых адаптеров, масштабируемых наборов виртуальных машин, общедоступных IP-адресов, внутренних IP-адресов, полных доменных имен и мультитенантых серверных частей, таких как служба приложений Azure. В этом примере вы создадите две виртуальные машины для Azure, которые будут использоваться как внутренние серверы для шлюза приложений. Вы также установите службы IIS на виртуальных машинах, чтобы убедиться, что шлюз приложений успешно создан в Azure.

#### <a name="create-two-virtual-machines"></a>Создание двух виртуальных машин

1. Создайте сетевой интерфейс с помощью командлета [New-AzNetworkInterface](/powershell/module/Az.network/new-Aznetworkinterface). 
2. Создайте конфигурацию виртуальной машины, используя командлет [New-AzVMConfig](/powershell/module/Az.compute/new-Azvmconfig).
3. Создайте виртуальную машину с помощью команды [New-AzVM](/powershell/module/Az.compute/new-Azvm).

Во время запуска следующего примера кода для создания виртуальных машин Azure запрашивает ваши учетные данные. Введите *azureuser* в качестве имени пользователя и *Azure123456!* в качестве пароля:
    
```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $subnet.Id
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostics `
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

1. Используйте [New-AzApplicationGatewayIPConfiguration](/powershell/module/Az.network/new-Azapplicationgatewayipconfiguration), чтобы создать конфигурацию, которая позволяет связать созданную подсеть со шлюзом приложений. 
2. Используйте командлет [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/Az.network/new-Azapplicationgatewayfrontendipconfig), чтобы создать конфигурацию, которая позволяет назначить шлюзу приложений созданный вами общедоступный IP-адрес. 
3. Используйте командлет [New-AzApplicationGatewayFrontendPort](/powershell/module/Az.network/new-Azapplicationgatewayfrontendport), чтобы назначить порт 80 для доступа к шлюзу приложений.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

1. Создайте внутренний пул для шлюза приложений с помощью командлета [New-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/new-Azapplicationgatewaybackendaddresspool). 
2. Настройте параметры для серверного пула, используя командлет [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/Az.network/new-Azapplicationgatewaybackendhttpsettings).

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

Прослушиватель требуется для того, чтобы шлюз приложений правильно маршрутизировал трафик на внутренние пулы. Azure требует правило для того, чтобы указать прослушивателю, какой внутренний пул использовать для входящего трафика. 

1. Создайте прослушиватель, используя [New-AzApplicationGatewayHttpListener](/powershell/module/Az.network/new-Azapplicationgatewayhttplistener) с конфигурацией внешнего интерфейса и интерфейсным портом, созданными ранее. 
2. Используйте командлет [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/Az.network/new-Azapplicationgatewayrequestroutingrule), чтобы создать правило с именем *rule1*. 

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

Теперь, поскольку вы уже создали необходимые вспомогательные ресурсы, создайте шлюз приложений.

1. Используйте командлет [New-AzApplicationGatewaySku](/powershell/module/Az.network/new-Azapplicationgatewaysku), чтобы задать параметры для шлюза приложений.
2. Используйте командлет [New-AzApplicationGateway](/powershell/module/Az.network/new-Azapplicationgateway), чтобы создать шлюз приложений.

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

Для создания шлюза приложений не требуется устанавливать IIS. Но в рамках этого руководства они устанавливаются, чтобы проверить, создан ли шлюз приложений. Используйте IIS для тестирования шлюза приложений.

1. Запустите командлет [Get-AzPublicIPAddress](/powershell/module/Az.network/get-Azpublicipaddress), чтобы получить общедоступный IP-адрес шлюза приложений. 
2. Скопируйте и вставьте общедоступный IP-адрес в адресную строку браузера. Когда вы обновляете браузер, должно отобразиться имя виртуальной машины. Допустимый ответ подтверждает, что шлюз приложений успешно создан и может успешно подключиться к серверу.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Тестирование шлюза приложений](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам уже не нужны ресурсы, созданные с помощью шлюза приложений, удалите группу ресурсов. Удалив ее, вы также удалите шлюз приложений и все связанные с ним ресурсы. 

Удалите группу ресурсов, вызвав командлет [Remove-AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Manage web traffic with an application gateway using Azure PowerShell](./tutorial-manage-web-traffic-powershell.md) (Управление веб-трафиком с помощью шлюза приложений в Azure PowerShell)

