---
title: Настройка брандмауэра веб-приложения версии 2 в настраиваемых правилах шлюза приложений с помощью Azure PowerShell
description: Узнайте, как настроить настраиваемые правила WAF v2 с помощью Azure PowerShell
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/30/2019
ms.author: victorh
ms.openlocfilehash: 8c307ac5553c2c333425b6c14b9b4da4a6582f62
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516803"
---
# <a name="configure-web-application-firewall-v2-on-application-gateway-with-a-custom-rule-using-azure-powershell"></a>Настройка брандмауэра веб-приложения версии 2 в шлюзе приложений с настраиваемым правилом с помощью Azure PowerShell

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

Настраиваемые правила позволяют создавать собственные правила, вычисляемые для каждого запроса, который проходит через брандмауэр веб-приложения (WAF) версии 2. Эти правила содержат более высокий приоритет, чем остальные правила в наборах управляемых правил. Настраиваемые правила имеют действие (для разрешения или блокировки), условие соответствия и оператор, позволяющие выполнить полную настройку.

В этой статье создается шлюз приложений WAF v2, использующий настраиваемое правило. Настраиваемое правило блокирует трафик, если заголовок запроса содержит User-Agent *evilbot*.

Дополнительные примеры настраиваемых правил см. в разделе [Создание и использование настраиваемых правил брандмауэра веб-приложения](create-custom-waf-rules.md) .

Если вы хотите выполнить Azure PowerShell в этой статье в одном непрерывном сценарии, который можно скопировать, вставить и запустить, см. статью [примеры PowerShell для шлюза приложений Azure](powershell-samples.md).

## <a name="prerequisites"></a>Технические условия

### <a name="azure-powershell-module"></a>модуль Azure PowerShell;

Чтобы установить и использовать Azure PowerShell локально для работы с этим скриптом, вам понадобится модуль Azure PowerShell 2.1.0 или последующей версии.

1. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps).
2. Чтобы создать подключение к Azure, выполните команду `Connect-AzAccount`.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="example-script"></a>Пример сценария

### <a name="set-up-variables"></a>Настройка переменных

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-vnet"></a>Создание виртуальной сети

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Создание статического общедоступного виртуального IP-адреса

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-frontend-port"></a>Создание пула и интерфейсного порта

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Создание прослушивателя, параметра HTTP, правила и автомасштабирования

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-two-custom-rules-and-apply-it-to-waf-policy"></a>Создание двух настраиваемых правил и их применение к политике WAF

```azurepowershell
# Create a User-Agent header custom rule 
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent
$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  
$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block
 
# Create a geo-match custom rule
$var2 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition2 = New-AzApplicationGatewayFirewallCondition -MatchVariable $var2 -Operator GeoMatch -MatchValue "US"  -NegationCondition $False
$rule2 = New-AzApplicationGatewayFirewallCustomRule -Name allowUS -Priority 14 -RuleType MatchRule -MatchCondition $condition2 -Action Allow

# Create a firewall policy
$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafpolicyNew -ResourceGroup $rgname -Location $location -CustomRule $rule,$rule2
```

### <a name="create-the-application-gateway"></a>Создание шлюза приложений

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection  $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts $fp01 -HttpListeners $listener01 `
  -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
  -WebApplicationFirewallConfig $wafConfig `
  -FirewallPolicy $wafPolicy
```

## <a name="next-steps"></a>Дальнейшие действия

[Дополнительные сведения о брандмауэре веб-приложения в шлюзе приложений](ag-overview.md)
