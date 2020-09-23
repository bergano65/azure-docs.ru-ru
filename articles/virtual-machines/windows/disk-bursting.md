---
title: Ускорение операций управляемого диска
description: Узнайте больше об ускорении операций для дисков Azure и ускорении дисков для виртуальных машин Azure.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ab54b68ab3f7984ee18a39cf3a81fa663af54dee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90889095"
---
# <a name="disk-bursting"></a>Всплеск активности диска
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Ускорение операций на уровне виртуальной машины
Поддержка ускорения операций на уровне виртуальной машины включена во всех регионах общедоступного облака с такими поддерживаемыми размерами: 
- [Серия Lsv2](../lsv2-series.md)

Кроме того, в западной центральной части США доступно увеличение уровня виртуальной машины для следующих поддерживаемых размеров:
- [Серия Dsv3](../dv3-dsv3-series.md)
- [серия Esv3](../ev3-esv3-series.md);

Ускорение операций включено по умолчанию для виртуальных машин, которые поддерживают его.

## <a name="disk-level-bursting"></a>Ускорение операций на уровне дисков
Пакетная поддержка также доступна в наших [твердотельных накопителях](disks-types.md#premium-ssd) уровня "Премиум" для размера диска P20 и меньше во всех регионах. Ускорение дисковых операций включено по умолчанию в новых развертываниях для дисков любых размеров, которые поддерживают эту функцию. Для имеющихся размеров дисков можно включить ускорение операций (если они поддерживают его) с помощью одного из таких методов: 
- **Перезапустите виртуальную машину.** 
- **Отсоедините и повторно присоедините диск.**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
