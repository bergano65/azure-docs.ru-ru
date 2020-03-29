---
title: Создание частного почтового сервиса Azure с помощью Azure PowerShell Документы Майкрософт
description: Узнайте, как создать частную службу ссылки Azure с помощью Azure PowerShell
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 225ae9d07cc6df2fa809e250083ee6007ab2f945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932083"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Создание службы частной ссылки с помощью Azure PowerShell
В этой статье показано, как создать частную службу ссылки в Azure с помощью Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решите установить и использовать PowerShell локально, эта статья требует последней версии модуля Azure PowerShell. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Прежде чем создать приватную ссылку, необходимо создать группу ресурсов с [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Следующий пример создает группу ресурсов под названием *myResourceGroup* в расположении *WestCentralUS:*

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Создание виртуальной сети
Создайте виртуальную сеть для вашей личной связи с [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Следующий пример создает виртуальную сеть под названием *myvnet* с подсетью для frontend (*frontendSubnet*), бэкэнд *(backendSubnet*), частная ссылка (*otherSubnet*):

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
## <a name="create-internal-load-balancer"></a>Создание внутреннего баланса нагрузки
Создайте внутренний балансировку стандартной нагрузки с [помощью New-AzLoadBalancer.](/powershell/module/az.network/new-azloadbalancer) Следующий пример создает внутренний балансер стандартной нагрузки с использованием конфигурации IP-адреса, зонда, правила и бэкэнда, созданного на предыдущих этапах:

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
## <a name="create-a-private-link-service"></a>Создание службы "Приватный канал"
Создайте частную связь с [New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  Этот пример создает частную ссылку службы под названием *myPLS* с помощью standard Load Balancer в ресурсной группе под названием *myResourceGroup*. 
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

### <a name="get-private-link-service"></a>Получить частную ссылку
Получить подробную информацию о вашей частной службе связи с [Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) следующим образом:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

На этом этапе ваша служба Private Link успешно создана и готова к приему трафика. Обратите внимание, что приведенный выше пример предназначен только для демонстрации создания службы private Link с помощью PowerShell.  Мы не настроили пулы бэкэнда нагрузочика или любое приложение в пулах бэкэнда, чтобы прослушать трафик. Если вы хотите видеть конечные до конца транспортные потоки, настоятельно рекомендуется настроить приложение по стандартному балансулизатору нагрузки. 

Далее мы продемонстрируем, как сопоставить эту услугу с частной конечной точкой в различных VNet с помощью PowerShell. Опять же, пример ограничивается созданием частной конечный точки и подключением к службе private Link, созданной выше. Вы можете создать виртуальные машины в виртуальной сети для отправки/получения трафика в частную конечную точку для построения сценария. 

## <a name="create-a-private-endpoint"></a>Создание частной конечной точки
### <a name="create-a-virtual-network"></a>Создание виртуальной сети
Создайте виртуальную сеть для вашей частной конечной точки с [Помощью New-AzVirtualNetwork.](/powershell/module/az.network/new-azvirtualnetwork) Этот пример создает виртуальную сеть под названием *vnetPE* в группе ресурсов под названием *myResourceGroup:*
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```

### <a name="create-a-private-endpoint"></a>Создание частной конечной точки
Создайте частную конечную точку для потребления частного сервиса ссылки, созданного выше в виртуальной сети:
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Получить частную конечную точку
Получить IP-адрес частной конечной точки следующим `Get-AzPrivateEndpoint` образом:

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>Утвердить приватное соединение конечных точек
Утвердить приватное соединение конечных точек к частной службе связи с 'Approve-AzPrivateEndpointConnection'.

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте больше о [частной ссылке Azure](private-link-overview.md)
 
