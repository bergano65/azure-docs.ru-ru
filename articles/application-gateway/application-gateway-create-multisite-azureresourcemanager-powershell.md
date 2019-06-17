---
title: Создание шлюза приложений с несколькими сайтами с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как создать шлюз приложений, на котором размещено несколько сайтов, с помощью Azure PowerShell.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 11962d8958811bc87fb94dc6fe78cb760fdaa20e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66729715"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-azure-powershell"></a>Создание шлюза приложений с несколькими сайтами с помощью Azure PowerShell

Чтобы настроить [размещение нескольких веб-сайтов](application-gateway-multi-site-overview.md) при создании [шлюза приложений](application-gateway-introduction.md), можно использовать Azure PowerShell. В этом руководстве мы создадим внутренние пулы с использованием масштабируемых наборов виртуальных машин. Затем вы настроите прослушиватели и правила на основе принадлежащих вам доменов, чтобы обеспечить передачу веб-трафика на соответствующие серверы в пулах. В этом руководстве предполагается, что вам принадлежат несколько доменов. Для примера здесь используются домены *www.contoso.com* и *www.fabrikam.com*.

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * Настройка сети
> * Создание шлюза приложений
> * создание прослушивателей и правил маршрутизации;
> * создание масштабируемых наборов виртуальных машин с внутренними пулами.
> * создание записи CNAME в домене.

![Пример маршрутизации нескольких сайтов](./media/application-gateway-create-multisite-azureresourcemanager-powershell/scenario.png)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать PowerShell локально, то для работы с этим учебником вам понадобится модуль Azure PowerShell версии 1.0.0 или более поздней. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. Создайте группу ресурсов Azure с помощью командлета [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Создание сетевых ресурсов

Настройте подсети с именами *myBackendSubnet* и *myAGSubnet*, выполнив командлет [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Создайте виртуальную сеть с именем *myVNet*, используя командлет [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) с конфигурациями подсетей. Наконец, создайте общедоступный IP-адрес с именем *myAGPublicIPAddress*, выполнив командлет [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Эти ресурсы используются для обеспечения сетевого подключения к шлюзу приложений и связанным с ним ресурсам.

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

### <a name="create-the-ip-configurations-and-frontend-port"></a>Создание IP-конфигураций и интерфейсного порта

Свяжите созданную ранее подсеть *myAGSubnet* со шлюзом приложений, используя командлет [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Назначьте шлюзу приложений адрес *myAGPublicIPAddress* с помощью командлета [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
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

### <a name="create-the-backend-pools-and-settings"></a>Создание внутреннего пула и настройка параметров

Создайте внутренние пулы *contosoPool* и *fabrikamPool* для шлюза приложений с помощью [New AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Настройте параметры для пула, используя командлет [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$contosoPool = New-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool 
$fabrikamPool = New-AzApplicationGatewayBackendAddressPool `
  -Name fabrikamPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listeners-and-rules"></a>Создание прослушивателей и правил

Прослушиватель позволяет шлюзу приложений правильно маршрутизировать трафик на внутренние пулы. В этом руководстве создаются прослушиватели для каждого из двух ваших доменов. В этом примере создаются прослушиватели для доменов *www.contoso.com* и *www.fabrikam.com*.

Создайте прослушиватели *contosoListener* и *fabrikamListener* с помощью [New AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) с конфигурацией внешнего интерфейса и интерфейса порт, созданный ранее. Правила требуются для того, чтобы указать прослушивателям, какой внутренний пул использовать для входящего трафика. Создайте базовые правила *contosoRule* и *fabrikamRule* с помощью [New AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$contosolistener = New-AzApplicationGatewayHttpListener `
  -Name contosoListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.contoso.com"
$fabrikamlistener = New-AzApplicationGatewayHttpListener `
  -Name fabrikamListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.fabrikam.com"
$contosoRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name contosoRule `
  -RuleType Basic `
  -HttpListener $contosoListener `
  -BackendAddressPool $contosoPool `
  -BackendHttpSettings $poolSettings
$fabrikamRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name fabrikamRule `
  -RuleType Basic `
  -HttpListener $fabrikamListener `
  -BackendAddressPool $fabrikamPool `
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
  -BackendAddressPools $contosoPool, $fabrikamPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $contosoListener, $fabrikamListener `
  -RequestRoutingRules $contosoRule, $fabrikamRule `
  -Sku $sku
```

## <a name="create-virtual-machine-scale-sets"></a>Создание масштабируемых наборов виртуальных машин

В этом примере вы создадите два масштабируемых набора виртуальных машин (*myvmss1* и *myvmss2*), которые поддерживают два созданных внутренних пула. Каждый масштабируемый набор содержит два экземпляра виртуальной машины, на которых устанавливаются службы IIS. Масштабируемый набор назначается серверному пулу при настройке параметров IP-адреса.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$contosoPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool `
  -ApplicationGateway $appgw
$fabrikamPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name fabrikamPool `
  -ApplicationGateway $appgw
for ($i=1; $i -le 2; $i++)
{
  if ($i -eq 1) 
  {
    $poolId = $contosoPool.Id
  }
  if ($i -eq 2)
  {
    $poolId = $fabrikamPool.Id
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

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 2; $i++)
{
  $vmss = Get-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -VMScaleSetName myvmss$i
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

## <a name="create-cname-record-in-your-domain"></a>Создание записи CNAME в домене

После создания шлюза приложений с общедоступным IP-адресом можно получить DNS-адрес и использовать его для создания записи CNAME в своем домене. Чтобы получить DNS-адрес шлюза приложений, используйте командлет [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). Скопируйте значение *fqdn* для DNSSettings и используйте его в качестве значения создаваемой записи CNAME. Использовать записи A не рекомендуется, так как виртуальный IP-адрес может измениться после перезапуска шлюза приложений.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## <a name="test-the-application-gateway"></a>Тестирование шлюза приложений

В адресной строке браузера введите имя домена. Например, http://www.contoso.com.

![Проверка сайта contoso в шлюзе приложений](./media/application-gateway-create-multisite-azureresourcemanager-powershell/application-gateway-iistest.png)

Введите адрес другого домена. Результат должен быть примерно таким:

![Проверка сайта fabrikam в шлюзе приложений](./media/application-gateway-create-multisite-azureresourcemanager-powershell/application-gateway-iistest2.png)

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка сети
> * Создание шлюза приложений
> * создание прослушивателей и правил маршрутизации;
> * создание масштабируемых наборов виртуальных машин с внутренними пулами.
> * создание записи CNAME в домене.

> [!div class="nextstepaction"]
> [Дополнительные сведения о возможностях шлюза приложений](application-gateway-introduction.md)
