---
title: Создание экземпляра Load Balancer с избыточным между зонами интерфейсным сервером — Azure PowerShell
titlesuffix: Azure Load Balancer
description: Сведения о создании общедоступной подсистемы Load Balancer (цен. категория "Стандартный") с избыточным между зонами интерфейсным общедоступным IP-адресом с помощью PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 9cee3b68273fe98e70d558b5d01232bedf113224
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122139"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-powershell"></a>Создание экземпляра Load Balancer (цен. категория "Стандартный") с избыточным между зонами интерфейсным сервером с помощью Azure PowerShell

В этой статье описываются действия по созданию общедоступного [Load Balancer (цен. категория "Стандартный")](https://aka.ms/azureloadbalancerstandard) с избыточным между зонами интерфейсным сервером с помощью общедоступного стандартного IP-адреса.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

> [!NOTE]
>  Поддержка зон доступности реализована для некоторых ресурсов, регионов и семейств размеров виртуальных машин Azure. Дополнительные сведения о начале работы и о том, какие ресурсы, регионы и семейства размеров виртуальных машин Azure поддерживаются для зон доступности, см. в разделе [Обзор зон доступности](https://docs.microsoft.com/azure/availability-zones/az-overview). Чтобы получить поддержку, обратитесь на форум [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) или [создайте запрос в службу поддержки Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Connect-AzAccount` и следуйте инструкциям на экране.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-resource-group"></a>Создать группу ресурсов

Чтобы создать группу ресурсов, выполните следующую команду:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Создание общедоступного стандартного IP-адреса 
Создайте общедоступный стандартный IP-адрес с помощью следующей команды:

```azurepowershell-interactive
$publicIp = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Создание интерфейсной IP-конфигурации веб-сайта

Создайте внешнюю IP-конфигурацию с помощью следующей команды:

```azurepowershell-interactive
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
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
$lb = New-AzLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения см. в статье [Azure Load Balancer уровня "Стандартный" и зоны доступности](load-balancer-standard-availability-zones.md).