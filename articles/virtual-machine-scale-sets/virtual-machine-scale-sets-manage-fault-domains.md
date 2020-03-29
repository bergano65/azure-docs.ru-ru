---
title: Управление доменами неисправностей в наборах виртуальных машин Azure
description: Узнайте, как выбрать нужное число доменов сбоя при создании масштабируемого набора виртуальных машин.
author: rajsqr
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: 297837354cea4bb5ccdcc03261810dcffd144243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275728"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Выбор нужного числа доменов сбоя для масштабируемого набора виртуальных машин
Масштабируемые наборы виртуальных машин создаются с пятью доменами сбоя по умолчанию в регионах Azure без зон. Для регионов, поддерживающих зональные развертывания виртуальных наборов масштабов машин и выбрана эта опция, значение домена по умолчанию составляет 1 для каждой из зон. FD = 1 в данном случае означает, что экземпляры виртуальных машин, принадлежащих к масштабируемому набору, будут распределены по многим стойкам по мере возможностей.

Можно также уравнять количество доменов сбоя масштабируемого набора с количеством доменов сбоя управляемых дисков. Такое выравнивание поможет предотвратить потерю кворума в том случае, если весь домен сбоя управляемых дисков выйдет из строя. Количество доменов сбоя может быть меньше количества доменов сбоя управляемых дисков, доступных в каждом регионе, или равно этому количеству. Ознакомьтесь с этим [документом](../virtual-machines/windows/manage-availability.md), чтобы узнать больше о количестве доменов сбоя управляемых дисков по регионам.

## <a name="rest-api"></a>REST API
Можно задать для свойства `properties.platformFaultDomainCount` значение 1, 2 или 3 (по умолчанию 5, если не указано). См. документацию по REST API [здесь](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Azure CLI
Можно задать для параметра `--platform-fault-domain-count` значение 1, 2 или 3 (по умолчанию 5, если не указано). См. документацию по Azure CLI [здесь](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

Создание и настройка всех ресурсов и виртуальных машин масштабируемого набора занимает несколько минут.

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения о [функциях доступности и избыточности](../virtual-machines/windows/availability.md) для сред Azure.
