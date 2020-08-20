---
title: Load Balancer (цен. категория "Стандартный") Azure и масштабируемые наборы виртуальных машин
titleSuffix: Azure Standard Load Balancer and Virtual Machine Scale Sets
description: По этой схеме обучения приступайте к работе с Azure Load Balancer (цен. категория "Стандартный") и масштабируемыми наборами виртуальных машин.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 41f1d6c18eab35bd1a41d4cfa98d0cbda69b35ac
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650338"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure Load Balancer с масштабируемыми наборами виртуальных машин Azure

При работе с масштабируемыми наборами виртуальных машин и подсистемой балансировки нагрузки следует учитывать следующие рекомендации.

## <a name="port-forwarding-and-inbound-nat-rules"></a>Правила пересылки портов и входящих подключений NAT:
  * После создания масштабируемого набора невозможно изменить внутренний порт для правила балансировки нагрузки, используемого зондом работоспособности балансировщика нагрузки. Чтобы изменить порт, можно удалить проверку работоспособности, обновив масштабируемый набор виртуальных машин Azure, обновить порт, а затем снова настроить зонд работоспособности.
  * При использовании масштабируемого набора виртуальных машин в внутреннем пуле подсистемы балансировки нагрузки правила NAT для входящего трафика по умолчанию создаются автоматически.
## <a name="inbound-nat-pool"></a>Пул NAT для входящего трафика:
  * Каждый масштабируемый набор виртуальных машин должен иметь по крайней мере один входящий пул NAT. 
  * Пул NAT для входящего трафика — это набор правил NAT для входящего трафика. Один пул NAT для входящего трафика не поддерживает несколько масштабируемых наборов виртуальных машин.
  * Чтобы удалить пул NAT из существующего масштабируемого набора виртуальных машин, сначала необходимо удалить пул NAT из масштабируемого набора. Полный пример использования CLI приведен ниже.
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
  az vmss update-instances
     -–instance-ids *
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```
## <a name="load-balancing-rules"></a>Правила балансировки нагрузки:
  * При использовании масштабируемого набора виртуальных машин в внутреннем пуле балансировщика нагрузки правило балансировки нагрузки по умолчанию создается автоматически.
## <a name="outbound-rules"></a>Правила исходящего трафика:
  *  Чтобы создать правило исходящего трафика для внутреннего пула, на который уже ссылается правило балансировки нагрузки, необходимо сначала пометить **"создавать неявные правила исходящего трафика"** как **нет** на портале при создании правила балансировки нагрузки для входящего трафика.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Создание правила балансировки нагрузки" border="true":::

Следующие методы можно использовать для развертывания масштабируемого набора виртуальных машин с помощью существующей подсистемы балансировки нагрузки Azure.

* [Настройте в масштабируемом наборе виртуальных машин существующую Azure Load Balancer с помощью портал Azure](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-portal).
* [Настройте в масштабируемом наборе виртуальных машин существующую Azure Load Balancer с помощью Azure PowerShell](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-powershell).
* [Настройте в масштабируемом наборе виртуальных машин существующую Azure Load Balancer с помощью Azure CLI](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-cli).
