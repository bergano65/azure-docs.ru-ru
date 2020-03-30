---
title: Используйте группы размещения близости для Linux VMs
description: Узнайте о создании и использовании групп размещения непосредственной близости для виртуальных машин Linux в Azure.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 353a266b647c299515c15889c302ba4409aa511b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73171054"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Развертывание вс-м входить в группы размещения объектов с помощью Azure CLI

Чтобы получить ВМ как можно ближе, достигнув минимально возможной задержки, вы должны развернуть их в [группе размещения близости.](co-location.md#proximity-placement-groups)

Группа размещения близости — это логическая группировка, используемая для того, чтобы обеспечить физические расположения ресурсов Azure вычислений близко друг к другу. Группы размещения близости полезны для рабочих нагрузок, где требуется низкая задержка.


## <a name="create-the-proximity-placement-group"></a>Создание группы размещения близости
Создайте группу [`az ppg create`](/cli/azure/ppg#az-ppg-create)размещения близости с помощью . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Список групп размещения близости

Вы можете перечислить все ваши группы размещения близости, используя [список az ppg.](/cli/azure/ppg#az-ppg-list)

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Создание виртуальной машины

Создайте VM в группе размещения близости, используя [новый az vm.](/cli/azure/vm#az-vm-create)

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

Вы можете увидеть VM в группе размещения близости с помощью [az ppg шоу](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Группы доступности
Вы также можете создать набор доступности в вашей группе размещения близости. Используйте `--ppg` тот же параметр с [набором доступности az vm](/cli/azure/vm/availability-set#az-vm-availability-set-create) для создания набора доступности, и все ВМ в наборе доступности также будут созданы в той же группе размещения близости.

## <a name="scale-sets"></a>Масштабируемые наборы

Вы также можете создать набор шкалы в группе размещения близости. Используйте `--ppg` тот же параметр с [az vmss создать](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) для создания набора масштаба и все экземпляры будут созданы в той же группе размещения близости.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о командах [Azure CLI](/cli/azure/ppg) для групп размещения непосредственной близости.
