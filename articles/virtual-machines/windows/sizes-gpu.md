---
title: Размеры виртуальных машин Windows в Azure — GPU
description: Список различных размеров виртуальных машин Windows в Azure, оптимизированных для графического процессора. Сведения о количестве виртуальных ЦП, дисков данных и сетевых адаптеров, а также о пропускной способности хранилища и сети для размеров виртуальных машин этой серии.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 1f26ad56734dfec0dbf7fc8198b8a91a706a843b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75371077"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Размеры виртуальных машин, оптимизированных для GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

Чтобы воспользоваться преимуществами возможностей GPU виртуальных машин Azure серии N под управлением Windows, необходимо установить драйверы NVIDIA или AMD GPU. 

[Расширение драйвера GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) устанавливает необходимые драйверы CUDA или GRID NVIDIA на виртуальную машину серии N. Для установки расширения и управления им можно использовать портал Azure или такие инструменты, как Azure PowerShell и шаблоны Azure Resource Manager. Сведения о поддерживаемых операционных системах и этапах развертывания см. в [документации по расширению драйвера GPU NVIDIA](../extensions/hpccompute-gpu-windows.md). Общие сведения о расширениях виртуальных машин см. в статье [Расширения и компоненты виртуальных машин Azure](../extensions/overview.md).

Если вы решили установить драйверы GUP NVIDIA вручную, то сможете найти сведения о поддерживаемых операционных системах и действиях по установке и проверке в разделе [Установка драйвера GPU для виртуальных машин серии N под управлением Windows](n-series-driver-setup.md).

Чтобы вручную установить драйверы AMD GPU, см. раздел [Установка драйвера GPU AMD серии N для Windows](n-series-amd-driver-setup.md) для поддерживаемых операционных систем, драйверов и шагов по установке и проверке.

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="other-sizes"></a>Остальные размеры
- [Универсальные](sizes-general.md)
- [Оптимизированные для вычислений](sizes-compute.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.

