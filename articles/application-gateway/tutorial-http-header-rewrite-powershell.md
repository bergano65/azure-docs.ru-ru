---
title: Повторное создание заголовков HTTP в шлюзе приложений Azure
description: Статья содержит сведения о том, как создать шлюз приложений Azure и как переписывать заголовки HTTP с помощью Azure PowerShell.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 8bc0d53080d0653ae630765d8a656cbe8d50b24c
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971575"
---
# <a name="tutorial-create-an-application-gateway-and-rewrite-http-headers"></a>Руководство. Создание шлюза приложений и перезапись заголовков HTTP

Для настройки [правил перезаписи заголовков запросов и ответов HTTP](rewrite-http-headers.md) при создании [SKU автоматического масштабирования и шлюза приложений, избыточного между зонами](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant), можно использовать Azure PowerShell.

> [!IMPORTANT] 
> SKU автоматического масштабирования и шлюза приложений, избыточного между зонами, на данный момент доступен в общедоступной предварительной версии. Предварительная версия предоставляется без соглашения об уровне обслуживания. Не рекомендуем использовать ее в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
>
> * Создание автоматически масштабируемой виртуальной сети.
> * Создание зарезервированного общедоступного IP-адреса
> * Настройка инфраструктуры шлюза приложений.
> * Укажите конфигурацию правила перезаписи заголовка HTTP.
> * Настройка автомасштабирования
> * Создание шлюза приложений
> * Тестирование шлюза приложений

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством требуется запустить Azure PowerShell в локальной среде. Необходим модуль Az 1.0.0 или более поздней версии. Чтобы узнать версию, выполните команду `Import-Module Az` и затем команду `Get-Module Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). После проверки версии PowerShell выполните командлет `Login-AzAccount`, чтобы создать подключение к Azure.

## <a name="sign-in-to-azure"></a>Вход в Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте группу ресурсов в одном из доступных расположений.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Создайте виртуальную сеть с одной выделенной подсетью для автоматически масштабируемого шлюза приложений. В настоящее время в каждой выделенной подсети можно развернуть только один автоматически масштабируемый шлюз приложений.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Создание зарезервированного общедоступного IP-адреса

Укажите метод распределения PublicIPAddress как **статический**. Виртуальный IP-адрес автоматически масштабируемого шлюза приложений может быть только статическим. Динамические IP-адреса не поддерживаются. Поддерживается только номер SKU "Стандартный" для PublicIpAddress.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Получение сведений

Извлеките сведения о группе ресурсов, подсети и IP-адресе в локальный объект, чтобы создать сведения об IP-конфигурации шлюза приложений.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Настройка инфраструктуры

Настройте IP-конфигурацию, IP-конфигурацию внешнего интерфейса, внутренний пул, параметры HTTP, сертификат, порт, прослушиватель и правило в формате, идентичном конфигурации имеющегося шлюза приложений ценовой категории "Стандартный". В новом номере SKU используется та же объектная модель, что и в номере SKU "Стандартный".

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Укажите конфигурацию правила перезаписи заголовка HTTP.

Настройте новые объекты, необходимые для перезаписи заголовков HTTP.

- **RequestHeaderConfiguration**. Этот объект используется для указания полей заголовка запроса, которые нужно перезаписать, и нового значения, которое должно быть записано в этих полях заголовка.
- **ResponseHeaderConfiguration**. Этот объект используется для указания полей заголовка ответа, которые нужно перезаписать, и нового значения, которое должно быть записано в этих полях заголовка.
- **ActionSet**. Этот объект содержит конфигурации запросов и ответов заголовков, указанных выше. 
- **RewriteRule**. Этот объект содержит все указанные выше наборы действий *actionSets*. 
- **RewriteRuleSet**. Этот объект содержит все правила перезаписи *rewriteRules* и должен быть подключен к базовому правилу или правилу маршрутизации запроса на основе путей.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Укажите правило маршрутизации

Создайте правило маршрутизации запроса. После создания эта конфигурация перезаписи присоединяется к прослушивателю источника с помощью правила маршрутизации. При использовании базового правила маршрутизации конфигурация перезаписи связывается с прослушивателем источника и выполняется перезапись глобального заголовка. При использовании правила маршрутизации на основе пути конфигурация перезаписи заголовка определяется в сопоставлении URL-пути. Поэтому она применяется только к области конкретного пути на сайте. Ниже создается базовое правило маршрутизации, и к нему присоединяется набор правил перезаписи.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Настройка автомасштабирования

Теперь можно указать конфигурацию автомасштабирования для шлюза приложений. Поддерживаются два типа конфигурации автомасштабирования.

* **Режим фиксированной емкости**. В этом режиме шлюз приложений не применяет автомасштабирование и использует фиксированное число единиц масштабирования.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Режим автомасштабирования**. В этом режиме шлюз приложений автоматически масштабируется в соответствии с шаблоном трафика приложений.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Создание шлюза приложений

Создайте шлюз приложений с зонами избыточности и конфигурацией автомасштабирования.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Тестирование шлюза приложений

Используйте командлет Get-AzureRmPublicIPAddress, чтобы получить общедоступный IP-адрес шлюза приложений. Скопируйте общедоступный IP-адрес или DNS-имя и вставьте его в адресную строку браузера.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Очистка ресурсов

Сначала изучите ресурсы, которые были созданы с помощью шлюза приложений. Потом выполните команду `Remove-AzResourceGroup`, чтобы удалить группу ресурсов, шлюз приложений и все связанные ресурсы, если они вам больше не нужны.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание шлюза приложений с правилами маршрутизации на основе URL-путей](./tutorial-url-route-powershell.md)
