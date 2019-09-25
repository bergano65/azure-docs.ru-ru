---
title: Настройка правил балансировки нагрузки и исходящего трафика с помощью Azure PowerShell
titlesuffix: Azure Load Balancer
description: В этой статье показано, как настроить балансировку нагрузки и правила исходящего трафика в Load Balancer (цен. категория "Стандартный") с помощью Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 6e85d31e40e35b9d796fc66394a6eb446c7302ad
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262626"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-powershell"></a>Настройка правил балансировки нагрузки и исходящего трафика в стандартном подсистеме балансировки нагрузки с помощью Azure PowerShell

В этой статье показано, как настроить правила исходящего трафика в стандартном подсистеме балансировки нагрузки с помощью Azure PowerShell.  

После этого ресурс балансировщика нагрузки будет содержать два внешних интерфейса и правила, связанные с ними: один для входящего, а другой для исходящего трафика.  Каждый внешний интерфейс содержит ссылку на общедоступный IP-адрес. В этом сценарии для входящего и исходящего трафика используются разные общедоступные IP-адреса.   Правило балансировки нагрузки обеспечивает балансировку нагрузки только входящего трафика, а правило для исходящего трафика управляет исходящим трафиком NAT, предоставленным для виртуальной машины.  В этой статье используются два отдельных серверных пула: один для входящего и один для исходящего трафика, чтобы продемонстрировать возможности и обеспечить гибкость в этом сценарии.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure-account"></a>Подключение к учетной записи Azure
Войдите в подписку Azure с помощью команды [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) и следуйте инструкциям на экране.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов с помощью командлета [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем *myresourcegroupoutbound* в расположении *eastus2*.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-virtual-network"></a>Создать виртуальную сеть
Создайте виртуальную сеть с именем *мивнетаутбаунд* с подсетью с именем *мисубнетаутбаунд* в *Миресаурцеграупаутбаунд* с помощью [New-азвиртуалнетворк](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) и [New-азвиртуалнетворксубнетконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0):

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-inbound-public-ip-address"></a>Создать входящий общедоступный IP-адрес 

Для доступа к веб-приложению через Интернет подсистеме балансировки нагрузки требуется общедоступный IP-адрес. Стандартный балансировщик нагрузки поддерживает только стандартные общедоступные IP-адреса. Используйте [New-азпублиЦипаддресс](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) , чтобы создать стандартный общедоступный IP-адрес с именем *мипублиЦипинбаунд* в *миресаурцеграупаутбаунд*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-outbound-public-ip-address"></a>Создание исходящего общедоступного IP-адреса 

Создайте стандартный IP-адрес для многоинтерфейсной конфигурации балансировщика нагрузки с помощью команды [New-азпублиЦипаддресс](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-azure-load-balancer"></a>Создание Azure Load Balancer

В этом разделе описано, как создать и настроить следующие компоненты подсистемы балансировки нагрузки:
  - интерфейсный IP-адрес, который получает входящий трафик в подсистеме балансировки нагрузки;
  - Внутренний пул, в котором интерфейсный IP-адрес отправляет сетевой трафик с балансировкой нагрузки.
  - Внутренний пул для исходящих подключений. 
  - проверка работоспособности, определяющая работоспособность внутренних экземпляров виртуальной машины;
  - правило подсистемы балансировки нагрузки для входящего трафика, определяющее порядок распределения трафика между виртуальными машинами;
  - правило подсистемы балансировки нагрузки для исходящего трафика, определяющее порядок распределения трафика из виртуальных машин.

### <a name="create-inbound-frontend-ip"></a>Создание входящего внешнего IP-адреса
Создайте конфигурацию исходящего внешнего IP-адреса для подсистемы балансировки нагрузки с помощью [New-азлоадбаланцерфронтендипконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) , которая включает в себя конфигурацию входящей внешней IP-конфигурации с именем *мифронтендинбаунд* , связанную с общедоступным IP-адресом *. мипублиЦипинбаунд*

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-outbound-frontend-ip"></a>Создание IP-адреса исходящего внешнего интерфейса
Создайте конфигурацию исходящего внешнего IP-адреса для балансировщика нагрузки с помощью [New-азлоадбаланцерфронтендипконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) , которая включает в себя конфигурацию исходящей внешней IP-конфигурации с именем *мифронтендаутбаунд* , связанную с общедоступным IP-адресом *. мипублиЦипаутбаунд*:

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-inbound-backend-pool"></a>Создание входящего внутреннего пула
Создайте внутренний входящий пул для балансировщика нагрузки с помощью [New-азлоадбаланцербаккендаддресспулконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) с именем *бепулинбаунд*:

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-outbound-backend-pool"></a>Создание исходящего серверного пула
Создайте дополнительный внутренний пул адресов, чтобы определить исходящее подключение для пула виртуальных машин с помощью [New-азлоадбаланцербаккендаддресспулконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) с именем *бепулаутбаунд*. Создание отдельного исходящего пула обеспечивает максимальную гибкость, но вы можете опустить этот шаг и использовать только входящие *бепулинбаунд* .  :

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-health-probe"></a>Создание пробы работоспособности

Проба работоспособности проверяет все экземпляры виртуальной машины, чтобы убедиться, что они могут отправлять сетевой трафик. Экземпляр виртуальной машины с неудачной пробой удаляется из балансировщика нагрузки, пока не перейдет в оперативный режим и проба не определит его работоспособность. Создайте пробу работоспособности с помощью [New-азлоадбаланцерпробеконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0) , чтобы отслеживать работоспособность виртуальных машин. 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-load-balancing-rule"></a>Создание правила балансировки нагрузки

Правило подсистемы балансировки нагрузки определяет интерфейсную конфигурацию IP-адресов для входящего трафика и внутренний пул для приема трафика, а также порты источника и назначения. Создайте правило балансировщика нагрузки *минбаундлбруле* с помощью [New-азлоадбаланцеррулеконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0) для прослушивания порта 80 в интерфейсном пуле *мифронтендинбаунд* и отправки сетевого трафика с балансировкой нагрузки в серверный пул *адресов. бепулинбаунд* также использует порт 80. 

>[!NOTE]
>Это правило балансировки нагрузки отключает автоматическую исходящий трафик (S) NAT в результате этого правила с параметром **-disableoutboundsnat.** . NAT для исходящего трафика предоставляется только с помощью правила для исходящего трафика.

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

### <a name="create-outbound-rule"></a>Создание правила для исходящего трафика

Правило для исходящего трафика определяет общедоступный IP-адрес внешнего интерфейса, представленный внешним интерфейсом *myfrontendoutbound*, который будет использоваться для всего исходящего трафика NAT, а также внутреннего пула, к которому применяется это правило.  Создайте правило для исходящего трафика *myoutboundrule* для сетевого преобразования исходящего трафика всех виртуальных машин (IP-конфигурации NIC) в серверном пуле *bepool*.  Приведенная ниже команда также изменяет время ожидания исходящего трафика в режиме простоя от 4 до 15 минут и выделяет 10 000 портов SNAT вместо 1024.  Дополнительные сведения см. в описании [New-азлоадбаланцераутбаундрулеконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0) .

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Если вы не хотите использовать отдельный исходящий пул, можно изменить аргумент пула адресов в предыдущей команде, указав вместо этого *$bepoolin* .  Мы рекомендуем использовать отдельные пулы для обеспечения гибкости и удобочитаемости результирующей конфигурации.

### <a name="create-load-balancer"></a>Создание подсистемы балансировки нагрузки

Создайте подсистему балансировки нагрузки с входящим IP-адресом с помощью команды [New-азлоадбаланцер](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0) *с именем Balance* , которая включает в себя внешнюю IP-конфигурацию входящего трафика и серверный пул *бепулинбаунд* , связанный с общедоступным IP-адресом *. мипублиЦипинбаунд* , созданный на предыдущем шаге.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

На этом этапе можно продолжить добавление виртуальных машин в серверный пул *бепулинбаунд* __и__ *бепулаутбаунд* , обновив IP-конфигурацию соответствующих сетевых ресурсов с помощью [Add-азнетворкинтерфацеипконфиг](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если она больше не нужна, можно удалить группу ресурсов, подсистему балансировки нагрузки и все связанные с ней ресурсы с помощью команды [Remove-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0) .

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Следующие шаги
В этой статье вы создали стандартную подсистему балансировки нагрузки, настроенную как правила трафика для входящего балансировщика нагрузки, настроенные и зонды работоспособности для виртуальных машин в серверном пуле. Чтобы узнать больше о Azure Load Balancer, перейдите к руководствам по подсистеме балансировки нагрузки Azure.

> [!div class="nextstepaction"]
> [Руководства по Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
