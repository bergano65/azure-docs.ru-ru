---
title: Создание подсистемы балансировки нагрузки с зональным внешним интерфейсом с помощью Azure PowerShell
titleSuffix: Azure Load Balancer
description: Узнайте, как создать экземпляр Load Balancer (цен. категория "Стандартный") с зональным внешним интерфейсом с помощью Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 1d05b130a98ce816d070bc3ad16e25b867d7dc8a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215122"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-powershell"></a>Создание экземпляра Load Balancer (цен. категория "Стандартный") с зональным интерфейсным сервером с помощью Azure PowerShell

В этой статье приведены действия по созданию общедоступного экземпляра [Load Balancer (цен. категория "Стандартный")](https://aka.ms/azureloadbalancerstandard) с зональным внешним интерфейсом и с использованием общедоступного стандартного IP-адреса. Чтобы понять, как работают зоны доступности с Load Balancer уровня "Стандартный", ознакомьтесь со статьей [Azure Load Balancer уровня "Стандартный" и зоны доступности](load-balancer-standard-availability-zones.md). 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

> [!NOTE]
> Поддержка зон доступности реализована для некоторых ресурсов, регионов и семейств размеров виртуальных машин Azure. Дополнительные сведения о начале работы и о том, какие ресурсы, регионы и семейства размеров виртуальных машин Azure поддерживаются для зон доступности, см. в разделе [Обзор зон доступности](https://docs.microsoft.com/azure/availability-zones/az-overview). Чтобы получить поддержку, обратитесь на форум [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) или [создайте запрос в службу поддержки Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Connect-AzAccount` и следуйте инструкциям на экране.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-resource-group"></a>Создать группу ресурсов

Чтобы создать группу ресурсов, выполните следующую команду:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupZLB -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Создание общедоступного стандартного IP-адреса 
Создайте общедоступный стандартный IP-адрес с помощью следующей команды:

```azurepowershell-interactive
$publicIp = New-AzPublicIpAddress -ResourceGroupName myResourceGroupZLB -Name 'myPublicIPZonal' `
  -Location westeurope -AllocationMethod Static -Sku Standard -zone 1
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Создание интерфейсной IP-конфигурации веб-сайта

Создайте внешнюю IP-конфигурацию с помощью следующей команды:

```azurepowershell-interactive
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>Создание пула внутренних адресов

Создайте пул внутренних адресов с помощью следующей команды:

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Создание пробы работоспособности подсистемы балансировки нагрузки в порте 80

Создайте пробу работоспособности подсистемы балансировки нагрузки в порте 80 с помощью следующей команды:

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>Создание правила балансировщика нагрузки
 Создайте правило балансировщика нагрузки с помощью следующей команды:

```azurepowershell-interactive
   $rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>Создание балансировщика нагрузки
Создайте экземпляр Load Balancer (цен. категория "Стандартный") с помощью следующей команды:

```azurepowershell-interactive
$lb = New-AzLoadBalancer -ResourceGroupName myResourceGroupZLB -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения см. в статье [Azure Load Balancer уровня "Стандартный" и зоны доступности](load-balancer-standard-availability-zones.md).