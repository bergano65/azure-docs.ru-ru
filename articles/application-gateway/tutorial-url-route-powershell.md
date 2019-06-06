---
title: Маршрутизация веб-трафика на основе URL-адреса с помощью Azure PowerShell
description: Узнайте, как маршрутизировать веб-трафик на основе URL-адреса в определенные масштабируемые пулы серверов с помощью Azure PowerShell.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: c636ab9956b369702c8319d67a83e33070113857
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729503"
---
# <a name="route-web-traffic-based-on-the-url-using-azure-powershell"></a>Маршрутизация веб-трафика на основе URL-адреса с использованием Azure PowerShell

С помощью Azure PowerShell можно настраивать маршрутизацию веб-трафика в определенные серверные пулы на основе URL-адреса, который используется для доступа к приложению. В рамках этого руководства вы создадите [шлюз приложений Azure](application-gateway-introduction.md) с тремя серверными пулами, в которых используется служба [Масштабируемые наборы виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) У каждого серверного пула свое назначение. Например, они могут использоваться для данных, изображений и видео.  Если направлять трафик в отдельные пулы, пользователи будут получать нужную информацию в нужное время.

Чтобы включить маршрутизацию трафика, создайте [правила маршрутизации](application-gateway-url-route-overview.md), назначенные прослушивателям, которые ожидают передачи данных через определенные порты. После этого веб-трафик будет поступать на надлежащие серверы в пуле.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка сети
> * создание прослушивателей, сопоставления URL-путей и правил;
> * создание масштабируемых серверных пулов.

![Пример маршрутизации для URL-адресов](./media/tutorial-url-route-powershell/scenario.png)

Инструкции из этого руководства также можно выполнить, используя [интерфейс командной строки Azure](tutorial-url-route-cli.md) или [портал Azure](create-url-route-portal.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать PowerShell локально, то для работы с этим учебником вам понадобится модуль Azure PowerShell версии 1.0.0 или более поздней. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzAccount`, чтобы создать подключение к Azure.

Для создания ресурсов потребуется некоторое время, поэтому работа с этим руководством может занять до 90 минут.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Необходимо создать группу ресурсов, в которой будут размещены все ресурсы для приложения. 

Создайте группу ресурсов Azure с помощью командлета [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Создание сетевых ресурсов

Вы можете применить существующую виртуальную сеть или создать новую, но она в любом случае должна содержать отдельную подсеть только для шлюзов приложения. С помощью этого руководства вы создадите подсеть для шлюза приложений и еще одну подсеть для масштабируемых наборов. Также вы создадите общедоступный IP-адрес для доступа к ресурсам через шлюз приложения.

Создайте конфигурации подсетей с именами *myAGSubnet* и *myBackendSubnet*, выполнив командлет [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Создайте виртуальную сеть с именем *myVNet*, используя командлет [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) и конфигурации подсетей. Наконец, создайте общедоступный IP-адрес с именем *myAGPublicIPAddress*, выполнив командлет [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Эти ресурсы используются для обеспечения сетевого подключения к шлюзу приложений и связанным с ним ресурсам.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Создание шлюза приложений

В этом разделе объясняется, как создать ресурсы с поддержкой шлюза приложений и сам шлюз приложений. Вы создадите следующие ресурсы:

- *IP-конфигурации и интерфейсный порт* — используются для связывания созданной ранее подсети с шлюзом приложений и назначения порта для доступа к нему.
- *Пул по умолчанию* — все шлюзы приложения должны иметь по крайней мере один внутренний пул серверов.
- *Прослушиватель и правило по умолчанию* — прослушиватель по умолчанию ожидает передачи трафика через назначенный порт, а правило по умолчанию отправляет трафик в пул по умолчанию.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Создание IP-конфигураций и интерфейсного порта

Свяжите созданную ранее подсеть *myAGSubnet* со шлюзом приложений, используя командлет [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Назначьте шлюзу приложений адрес *myAGPublicIPAddress* с помощью командлета [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

$pip = Get-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress

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

### <a name="create-the-default-pool-and-settings"></a>Создание пула по умолчанию и настройка параметров

Создайте для шлюза приложений серверный пул по умолчанию с именем *appGatewayBackendPool* с помощью командлета [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Настройте параметры для серверного пула, используя командлет [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Создание прослушивателя по умолчанию и правила

Прослушиватель требуется, чтобы шлюз приложений правильно маршрутизировал трафик в серверный пул. В этом руководстве создается два прослушивателя. Первым создается базовый прослушиватель, который ожидает передачи данных по корневому URL-адресу. Вторым создается прослушиватель, который ожидает передачи данных по определенным URL-адресам.

Создайте прослушиватель по умолчанию с именем *myDefaultListener*, используя командлет [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener), с конфигурацией внешнего интерфейса и интерфейсным портом, созданными ранее. 

Правило требуется для того, чтобы указать прослушивателю, какой внутренний пул использовать для входящего трафика. Создайте базовое правило *rule1* с помощью командлета [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myDefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Создание шлюза приложений

Теперь, когда вы создали необходимые вспомогательные ресурсы, укажите параметры для шлюза приложений *myAppGateway* с помощью командлета [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku), а затем создайте шлюз с помощью командлета [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

Создание шлюза приложений может занять до 30 минут. Дождитесь успешного завершения развертывания, прежде чем переходить к следующему разделу. На этом этапе у вас есть готовый шлюз приложения, который ожидает передачи трафика через порт 80 и направляет его к пулу серверов по умолчанию.

### <a name="add-image-and-video-backend-pools-and-port"></a>Добавление внутреннего порта и внутренних пулов для изображений и видео

Добавьте в шлюз приложений серверные пулы с именами *imagesBackendPool* и *videoBackendPool* с помощью командлета [Add-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/add-azapplicationgatewaybackendaddresspool). Добавьте интерфейсный порт для пулов с помощью командлета [Add-AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport). Отправьте изменения в шлюз приложений с помощью командлета [Set-AzApplicationGateway](/powershell/module/az.network/set-azapplicationgateway).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

Add-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080

Set-AzApplicationGateway -ApplicationGateway $appgw
```

Обновление шлюза приложений также может занять до 20 минут.

### <a name="add-backend-listener"></a>Добавление серверного прослушивателя

Добавьте серверный прослушиватель с именем *backendListener*, необходимый для маршрутизации трафика, с помощью командлета [Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport

$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-url-path-map"></a>Добавление сопоставления URL-путей

Сопоставления URL-путей позволяют направлять запросы по определенным URL-адресам в определенные внутренние пулы. Создайте сопоставления URL-путей с именами *imagePathRule* и *videoPathRule* с помощью командлетов [New-AzApplicationGatewayPathRuleConfig](/powershell/module/az.network/new-azapplicationgatewaypathruleconfig) и [Add-AzApplicationGatewayUrlPathMapConfig](/powershell/module/az.network/add-azapplicationgatewayurlpathmapconfig).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$imagePool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

$defaultPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$imagePathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings

$videoPathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
    -Paths "/video/*" `
    -BackendAddressPool $videoPool `
    -BackendHttpSettings $poolSettings

Add-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rule"></a>Добавление правила маршрутизации

Правило маршрутизации связывает сопоставление URL-адресов с созданным прослушивателем. Добавьте правило с именем *rule2* с помощью командлета [Add-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$urlPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name rule2 `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>Создание масштабируемых наборов виртуальных машин

В этом примере вы создадите три масштабируемых набора виртуальных машин, которые поддерживают три созданных внутренних пула. Имена создаваемых масштабируемых наборов — *myvmss1*, *myvmss2* и *myvmss3*. Масштабируемый набор назначается серверному пулу при настройке параметров IP-адреса.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$imagesPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name videoBackendPool `
  -ApplicationGateway $appgw

for ($i=1; $i -le 3; $i++)
{
  if ($i -eq 1)
  {
     $poolId = $backendPool.Id
  }
  if ($i -eq 2) 
  {
    $poolId = $imagesPool.Id
  }
  if ($i -eq 3)
  {
    $poolId = $videoPool.Id
  }

  $ipConfig = New-AzVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId

  $vmssConfig = New-AzVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

  Set-AzVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest
    -OsDiskCreateOption FromImage

  Set-AzVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i

  Add-AzVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig

  New-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Установка служб IIS

Каждый масштабируемый набор содержит два экземпляра виртуальных машин, на которых установлен сервер IIS с тестовой страницей, позволяющей проверить работу шлюза приложений.

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i
  Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>Тестирование шлюза приложений

Используйте командлет [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress), чтобы получить общедоступный IP-адрес шлюза приложений. Скопируйте общедоступный IP-адрес и вставьте его в адресную строку браузера. Например, `http://52.168.55.24`, `http://52.168.55.24:8080/images/test.htm` или `http://52.168.55.24:8080/video/test.htm`.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Тестирование базового URL-адреса в шлюзе приложений](./media/tutorial-url-route-powershell/application-gateway-iistest.png)

Измените URL-адрес на http://&lt;ip-address&gt;:8080/images/test.htm, указав вместо заполнителя &lt;ip-address&gt; фактический IP-адрес. Результат должен быть примерно таким, как показано ниже:

![Тестирование URL-адреса изображений в шлюзе приложений](./media/tutorial-url-route-powershell/application-gateway-iistest-images.png)

Измените URL-адрес на http://&lt;ip-address&gt;:8080/video/test.htm и замените &lt;ip-address&gt; своим IP-адресом. Результат должен быть примерно таким, как показано ниже.

![Тестирование URL-адреса видео в шлюзе приложений](./media/tutorial-url-route-powershell/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если группа ресурсов, виртуальная машина и все связанные с ними ресурсы вам больше не требуются, их можно удалить. Для этого выполните командлет [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Настройка сети
> * создание прослушивателей, сопоставления URL-путей и правил;
> * создание масштабируемых серверных пулов.

> [!div class="nextstepaction"]
> [Перенаправление веб-трафика на основе URL-адреса](./tutorial-url-redirect-powershell.md)
