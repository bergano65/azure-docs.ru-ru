---
title: Настройка настраиваемых правил брандмауэра веб-приложения v2 с помощью Azure PowerShell
description: Узнайте, как настроить настраиваемые правила брандмауэра веб-приложения v2 с помощью Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 9e50b47e22f5760c213cd0cafad82ecca592dec8
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263742"
---
# <a name="configure-web-application-firewall-v2-custom-rules-by-using-azure-powershell"></a>Настройка настраиваемых правил брандмауэра веб-приложения v2 с помощью Azure PowerShell

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

С помощью настраиваемых правил можно создавать собственные правила, которые оцениваются для каждого запроса, который проходит через брандмауэр веб-приложения (WAF). Эти правила содержат более высокий приоритет, чем остальные правила в наборах управляемых правил. Чтобы разрешить полную настройку, пользовательские правила имеют действие (для разрешения или блокировки), условие соответствия и оператор.

В этой статье описывается создание шлюза приложений Azure WAF v2, использующего настраиваемое правило. Настраиваемое правило блокирует трафик, если заголовок запроса содержит User-Agent *evilbot*.

Дополнительные примеры настраиваемых правил см. в разделе [Создание и использование пользовательских правил брандмауэра веб-приложения](create-custom-waf-rules.md).

Чтобы запустить Azure PowerShell код в этой статье в одном непрерывном сценарии, который можно скопировать, вставить и запустить, ознакомьтесь с [примерами PowerShell для шлюза приложений Azure](powershell-samples.md).

## <a name="prerequisites"></a>Предварительные требования
* [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Необходим модуль Azure PowerShell. Если вы решили установить и использовать Azure PowerShell локально, для этого скрипта требуется Azure PowerShell Module версии 2.1.0 или более поздней. Выполните следующее:

  1. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps).
  2. Чтобы создать подключение к Azure, выполните команду `Connect-AzAccount`.

## <a name="example-script"></a>Пример сценария

### <a name="set-up-variables"></a>Настройка переменных

Выполните следующий код:

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Выполните следующий код:

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-virtual-network"></a>Создание виртуальной сети

Выполните следующий код:

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Создание статического общедоступного виртуального IP-адреса

Выполните следующий код:

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-a-pool-and-front-end-port"></a>Создание пула и внешнего порта

Выполните следующий код:

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Создание прослушивателя, параметра HTTP, правила и автомасштабирования

Выполните следующий код:

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

### <a name="create-the-custom-rule-and-apply-it-to-waf-policy"></a>Создание настраиваемого правила и его применение к политике WAF

Выполните следующий код:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  

$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafPolicy -ResourceGroup $rgname -Location $location -CustomRule $rule

$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="create-an-application-gateway"></a>Создание шлюза приложений

Выполните следующий код:

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

## <a name="next-steps"></a>Следующие шаги

[Дополнительные сведения о брандмауэре веб-приложения](waf-overview.md)
