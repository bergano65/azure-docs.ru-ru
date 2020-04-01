---
title: Обновление приложения IPv4 до IPv6 в виртуальной сети Azure - PowerShell
titlesuffix: Azure Virtual Network
description: В этой статье показано, как развертывать адреса IPv6 в существующее приложение в виртуальной сети Azure с помощью Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: c733538a4e730a95008a8ec1e4d50c20d6ce24ec
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420764"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>Обновление приложения IPv4 до IPv6 в виртуальной сети Azure - PowerShell

В этой статье показано, как добавить подключение IPv6 к существующему приложению IPv4 в виртуальной сети Azure со балансом стандартной нагрузки и общественным IP. Обновление на месте включает в себя:
- Адресное пространство IPv6 для виртуальной сети и подсети
- Балансом стандартной нагрузки с конфигурациями передней панели IPv4 и IPV6
- ВМ с NICs, которые имеют конфигурацию IPv4 и IPv6
- IPv6 Public IP, чтобы балансомер нагрузки имеет подключение IPv6 к Интернету



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решите установить и использовать PowerShell локально, эта статья требует версии модуля Azure PowerShell 6.9.0 или позже. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что вы развернули балансовик стандартной нагрузки, как описано в [квикстарте: Создайте баланс- стандартный баланс - Azure PowerShell.](../load-balancer/quickstart-create-standard-load-balancer-powershell.md)

## <a name="retrieve-the-resource-group"></a>Извлечение группы ресурсов

Прежде чем создать виртуальную сеть с двойным стеком, необходимо получить группу ресурсов с [помощью Get-AzResourceGroup.](/powershell/module/az.resources/get-azresourcegroup)

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Создание IP-адресов IPv6

Создайте общедоступный адрес IPv6 с [помощью New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) для балансировора стандартной нагрузки. Следующий пример создает ip-адрес IPv6 *с* PublicIP_v6 в группе ресурсов *myResourceGroupSLB:*

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Настройка фронтера балансовик иссопереена нагрузки

Извлеките существующую конфигурацию балансоровы нагрузки, а затем добавьте новый IP-адрес IPv6 с помощью [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) следующим образом:

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

## <a name="configure-load-balancer-backend-pool"></a>Настройка пула бэкэнда балансера нагрузки

Создайте пул бэкэнда на локальной копии конфигурации балансоровы нагрузок и обновите балансирумы рабочей нагрузки с новой конфигурацией пула бэкэнда следующим образом:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Настройка правил подсистемы балансировки нагрузки.
Извлеките существующую конфигурацию баланса нагрузки и бэкэндпула, а затем добавьте новые правила балансировки нагрузки с помощью [Add-AzLoadBalancerRuleConfig.](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig)

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
## <a name="add-ipv6-address-ranges"></a>Добавление диапазонов адресов IPv6

Добавьте диапазоны адресов IPv6 в виртуальную сеть и подсеть, где размещаются виртуальные виртуальные технологии следующим образом:

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
## <a name="add-ipv6-configuration-to-nic"></a>Добавление конфигурации IPv6 в NIC

Настройте все VM NICs с адресом IPv6 с помощью [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) следующим образом:

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

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Просмотр виртуальной сети IPv6 с двойным стеком на портале Azure
Виртуальную сеть IPv6 с двойным стеком можно просмотреть следующим образом:
1. В панели поиска портала введите *myVnet*.
2. Когда **myVnet** появится в результатах поиска, выберите его. Это запускает **Обзор** страницы двойного стека виртуальной сети под названием *myVNet*. Виртуальная сеть двойного стека показывает три NICs с конфигурациями IPv4 и IPv6, расположенными в подсети с двойным стеком под названием *mySubnet.*

  ![Виртуальная сеть IPv6 с двойным стеком в Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Следующие шаги

В этой статье вы обновили существующий балансироворовую стандартную нагрузку с конфигурацией IP-адреса IPv4 до двойной стек (IPv4 и IPv6). Вы также добавили конфигурации IPv6 в NICs виртуальных технологий в пул бэкэнда и в Виртуальную сеть, которая их размещает. Подробнее о поддержке IPv6 в виртуальных сетях Azure читайте в пример [IPv6 для виртуальной сети Azure?](ipv6-overview.md)
