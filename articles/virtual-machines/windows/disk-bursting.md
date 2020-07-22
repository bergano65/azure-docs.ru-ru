---
title: Ускорение операций управляемого диска
description: Узнайте больше об ускорении операций для дисков Azure и ускорении дисков для виртуальных машин Azure.
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82594464"
---
# <a name="disk-bursting"></a>Всплеск активности диска
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Ускорение операций на уровне виртуальной машины
Поддержка ускорения операций на уровне виртуальной машины включена во всех регионах общедоступного облака с такими поддерживаемыми размерами: 
- [Серия Lsv2](../lsv2-series.md)

Ускорение операций включено по умолчанию для виртуальных машин, которые поддерживают его.

## <a name="disk-level-bursting"></a>Ускорение операций на уровне дисков
Пакетная поддержка также доступна в наших [твердотельных накопителях](disks-types.md#premium-ssd) уровня "Премиум" для размера диска P20 и меньше во всех регионах. Ускорение дисковых операций включено по умолчанию в новых развертываниях для дисков любых размеров, которые поддерживают эту функцию. Для имеющихся размеров дисков можно включить ускорение операций (если они поддерживают его) с помощью одного из таких методов: 
- **Перезапустите виртуальную машину.** 
- **Отсоедините и повторно присоедините диск.**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
