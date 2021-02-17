---
title: Ускорение операций управляемого диска
description: Сведения о ускоренном использовании дисков для дисков Azure и виртуальных машин Azure.
author: albecker1
ms.author: albecker
ms.date: 01/27/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1cedac5814d1c547a28e9b1c894f416af5a924b5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585077"
---
# <a name="managed-disk-bursting"></a>Ускорение операций управляемого диска
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Ускорение операций на уровне виртуальной машины
На следующей серии виртуальных машин во всех регионах, в которых они поддерживаются, включена возможность разбивки на уровни виртуальных машин.
- [Серия Lsv2](lsv2-series.md)
- [Серия Dsv3](dv3-dsv3-series.md)
- [серия Esv3](ev3-esv3-series.md);

Ускорение операций включено по умолчанию для виртуальных машин, которые поддерживают его.

## <a name="disk-level-bursting"></a>Ускорение операций на уровне дисков
Ускорение операций также доступно для наших [дисков SSD цен. категории "Премиум"](disks-types.md#premium-ssd) с размером вплоть до P20 во всех регионах общедоступного облака Azure, облака для государственных учреждений и облака для Китая. По умолчанию для всех новых и существующих развертываний размеров дисков, поддерживающих эту возможность, включена Ускоренная работа диска. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Дальнейшие шаги

Сведения о том, как получить представление о пакетных ресурсах, см. в разделе [метрики ускорения работы диска](disks-metrics.md).
