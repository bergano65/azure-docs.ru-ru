---
title: Ускорение операций управляемого диска
description: Сведения о ускорении дисковых операций для дисков Azure и ускорения дисков для виртуальных машин Azure в Linux.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ee3b65e95fe3f4c52136cb3645c987fe7f6c9a7a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975371"
---
# <a name="managed-disk-bursting"></a>Ускорение операций управляемого диска
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Ускорение операций на уровне виртуальной машины
Поддержка ускорения операций на уровне виртуальной машины включена во всех регионах общедоступного облака с такими поддерживаемыми размерами: 
- [Серия Lsv2](../lsv2-series.md)

Кроме того, в западной центральной части США доступно увеличение уровня виртуальной машины для следующих поддерживаемых размеров:
- [Серия Dsv3](../dv3-dsv3-series.md)
- [серия Esv3](../ev3-esv3-series.md);

Ускорение операций включено по умолчанию для виртуальных машин, которые поддерживают его.

## <a name="disk-level-bursting"></a>Ускорение операций на уровне дисков
Ускорение операций также доступно для наших [дисков SSD цен. категории "Премиум"](../disks-types.md#premium-ssd) с размером вплоть до P20 во всех регионах общедоступного облака Azure, облака для государственных учреждений и облака для Китая. По умолчанию для всех новых и существующих развертываний размеров дисков, поддерживающих эту возможность, включена Ускоренная работа диска. 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]