---
title: Использование предварительной версии групп размещения с учетом расположения для виртуальных машин Linux | Документация Майкрософт
description: Узнайте, как создавать и использовать группы размещения близкого взаимодействия для виртуальных машин Linux в Azure.
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: a3009c9863d8322e3fe34bd99d64b93f0aa3d858
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385733"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Предварительный просмотр: Развертывание виртуальных машин в группах размещения с помощью Azure CLI

Чтобы виртуальные машины максимально близки к максимально возможной задержке, следует развернуть их в [группе размещения](co-location.md#preview-proximity-placement-groups)с учетом расположения.

Группа размещения с учетом расположения — это логическая группировка, используемая для того, чтобы ресурсы вычислений Azure физически размещались близко друг к другу. Группы размещения с учетом расположения удобны для рабочих нагрузок, где требуется низкая задержка.

> [!IMPORTANT]
> Группы размещения близкого взаимодействия в настоящее время находятся в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Группы размещения с близкой доступностью недоступны в этих регионах во время предварительной версии: **Восточная Япония**, **Восточная Австралия** и **Индии Central**.

## <a name="create-the-proximity-placement-group"></a>Создание группы размещения с учетом расположения
Создайте группу размещения с учетом [`az ppg create`](/cli/azure/ppg#az-ppg-create)расположения с помощью. 

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

## <a name="create-a-vm"></a>Создание виртуальной машины

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
Вы также можете создать группу доступности в группе размещения с близкой назначением. Используйте тот же `--ppg` параметр, выполнив команду [AZ VM Availability-Set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create) , чтобы создать группу доступности, а все виртуальные машины в группе доступности также будут созданы в той же группы размещения близости.

## <a name="scale-sets"></a>Масштабируемые наборы

Вы также можете создать масштабируемый набор в группе размещения с учетом расположения. Используйте тот же `--ppg` параметр команды [AZ vmss Create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) , чтобы создать масштабируемый набор, и все экземпляры будут созданы в той же группе размещения близости.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о командах [Azure CLI](/cli/azure/ppg) для групп размещения с учетом расположения.
