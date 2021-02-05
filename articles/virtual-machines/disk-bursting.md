---
title: Ускорение операций управляемого диска
description: Сведения о ускоренном использовании дисков для дисков Azure и виртуальных машин Azure.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: dcdbf94e547581cb9ff885ac5896467abdf316ae
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576199"
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
