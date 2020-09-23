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
ms.openlocfilehash: 107b1cbde35ef639883f86153859679a834735a3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891839"
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
Ускорение операций также доступно для наших [дисков SSD цен. категории "Премиум"](disks-types.md#premium-ssd) с размером вплоть до P20 во всех регионах общедоступного облака Azure, облака для государственных учреждений и облака для Китая. По умолчанию для всех новых и существующих развертываний размеров дисков, поддерживающих эту возможность, включена Ускоренная работа диска. 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
