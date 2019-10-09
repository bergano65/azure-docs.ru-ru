---
title: Создание службы частной связи Azure с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как создать службу частной связи Azure с помощью Azure PowerShell
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 2f9b7b148900e827f4bfb17de1ef3cf05d8bbf10
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169151"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Создание службы частной связи с помощью Azure PowerShell
В этой статье показано, как создать службу частной связи в Azure с помощью Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать PowerShell локально, для работы с этой статьей потребуется последняя версия модуля Azure PowerShell. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Перед созданием частной ссылки необходимо создать группу ресурсов с помощью [New-азресаурцеграуп](/powershell/module/az.resources/new-azresourcegroup). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *WestCentralUS* :

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Создание виртуальной сети
Создайте виртуальную сеть для частной ссылки с помощью [New-азвиртуалнетворк](/powershell/module/az.network/new-azvirtualnetwork). В следующем примере создается виртуальная сеть с именем *myvnet* с подсетью для интерфейсной части (*frontendSubnet*), серверная часть (*backendSubnet*), Частная ссылка (*otherSubnet*):

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"  

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"  

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled" 

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet 
```
## <a name="create-internal-load-balancer"></a>Создание внутренней Load Balancer
Создайте внутренний Load Balancer (цен. категория "Стандартный") с помощью [New-азлоадбаланцер](/powershell/module/az.network/new-azloadbalancer). В следующем примере создается внутренняя Load Balancer (цен. категория "Стандартный") с использованием внешней IP-конфигурации, пробы, правила и серверного пула, созданного на предыдущих шагах:

```azurepowershell

$lbBackendName = "LB-backend" 
$lbFrontName = "LB-frontend" 
$lbName = "lb"
 
#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id 
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName 
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard 
```
## <a name="create-a-private-link-service"></a>Создание службы частной связи
Создайте службу частной связи с помощью [New-азпривателинксервице](/powershell/module/az.network/new-azloadbalancer).  В этом примере создается служба частной связи с именем *миплс* , использующая Load Balancer (цен. Категория "Стандартный") в группе ресурсов с именем *myResourceGroup*. 
```azurepowershell

$plsIpConfigName = "PLS-ipconfig" 
$plsName = "pls"
$peName = "pe" 
  
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5 

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig 

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig 
```

### <a name="get-private-link-service"></a>Получение службы частной связи
Получите сведения о службе Private Link с помощью [New-азпривателинксервице](/powershell/module/az.network/get-azprivatelinkservice) , как показано ниже.

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

На этом этапе служба закрытых ссылок успешно создана и готова к получению трафика. Обратите внимание, что в примере выше показано только создание службы Private Link с помощью PowerShell.  Мы не настроили серверные пулы подсистемы балансировки нагрузки или любое приложение в серверных пулах для прослушивания трафика. Если вы хотите просмотреть сквозные потоки трафика, настоятельно рекомендуем настроить приложение за стандартную подсистему балансировки нагрузки. 

Далее мы продемонстрируем, как сопоставлять эту службу с частной конечной точкой в другой виртуальной сети с помощью PowerShell. Опять же, пример ограничен созданием частной конечной точки и подключением к службе закрытых ссылок, созданной выше. Вы можете создать виртуальные машины в виртуальной сети, чтобы отправлять и получать трафик в закрытую конечную точку для создания сценария. 

## <a name="create-a-private-endpoint"></a>Создание частной конечной точки
### <a name="create-a-virtual-network"></a>Создание виртуальной сети
Создайте виртуальную сеть для частной конечной точки с помощью [New-азвиртуалнетворк](/powershell/module/az.network/new-azvirtualnetwork). В этом примере создается виртуальная сеть с именем *внетпе* in Resource Group с именем *myResourceGroup*:
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName myResourceGroup `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```

### <a name="create-a-private-endpoint"></a>Создание частной конечной точки
Создайте закрытую конечную точку для использования службы закрытых ссылок, созданной в виртуальной сети.
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Получение частной конечной точки
Получите IP-адрес частной конечной точки с `Get-AzPrivateEndpoint` следующим образом:

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>Утверждение подключения к частной конечной точке
Утвердите подключение к частной конечной точке со службой закрытых ссылок с помощью команды "утвердить-Азприватиндпоинтконнектион".

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>Следующие шаги
- Дополнительные сведения о [частной ссылке Azure](private-link-overview.md)
 
