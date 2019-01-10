---
title: Управление доменами сбоя в масштабируемых наборах виртуальных машин Azure | Документация Майкрософт
description: Узнайте, как выбрать нужное число доменов сбоя при создании масштабируемого набора виртуальных машин.
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: drewm
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/18
ms.author: rajraj
ms.openlocfilehash: 3b5568e0eed06684667b8aeefa389b6d0aab2a5f
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53636125"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Выбор нужного числа доменов сбоя для масштабируемого набора виртуальных машин
Масштабируемые наборы виртуальных машин создаются с пятью доменами сбоя по умолчанию в регионах Azure без зон. Для регионов, поддерживающих зональные развертывания масштабируемых наборов виртуальных машин, по умолчанию для каждой зоны выделяется один домен сбоя. FD = 1 в данном случае означает, что экземпляры виртуальных машин, принадлежащих к масштабируемому набору, будут распределены по многим стойкам по мере возможностей.

Можно также уравнять количество доменов сбоя масштабируемого набора с количеством доменов сбоя управляемых дисков. Такое выравнивание поможет предотвратить потерю кворума в том случае, если весь домен сбоя управляемых дисков выйдет из строя. Количество доменов сбоя может быть меньше количества доменов сбоя управляемых дисков, доступных в каждом регионе, или равно этому количеству. Ознакомьтесь с этим [документом](../virtual-machines/windows/manage-availability.md), чтобы узнать больше о количестве доменов сбоя управляемых дисков по регионам.

## <a name="rest-api"></a>REST API
Можно задать для свойства `properties.platformFaultDomainCount` значение 1, 2 или 3 (по умолчанию 5, если не указано). См. документацию по REST API [здесь](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Инфраструктура CLI Azure
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

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о [функциях доступности и избыточности](../virtual-machines/windows/regions-and-availability.md) для сред Azure.
