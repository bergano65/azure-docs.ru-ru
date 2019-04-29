---
title: Общие команды PowerShell для виртуальных сетей Azure | Документация Майкрософт
description: Общие команды PowerShell, позволяющие приступить к созданию виртуальной сети и связанных с ней ресурсов для виртуальных машин.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 020f2a4171a5bd656e53c91e59edb16931b20d0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597676"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Общие команды PowerShell для виртуальных сетей Azure

Если вы хотите создать виртуальную машину, необходимо создать [виртуальную сеть](../../virtual-network/virtual-networks-overview.md) или иметь данные существующей виртуальной сети, в которую можно добавить виртуальную машину. Обычно при создании виртуальной машины требуется также рассмотреть возможность создания ресурсов, приведенных в этой статье.

Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в учетную запись Azure см. в статье [Как установить и настроить службы Azure PowerShell](/powershell/azure/overview).

При выполнении нескольких команд, описываемых в этой статье, могут пригодиться некоторые переменные.

- $location — расположение сетевых ресурсов. Вы можете использовать командлет [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation), чтобы найти используемый [географический регион](https://azure.microsoft.com/regions/).
- $myResourceGroup — имя группы ресурсов, в которой находятся сетевые ресурсы.

## <a name="create-network-resources"></a>Создание сетевых ресурсов

| Задача | Команда |
| ---- | ------- |
| Создание конфигураций подсети |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>В обычной сети может быть подсеть для [балансировщика нагрузки с выходом в Интернет](../../load-balancer/load-balancer-internet-overview.md) и отдельная подсеть для [внутреннего балансировщика нагрузки](../../load-balancer/load-balancer-internal-overview.md). |
| Создать виртуальную сеть |$vnet = [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Проверка доменного имени на уникальность |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Вы можете указать DNS-имя домена для [ресурса с общедоступным IP-адресом](../../virtual-network/virtual-network-ip-addresses-overview-arm.md). В результате этого будут сопоставлены адрес доменное_имя.расположение.cloudapp.azure.com и общедоступный IP-адрес на DNS-серверах под управлением Azure. Имя может содержать только буквы, цифры и дефисы. Первым и последним знаком должна быть буква или цифра, а доменное имя должно быть уникальным в пределах его расположения Azure. Если возвращается значение **True**, то предложенное имя является глобально уникальным. |
| Создание общедоступного IP-адреса |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>В общедоступном IP-адресе используется доменное имя, которое вы ранее проверили и которое используется интерфейсной конфигурацией балансировщика нагрузки. |
| Создание интерфейсной IP-конфигурации |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>Конфигурация внешнего интерфейса содержит общедоступный IP-адрес, созданный ранее для входящего сетевого трафика. |
| Создание внутреннего пула адресов |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Предоставляет внутренние адреса для серверной части балансировщика нагрузки, доступной через сетевой интерфейс. |
| Создание пробы |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Содержит пробы работоспособности, используемые для проверки доступности экземпляров виртуальных машин в пуле внутренних адресов. |
| Создание правила балансировки нагрузки |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Содержит правила, которые назначают общедоступный порт в балансировщике нагрузки порту во внутреннем пуле адресов. |
| Создание правила NAT для входящего трафика |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Содержит правила сопоставления общедоступного порта в балансировщике нагрузки с портом конкретной виртуальной машины в пуле внутренних адресов. |
| Создание балансировщика нагрузки |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Создание сетевого интерфейса |$nic1= [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX.X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Создает сетевой интерфейс с использованием общедоступного IP-адреса и подсети виртуальной сети, созданной ранее. |

## <a name="get-information-about-network-resources"></a>Получение сведений о сетевых ресурсах

| Задача | Команда |
| ---- | ------- |
| Получение списка виртуальных сетей |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Выводит список всех виртуальных сетей в группе ресурсов. |
| Получение сведений о виртуальной сети |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Получение списка подсетей в виртуальной сети |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup | Выбор подсетей |
| Получение сведений о подсети |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Возвращает сведения о подсети в указанной виртуальной сети. Значение $vnet представляет собой объект, возвращаемый командлетом Get-AzVirtualNetwork. |
| Получение списка IP-адресов |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Выводит список общедоступных IP-адресов в группе ресурсов. |
| Получение списка балансировщиков нагрузки |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Выводит список всех балансировщиков нагрузки в группе ресурсов. |
| Получение списка сетевых интерфейсов |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Выводит список всех виртуальных интерфейсов в группе ресурсов. |
| Получение сведений о виртуальном интерфейсе |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Получение сведений об определенном сетевом интерфейсе. |
| Получение IP-конфигурации сетевого интерфейса |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>Возвращает сведения об IP-конфигурации указанного сетевого интерфейса. Значение $nic представляет собой объект, возвращаемый командлетом Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Управление сетевыми ресурсами

| Задача | Команда |
| ---- | ------- |
| Добавление подсети в виртуальную сеть |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Добавляет подсеть в существующую виртуальную сеть. Значение $vnet представляет собой объект, возвращаемый командлетом Get-AzVirtualNetwork. |
| Удаление виртуальной сети |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Удаляет указанную виртуальную сеть из группы ресурсов. |
| Удаление сетевого интерфейса |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Удаляет указанный сетевой интерфейс из группы ресурсов. |
| Удаление балансировщика нагрузки |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Удаляет указанный балансировщик нагрузки из группы ресурсов. |
| Удаление общедоступного IP-адреса |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Удаляет указанный общедоступный IP-адрес из группы ресурсов. |

## <a name="next-steps"></a>Следующие шаги
* Использование созданного сетевого интерфейса при [создании виртуальной машины](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Узнайте, как можно [создать виртуальную машину с несколькими сетевыми интерфейсами](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

