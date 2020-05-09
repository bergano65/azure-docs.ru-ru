---
title: Нагрузка управляемого диска
description: Сведения о ускорении дисковых операций для дисков Azure и ускорения дисков для виртуальных машин Azure
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594464"
---
# <a name="disk-bursting"></a>Всплеск активности диска
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Пакетная передача на уровне виртуальной машины
Поддержка ускорения на уровне виртуальной машины включена во всех регионах общедоступного облака с поддерживаемыми размерами: 
- [Серия Lsv2](../lsv2-series.md)

Для виртуальных машин, поддерживающих эту возможность, по умолчанию включено выполнение пакетов.

## <a name="disk-level-bursting"></a>Разбивка на уровни диска
Пакетная поддержка также доступна в наших [твердотельных накопителях](disks-types.md#premium-ssd) уровня "Премиум" для размера диска P20 и меньше во всех регионах. По умолчанию для новых развертываний размеров дисков, поддерживающих эту возможность, включена Ускоренная работа диска. Существующие размеры дисков, если они поддерживают ускорение работы с дисками, могут включать в себя один из следующих методов: 
- **Перезапустите виртуальную машину.** 
- **Отсоединение и повторное подключение диска**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
