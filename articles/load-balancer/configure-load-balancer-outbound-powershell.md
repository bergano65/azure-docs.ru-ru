---
title: Налаживание балансировки и исходящих правил с помощью Azure PowerShell
titleSuffix: Azure Load Balancer
description: В этой статье показано, как настроить балансировку нагрузки и исходящие правила в Standard Load Balancer с помощью Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225441"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Налаживание балансировки нагрузки и исходящих правил в Standard Load Balancer с помощью Azure PowerShell

В этой статье показано, как настроить исходящие правила в Standard Load Balancer с помощью Azure PowerShell.  

Когда вы закончите сценарий этой статьи, ресурс балансоровы нагрузок содержит два передних конца и связанные с ними правила. У вас есть один передний конец для входящего трафика и другой передний конец для исходящего трафика.  

Каждый передний конец ссылается на общедоступный IP-адрес. В этом сценарии общедоступный IP-адрес для входящего трафика отличается от адреса исходящего трафика.   Правило балансировки нагрузки обеспечивает только балансировку входящих нагрузок. Исходящие правила контролируют перевод исходящих сетевых адресов (NAT) для VM.  

В сценарии используются два пула бэк-энда: один для входящего трафика и один для исходящего трафика. Эти пулы иллюстрируют возможности и обеспечивают гибкость сценария.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Подключение к учетной записи Azure
Войдите в подписку Azure с помощью команды [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0). Затем выполните инструкции на экране.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) Группа ресурсов Azure — это логический контейнер, в который развертываются ресурсы Azure. Ресурсы затем управляются из группы.

В следующем примере создается группа ресурсов с именем *myresourcegroupoutbound* в расположении *eastus2*.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Создание виртуальной сети
Создайте виртуальную сеть под названием *myvnetoutbound*. Назовите его подсеть *mysubnetoutbound*. Поместите его в *myresourcegroupoutbound* с помощью [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) и [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Создание входящего общедоступного IP-адреса 

Чтобы получить доступ к веб-приложению в Интернете, вам нужен общедоступный IP-адрес для балансиватель нагрузки. Standard Load Balancer поддерживает только стандартные общедоступные IP-адреса. 

Используйте [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) для создания стандартного общедоступного IP-адреса под названием *mypublicipinbound* в *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Создание исходящего общедоступного IP-адреса 

Создайте стандартный IP-адрес для исходящих конфигураций фронтового баланса нагрузки с помощью [New-AzPublicIpAddress.](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Создание Azure Load Balancer

В этом разделе объясняется, как создать и настроить следующие компоненты балансоилира нагрузки:
  - Фронтовой IP-адрес, который получает входящий сетевой трафик на балансере нагрузки
  - Пул бэк-энда, где ip-адрес фронтового IP отправляет сбалансированный с нагрузкой сетевой трафик
  - Пул бэк-энда для исходящих подключений
  - Зонд работоспособности, определяющий работоспособность бэк-эндовых экземпляров VM
  - Входящие правила грузобаланса, определяющие распределение трафика среди вс-чаевых
  - Правило исходящего груза и баланса, определяющее распределение трафика по вдовым

### <a name="create-an-inbound-front-end-ip"></a>Создание входящего переднего IP-адреса
Создайте входяшую конфигурацию IP-адреса для балансиваля нагрузки с помощью [New-AzLoadBalancerFrontEndIpConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) Балансомера нагрузки должна включать входящий передний конец IP-конфигурации под названием *myfrontendinbound.* Связать эту конфигурацию с публичным IP-адресом *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Создание исходящего фронт-энда IP
Создайте исходящие конфигурации ip-адреса для балансиваля нагрузки с помощью [New-AzLoadBalancerFrontendIpConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) Этот балансоровостом нагрузки должен включать исходящий передний конец IP-конфигурации под названием *myfrontendout.* Связать эту конфигурацию с общедоступным IP-адресом *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Создание входящего пула бэк-энда
Создайте резервный входящий пул для балансиваля нагрузки с помощью [New-AzLoadBalancerBackendAddressPoolConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) Назовите бассейн *bepoolinbound*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Создание исходящего пула бэк-энда
Используйте следующую команду для создания другого пула адресов бэк-энда для определения исходящих подключений для пула вс-м с помощью [New-AzLoadBalancerBackendAddressPoolConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) Назовите этот бассейн *bepooloutbound*. 

Создавая отдельный исходящий пул, вы обеспечиваете максимальную гибкость. Но вы можете опустить этот шаг и использовать только входящих *bepoolinbound,* если вы предпочитаете.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Создание пробы работоспособности

Зонд работоспособности проверяет все экземпляры VM, чтобы убедиться, что они могут отправлять сетевой трафик. Экземпляр VM, который не проверяет зонд, удаляется из балансоостатка нагрузки до тех пор, пока он не вернется в онлайн, и проверка зонда не определит, что он здоров. 

Для мониторинга работоспособности ВМ создайте зонд работоспособности с помощью [New-AzLoadBalancerProbeConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0) 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Создание правила грузобаланса

Правило грузобаланса определяет конфигурацию IP-адреса переднего конца для входящего трафика и пул бэк-энда для получения трафика. Он также определяет необходимый источник и порт назначения. 

Создайте правило грузобаланса под названием *myinboundlbrule* с помощью [New-AzLoadBalancerRuleConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0) Это правило будет слушать порт 80 в переднем конце бассейна *myfrontendinbound*. Он также будет использовать порт 80 для отправки сбалансированного сетевого трафика в пул адрес бэк-энда *bepoolinbound.* 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Это правило балансировки нагрузки отключает автоматический исходящий безопасный NAT (SNAT) из-за параметра **-DisableOutboundSNAT.** Исходящие NAT предоставляются только исходящим правилом.

### <a name="create-an-outbound-rule"></a>Создайте правило для исходящего трафика.

Исходящие правила определяет передний конец общественного IP, который представлен передним конце *myfrontendout.* Этот передний конец будет использоваться для всех исходящих трафика NAT, а также бэк-энд бассейн, к которому применяется правило.  

Используйте следующую команду для создания исходящего правила *myoutboundrule* для исходящего сетевого перевода всех VMs (в конфигурациях NIC IP) в *пуле* бэкпула.  Команда изменяет тайм-аут с момента простоя с 4 до 15 минут. Он выделяет 10 000 портов SNAT вместо 1024. Для получения дополнительной информации, [см.](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0)

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Если вы не хотите использовать отдельный пул исходящих, можно изменить аргумент пула адресов в предыдущей команде, чтобы указать *$bepoolin* вместо этого.  Мы рекомендуем использовать отдельные пулы, чтобы сделать полученную конфигурацию гибкой и читаемой.

### <a name="create-a-load-balancer"></a>Создание балансировщика нагрузки

Используйте следующую команду для создания балансо-баланса нагрузки для входящего IP-адреса с помощью [New-AzLoadBalancer.](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0) Назовите балансизатор нагрузки *lb*. Она должна включать входящий передний конец IP-конфигурации. Его бэк-энд бассейн *bepoolinbound* должны быть связаны с публичным IP-адресmy *mypublicipinbound,* что вы создали в предыдущем шаге.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

На этом этапе можно продолжать добавлять свои вм- носкаки как *в bepoolinbound,* так и *в пулеизм* бэк-энд пулы, обновляя конфигурацию IP соответствующих ресурсов NIC. Обновление конфигурации ресурса с помощью [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужна группа ресурсов, балансизатор нагрузки и связанные с ними ресурсы, вы можете удалить их с помощью [Remove-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0)

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Дальнейшие действия
В этой статье вы создали стандартный балансера нагрузки, настроили правила движения как входящих, так и исходящих правил движения грузоподъемности и настроили зонд работоспособности для VMs в пуле бэк-энда. 

Чтобы узнать больше, перейти к [учебникам для Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
