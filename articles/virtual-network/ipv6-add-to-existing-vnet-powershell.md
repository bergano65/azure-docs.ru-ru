---
title: Обновление приложения IPv4 до IPv6 в виртуальной сети Azure с помощью PowerShell
titlesuffix: Azure Virtual Network
description: В этой статье показано, как развернуть IPv6-адреса в существующем приложении в виртуальной сети Azure с помощью Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/21/2019
ms.author: kumud
ms.openlocfilehash: 47f73ca8ece8db5fad3f8a7709d8787db42626f4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791199"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell-preview"></a>Обновление приложения IPv4 до IPv6 в виртуальной сети Azure с помощью PowerShell (Предварительная версия)

В этой статье показано, как добавить IPv6-адреса в приложение, которое использует общедоступный IP-адрес IPv4 в виртуальной сети Azure для Load Balancer (цен. категория "Стандартный"). Обновление на месте включает виртуальную сеть и подсеть, Load Balancer (цен. категория "Стандартный") с многоадресными конфигурациями IPv4 и IPV6, виртуальные машины с сетевыми картами, которые имеют конфигурации IPv4 + IPv6, группу безопасности сети и общедоступные IP-адреса.

> [!Important]
> Поддержка IPv6 для виртуальной сети Azure в настоящее время доступна в общедоступной предварительной версии. Предварительная версия предоставляется без соглашения об уровне обслуживания. Не рекомендуем использовать ее в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать PowerShell локально, для работы с этой статьей требуется модуль Azure PowerShell версии 6.9.0 или более поздней. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="prerequisites"></a>Технические условия

### <a name="register-the-service"></a>Регистрация службы

Перед развертыванием приложения с двойным стеком в Azure необходимо настроить подписку для этой предварительной версии, используя следующие Azure PowerShell:

Зарегистрируйтесь следующим образом:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Регистрация функции занимает до 30 минут. Вы можете проверить состояние регистрации, выполнив следующую команду Azure PowerShell: Проверьте регистрацию следующим образом:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
После регистрации выполните следующую команду:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>создание подсистемы балансировки нагрузки уровня "Стандартный";
В этой статье предполагается, что вы развернули Load Balancer (цен. категория "Стандартный"), как описано в разделе [Краткое руководство по созданию Load Balancer (цен. категория "Стандартный") Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md).

## <a name="retrieve-the-resource-group"></a>Получение группы ресурсов

Прежде чем создавать виртуальную сеть с двумя стеками, необходимо получить группу ресурсов с помощью [Get-азресаурцеграуп](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Создание IP-адресов IPv6

Создайте общедоступный адрес IPv6 с помощью [New-азпублиЦипаддресс](/powershell/module/az.network/new-azpublicipaddress) для Load Balancer (цен. Категория "Стандартный"). В следующем примере создается общедоступный IP-адрес IPv6 с именем *PublicIP_v6* в группе ресурсов *myResourceGroupSLB* :

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Настройка внешнего интерфейса балансировщика нагрузки

Извлеките существующую конфигурацию подсистемы балансировки нагрузки и настройте ее с помощью нового IP-адреса IPv6 с помощью [Add-азлоадбаланцерфронтендипконфиг](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) следующим образом:

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Настройка внутреннего пула подсистемы балансировки нагрузки

Создайте внутренний пул в локальной копии конфигурации подсистемы балансировки нагрузки и обновите работающую подсистему балансировки нагрузки с помощью новой конфигурации серверного пула следующим образом:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Настройка правил подсистемы балансировки нагрузки
Извлеките существующую конфигурацию внешнего интерфейса и внутреннего пула подсистемы балансировки нагрузки, а затем добавьте новые правила балансировки нагрузки с помощью [Add-азлоадбаланцеррулеконфиг](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig).

```azurepowershell
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb
# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>Добавить диапазоны IPv6-адресов

Добавьте диапазоны IPv6-адресов в виртуальную сеть и подсеть, в которых размещен балансировщик нагрузки, следующим образом:

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>Добавление конфигурации IPv6 к сетевой карте

Настройте оба сетевых адаптера виртуальной машины с адресом IPv6 с помощью [Add-азнетворкинтерфацеипконфиг](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) следующим образом:

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Просмотр двух виртуальных сетей IPv6 с двумя стеками в портал Azure
Виртуальную сеть с двумя стеками IPv6 можно просмотреть в портал Azure следующим образом:
1. На панели поиска портала введите *myVnet*.
2. Когда в результатах поиска появится **myVnet** , выберите его. Откроется страница **обзора** для виртуальной сети с двумя стеками с именем *myVNet*. В виртуальной сети с двумя стеками показаны три сетевых адаптера с конфигурациями IPv4 и IPv6, расположенными в подсети с двойным стеком с именем *mySubnet*.

  ![Виртуальная сеть с двумя стеками IPv6 в Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> Виртуальная сеть IPv6 для виртуальной сети Azure доступна в портал Azure в режиме только для чтения для этой предварительной версии.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы обновили существующую Load Balancer (цен. категория "Стандартный") с многоинтерфейсной IP-конфигурацией IPv4 для конфигурации с двумя стеками (IPv4 и IPv6). Вы также добавили конфигурации IPv6 в сетевые адаптеры виртуальных машин в серверном пуле. Дополнительные сведения о поддержке IPv6 в виртуальных сетях Azure см. в статье [что такое IPv6 для виртуальной сети Azure?](ipv6-overview.md)
