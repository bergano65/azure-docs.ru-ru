---
title: Создание экземпляра Load Balancer с избыточным интерфейсным сервером между зонами — интерфейс командной строки Azure
titlesuffix: Azure Load Balancer
description: Сведения о создании общедоступной подсистемы Load Balancer (цен. категория "Стандартный") с избыточным интерфейсным общедоступным IP-адресом между зонами с помощью интерфейса командной строки Azure
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
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 40396a8ef62553dd3ec721e97fe08bf8aa51c731
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122115"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-cli"></a>Создание экземпляра Load Balancer (цен. категория "Стандартный") с избыточным между зонами интерфейсным сервером с помощью Azure CLI

В этой статье описываются действия по созданию общедоступного [балансировщика нагрузки уровня "Стандартный"](https://aka.ms/azureloadbalancerstandard) с избыточным между зонами интерфейсным сервером с помощью общедоступного стандартного IP-адреса.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы планируете установить CLI и использовать его локально, обязательно установите последнюю версию [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) и войдите в учетную запись Azure с помощью команды [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest).

> [!NOTE]
>  Поддержка зон доступности реализована для некоторых ресурсов, регионов и семейств размеров виртуальных машин Azure. Дополнительные сведения о начале работы и о том, какие ресурсы, регионы и семейства размеров виртуальных машин Azure поддерживаются для зон доступности, см. в разделе [Обзор зон доступности](https://docs.microsoft.com/azure/availability-zones/az-overview). Чтобы получить поддержку, обратитесь на форум [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) или [создайте запрос в службу поддержки Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Создание группы ресурсов

Чтобы создать группу ресурсов, выполните следующую команду:

```azurecli-interactive
az group create --name myResourceGroupSLB --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Создание общедоступного стандартного IP-адреса

Создайте общедоступный стандартный IP-адрес с помощью следующей команды:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Создание балансировщика нагрузки

Создайте общедоступный балансировщик нагрузки уровня "Стандартный" с созданным на предыдущем шаге общедоступным стандартным IP-адресом с помощью следующей команды:

```azurecli-interactive
az network lb create --resource-group myResourceGroupSLB --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Создание зонда балансировщика нагрузки на порту 80

Создайте зонд работоспособности балансировщика нагрузки с помощью следующей команды:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupSLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Создание правила балансировщика нагрузки для порта 80

Создайте правило балансировщика нагрузки с помощью следующей команды:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения см. в статье [Azure Load Balancer уровня "Стандартный" и зоны доступности](load-balancer-standard-availability-zones.md).



