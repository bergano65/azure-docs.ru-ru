---
title: Обновление или удаление существующих Azure Load Balancer, используемых в масштабируемом наборе виртуальных машин
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
description: В этом пошаговом руководстве приступите к работе с Azure Load Balancer (цен. категория "Стандартный") и масштабируемыми наборами виртуальных машин.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: f8f664375e53a1cef28b0c7b95207770434f67fa
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97893284"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>Обновление и удаление Azure Load Balancer, используемых масштабируемыми наборами виртуальных машин

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Как настроить Azure Load Balancer для масштабирования масштабируемых наборов виртуальных машин
  * Убедитесь, что в Load Balancer настроен [входящий пул NAT](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) и что масштабируемый набор виртуальных машин размещен в серверном пуле Load Balancer. Azure Load Balancer автоматически создаст новые правила NAT для входящего трафика в пуле входящих подключений NAT при добавлении новых экземпляров виртуальных машин в масштабируемый набор виртуальных машин. 
  * Чтобы проверить, правильно ли настроен входящий пул NAT, 
  1. Войдите на портал Azure по адресу https://portal.azure.com.
  
  1. Щелкните **Все ресурсы** в меню слева, а затем из списка ресурсов выберите **MyLoadBalancer**.
  
  1. В разделе **Параметры** выберите **правила NAT для входящего трафика**.
Если на панели справа отображается список правил, созданных для каждого отдельного экземпляра в масштабируемом наборе виртуальных машин, то все, что вы все задаете для увеличения масштаба в любое время.

## <a name="how-to-add-inbound-nat-rules"></a>Как добавить правила NAT для входящего трафика? 
  * Невозможно добавить отдельное правило NAT для входящего трафика. Однако можно добавить набор правил NAT для входящего трафика с определенным диапазоном внешних портов и внутренним портом для всех экземпляров в масштабируемом наборе виртуальных машин.
  * Чтобы добавить весь набор правил NAT для входящего трафика для масштабируемых наборов виртуальных машин, необходимо сначала создать входящий в Load Balancer пул NAT, а затем указать ссылку на пул NAT для входящего трафика из сетевого профиля масштабируемого набора виртуальных машин. Полный пример использования CLI показан ниже.
  * Новый пул NAT для входящего трафика не должен перекрывать диапазон интерфейсных портов с помощью существующих пулов NAT для входящего трафика. Чтобы просмотреть настроенные пулы NAT для входящего трафика, можно использовать эту [команду CLI](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list) .
```azurecli-interactive
az network lb inbound-nat-pool create 
        -g MyResourceGroup 
        --lb-name MyLb
        -n MyNatPool 
        --protocol Tcp 
        --frontend-port-range-start 80 
        --frontend-port-range-end 89 
        --backend-port 80 
        --frontend-ip-name MyFrontendIp
az vmss update 
        -g MyResourceGroup 
        -n myVMSS 
        --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
        
az vmss update-instances
        -–instance-ids *
        --resource-group MyResourceGroup
        --name MyVMSS
```
## <a name="how-to-update-inbound-nat-rules"></a>Как обновить правила NAT для входящего трафика? 
  * Не удается обновить отдельное правило NAT для входящего трафика. Однако можно обновить набор правил NAT для входящего трафика с определенным диапазоном внешних портов и внутренним портом для всех экземпляров в масштабируемом наборе виртуальных машин.
  * Чтобы обновить весь набор правил NAT для входящего трафика для масштабируемых наборов виртуальных машин, необходимо обновить пул NAT для входящего трафика в Load Balancer. 
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="how-to-delete-inbound-nat-rules"></a>Как удалить правила NAT для входящего трафика? 
* Невозможно удалить отдельное правило NAT для входящего трафика. Однако можно удалить весь набор правил NAT для входящего трафика.
* Чтобы удалить весь набор правил NAT для входящего трафика, используемый масштабируемым набором, сначала необходимо удалить пул NAT из масштабируемого набора. Полный пример использования CLI приведен ниже.
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
   az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```

## <a name="how-to-add-multiple-ip-configurations"></a>Как добавить несколько IP-конфигураций:
1. Щелкните **Все ресурсы** в меню слева, а затем из списка ресурсов выберите **MyLoadBalancer**.
   
1. В разделе **Параметры** выберите **IP-конфигурации переднего плана** и нажмите кнопку **добавить**.
   
1. На странице **Добавление внешнего IP-адреса** введите значения и нажмите кнопку **ОК** .

1. Выполните [Шаг 5](https://docs.microsoft.com/azure/load-balancer/load-balancer-multiple-ip#step-5-configure-the-health-probe) и [Шаг 6](https://docs.microsoft.com/azure/load-balancer/load-balancer-multiple-ip#step-5-configure-the-health-probe) в этом учебнике, если требуются новые правила балансировки нагрузки.

1. При необходимости создайте новый набор правил NAT для входящего трафика, используя только что созданные IP-конфигурации внешнего интерфейса. Пример можно найти здесь в [предыдущем разделе].

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>Удаление внешней IP-конфигурации, используемой в масштабируемом наборе виртуальных машин: 
 1. Чтобы удалить внешнюю IP-конфигурацию, используемую масштабируемым набором, сначала необходимо удалить пул входящих подключений NAT (набор правил NAT для входящего трафика), ссылающийся на IP-конфигурацию внешнего интерфейса. Инструкции по удалению правил для входящих подключений можно найти в предыдущем разделе.
 1. Удалите правило балансировки нагрузки, ссылающееся на интерфейсную IP-конфигурацию. 
 1. Удалите интерфейсную IP-конфигурацию.
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>Удаление Azure Load Balancer, используемых в масштабируемом наборе виртуальных машин: 
 1. Чтобы удалить внешнюю IP-конфигурацию, используемую масштабируемым набором, сначала необходимо удалить пул входящих подключений NAT (набор правил NAT для входящего трафика), ссылающийся на IP-конфигурацию внешнего интерфейса. Инструкции по удалению правил для входящих подключений можно найти в предыдущем разделе.
 
 1. Удалите правило балансировки нагрузки, ссылающееся на внутренний пул, содержащий масштабируемый набор виртуальных машин.
 
 1. Удалите ссылку Лоадбаланцербаккендаддресспул из сетевого профиля масштабируемого набора виртуальных машин. Полный пример использования CLI приведен ниже.
 ```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
  az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
```
Наконец, удалите ресурс Load Balancer.
 
## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure Load Balancer и масштабируемом наборе виртуальных машин см. в статье основные понятия.

> [Azure Load Balancer с масштабируемыми наборами виртуальных машин Azure](load-balancer-standard-virtual-machine-scale-sets.md)
