---
title: Использование групп размещения близости для виртуальных машин Linux
description: Узнайте, как создавать и использовать группы размещения близкого взаимодействия для виртуальных машин Linux в Azure.
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
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171054"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Развертывание виртуальных машин в группах размещения с помощью Azure CLI

Чтобы виртуальные машины максимально близки к максимально возможной задержке, следует развернуть их в [группе размещения](co-location.md#proximity-placement-groups)с учетом расположения.

Группа размещения с учетом расположения — это логическая группировка, используемая для того, чтобы ресурсы вычислений Azure физически размещались близко друг к другу. Группы размещения с учетом расположения удобны для рабочих нагрузок, где требуется низкая задержка.


## <a name="create-the-proximity-placement-group"></a>Создание группы размещения с учетом расположения
Создайте группу размещения с учетом расположения с помощью [`az ppg create`](/cli/azure/ppg#az-ppg-create). 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Список групп размещения с учетом расположения

Список всех групп размещения с учетом расположения можно вывести с помощью команды [AZ ППГ List](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Создание ВМ

Создайте виртуальную машину в группе размещения с учетом расположения, используя [новую виртуальную машину AZ](/cli/azure/vm#az-vm-create).

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

Виртуальную машину можно просмотреть в группе размещения с помощью команды [AZ ППГ Показать](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Группы доступности
Вы также можете создать группу доступности в группе размещения с близкой назначением. Используйте один и тот же параметр `--ppg` с помощью команды [AZ VM Availability — Set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create) , чтобы создать группу доступности, а все виртуальные машины в группе доступности также будут созданы в той же группы размещения близости.

## <a name="scale-sets"></a>Масштабируемые наборы

Вы также можете создать масштабируемый набор в группе размещения с учетом расположения. Используйте тот же параметр `--ppg` с помощью команды [AZ vmss Create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) , чтобы создать масштабируемый набор, и все экземпляры будут созданы в той же группе размещения с учетом расположения.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о командах [Azure CLI](/cli/azure/ppg) для групп размещения с учетом расположения.
