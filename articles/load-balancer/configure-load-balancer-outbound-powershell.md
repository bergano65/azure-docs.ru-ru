---
title: Настройка балансировки нагрузки и правил исходящего трафика с помощью Azure PowerShell
titlesuffix: Azure Load Balancer
description: В этой статье показано, как настроить балансировку нагрузки и правила исходящего трафика в Load Balancer (цен. категория "Стандартный") с помощью Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b621d56a76a51f9ce9df10f88f5c3f2f4babfc03
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71816024"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Настройка балансировки нагрузки и правил исходящего трафика в Load Balancer (цен. категория "Стандартный") с помощью Azure PowerShell

В этой статье показано, как настроить правила исходящего трафика в Load Balancer (цен. категория "Стандартный") с помощью Azure PowerShell.  

По завершении работы с этой статьей в ресурсе балансировщика нагрузки будут содержаться два внешних интерфейса и связанные с ними правила. У вас есть один внешний интерфейс для входящего трафика и другой внешний интерфейс для исходящего трафика.  

Каждый внешний интерфейс ссылается на общедоступный IP-адрес. В этом сценарии общедоступный IP-адрес для входящего трафика отличается от адреса для исходящего трафика.   Правило балансировки нагрузки обеспечивает только входящую балансировку нагрузки. Правило исходящего трафика управляет преобразованием исходящего сетевого адреса (NAT) для виртуальной машины.  

В сценарии используются два внутренних пула: один для входящего трафика и один для исходящего трафика. Эти пулы иллюстрируют возможности и обеспечивают гибкость для сценария.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Подключение к учетной записи Azure
Войдите в подписку Azure с помощью команды [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0). Затем выполните инструкции на экране.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов с помощью команды [New-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Группа ресурсов Azure — это логический контейнер, в котором развертываются ресурсы Azure. Затем ресурсы управляются из группы.

В следующем примере создается группа ресурсов с именем *myresourcegroupoutbound* в расположении *eastus2*.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Создайте виртуальную сеть с именем *мивнетаутбаунд*. Присвойте имя подсети *мисубнетаутбаунд*. Поместите его в *миресаурцеграупаутбаунд* с помощью [New-азвиртуалнетворк](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) и [New-азвиртуалнетворксубнетконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Создание входящего общедоступного IP-адреса 

Для доступа к веб-приложению в Интернете необходим общедоступный IP-адрес для балансировщика нагрузки. Load Balancer (цен. категория "Стандартный") поддерживает только стандартные общедоступные IP-адреса. 

Используйте [New-азпублиЦипаддресс](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) , чтобы создать стандартный общедоступный IP-адрес с именем *мипублиЦипинбаунд* в *миресаурцеграупаутбаунд*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Создание исходящего общедоступного IP-адреса 

Создайте стандартный IP-адрес для конфигурации исходящего трафика балансировщика нагрузки с помощью команды [New-азпублиЦипаддресс](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Создание Azure Load Balancer

В этом разделе объясняется, как создать и настроить следующие компоненты балансировщика нагрузки:
  - Интерфейсный IP-адрес, который получает входящий сетевой трафик в подсистеме балансировки нагрузки
  - Серверный пул, в котором интерфейсный IP-адрес отправляет сетевой трафик с балансировкой нагрузки.
  - Пул серверной части для исходящего подключения
  - Зонд работоспособности, определяющий работоспособность экземпляров серверной виртуальной машины
  - Правило входящего трафика балансировщика нагрузки, определяющее способ передачи трафика на виртуальные машины
  - Правило исходящего трафика балансировщика нагрузки, которое определяет, как трафик распределяется из виртуальных машин.

### <a name="create-an-inbound-front-end-ip"></a>Создание входящего внешнего IP-адреса
Создайте конфигурацию входящего внешнего IP-адреса для подсистемы балансировки нагрузки с помощью команды [New-азлоадбаланцерфронтендипконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Подсистема балансировки нагрузки должна включать в себя входящую конфигурацию IP-адресов для входящего внешнего интерфейса с именем *мифронтендинбаунд*. Свяжите эту конфигурацию с общедоступным IP-адресом *мипублиЦипинбаунд*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Создание исходящего IP-адреса внешнего интерфейса
Создайте конфигурацию исходящего внешнего IP-адреса для балансировщика нагрузки с помощью команды [New-азлоадбаланцерфронтендипконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Эта подсистема балансировки нагрузки должна включать в себя исходящую IP-конфигурацию внешнего интерфейса с именем *мифронтендаутбаунд*. Свяжите эту конфигурацию с общедоступным IP-адресом *мипублиЦипаутбаунд*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Создание входящего пула серверной части
Создайте сервер входящего пула для подсистемы балансировки нагрузки с помощью команды [New-азлоадбаланцербаккендаддресспулконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Назовите пул *бепулинбаунд*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Создание исходящего пула серверной части
Используйте следующую команду, чтобы создать другой пул внутренних адресов для определения исходящих подключений для пула виртуальных машин с помощью [New-азлоадбаланцербаккендаддресспулконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Назовите этот пул *бепулаутбаунд*. 

Создавая отдельный исходящий пул, вы предоставляете максимальную гибкость. Но вы можете опустить этот шаг и использовать только входящий *бепулинбаунд* , если предпочитаете.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Создание пробы работоспособности

Зонд работоспособности проверяет все экземпляры виртуальной машины, чтобы убедиться, что они могут передавать сетевой трафик. Экземпляр виртуальной машины, который не проходит проверки зонда, удаляется из подсистемы балансировки нагрузки, пока не вернется в режим «в сети», а проверка проверки определит, что она работоспособна. 

Чтобы отслеживать работоспособность виртуальных машин, создайте пробу работоспособности с помощью команды [New-азлоадбаланцерпробеконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Создание правила подсистемы балансировки нагрузки

Правило подсистемы балансировки нагрузки определяет конфигурацию внешнего IP-адреса для входящего трафика и пула внутренних серверов для получения трафика. Он также определяет требуемый исходный и конечный порты. 

Создайте правило подсистемы балансировки нагрузки с именем *минбаундлбруле* с помощью [New-азлоадбаланцеррулеконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Это правило будет прослушивать порт 80 в интерфейсном пуле *мифронтендинбаунд*. Он также будет использовать порт 80 для отправки сетевого трафика с балансировкой нагрузки на серверный пул адресов *бепулинбаунд*. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Это правило балансировки нагрузки отключает автоматическую исходящую исходящий трафик NAT (SNAT), так как параметр **-disableoutboundsnat.** . Исходящий трафик NAT предоставляется только правилом исходящего трафика.

### <a name="create-an-outbound-rule"></a>Создайте правило для исходящего трафика.

Правило для исходящего трафика определяет интерфейсный общедоступный IP-адрес, который представляется внешним интерфейсом *мифронтендаутбаунд*. Этот внешний интерфейс будет использоваться для всего исходящего трафика NAT, а также для пула внутренних серверных ресурсов, к которому применяется это правило.  

Используйте следующую команду, чтобы создать правило исходящего трафика *мйоутбаундруле* для исходящего преобразования сети всех виртуальных машин (в IP-конфигурациях NIC) в пуле серверной части *пула* .  Команда изменяет время исходящего простоя с 4 до 15 минут. Он выделяет порты 10 000 SNAT вместо 1 024. Дополнительные сведения см. в разделе [New-азлоадбаланцераутбаундрулеконфиг](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Если вы не хотите использовать отдельный исходящий пул, можно изменить аргумент пула адресов в предыдущей команде, указав вместо этого *$bepoolin* .  Мы рекомендуем использовать отдельные пулы, чтобы сделать конечную конфигурацию гибкой и удобочитаемой.

### <a name="create-a-load-balancer"></a>Создание балансировщика нагрузки

Используйте следующую команду, чтобы создать подсистему балансировки нагрузки для входящего IP-адреса с помощью [New-азлоадбаланцер](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0). Назовите подсистему *балансировки нагрузки.* Он должен включать в себя конфигурацию входящего IP-адреса внешнего интерфейса. Его серверный пул *бепулинбаунд* должен быть связан с общедоступным IP-адресом *мипублиЦипинбаунд* , созданным на предыдущем шаге.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

На этом этапе можно продолжить добавление виртуальных машин в пулы внутренних *бепулинбаунд* и *бепулаутбаунд* , обновив IP-конфигурацию соответствующих ресурсов сетевых карт. Обновите конфигурацию ресурсов с помощью команды [Add-азнетворкинтерфацеипконфиг](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если группа ресурсов, подсистема балансировки нагрузки и связанные ресурсы больше не требуются, их можно удалить с помощью [Remove-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Следующие шаги
В этой статье вы создали стандартную подсистему балансировки нагрузки, настроенную для входящих и исходящих правил трафика балансировщика нагрузки, и настроили проверку работоспособности для виртуальных машин в пуле серверной части. 

Чтобы узнать больше, перейдите к [руководствам для Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
