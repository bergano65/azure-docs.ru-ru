---
title: Ускорение операций управляемого диска
description: Узнайте больше об ускорении операций для дисков Azure и ускорении дисков для виртуальных машин Azure.
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: baabad550f5e6b0ae39936fc182e6d9789d189d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650940"
---
# <a name="disk-bursting"></a>Всплеск активности диска
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Ускорение операций на уровне виртуальной машины
Поддержка ускорения операций на уровне виртуальной машины включена во всех регионах общедоступного облака с такими поддерживаемыми размерами: 
- [Серия Lsv2](../lsv2-series.md)

Ускорение операций включено по умолчанию для виртуальных машин, которые поддерживают его.

## <a name="disk-level-bursting"></a>Ускорение операций на уровне дисков
Ускорение операций также доступно для наших [дисков SSD цен. категории "Премиум"](disks-types.md#premium-ssd) с размером вплоть до P20 во всех регионах общедоступного облака Azure, облака для государственных учреждений и облака для Китая. Ускорение дисковых операций включено по умолчанию в новых развертываниях для дисков любых размеров, которые поддерживают эту функцию. Для имеющихся размеров дисков можно включить ускорение операций (если они поддерживают его) с помощью одного из таких методов: 
- **Перезапустите виртуальную машину.** 
- **Отсоедините и повторно присоедините диск.**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
