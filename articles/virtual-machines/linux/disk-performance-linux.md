---
title: Производительность виртуальных машин и дисков — Linux
description: Узнайте больше о том, как виртуальные машины и их подключенные диски работают в сочетании с производительностью Linux.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 2c48672bcfd8c552b36e3ae0807135924669c1d9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591860"
---
# <a name="virtual-machine-and-disk-performance-linux"></a>Производительность виртуальных машин и дисков (Linux)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Кэшированные ограничения виртуальной машины в сравнении с кэшем
Для виртуальных машин, включенных как для хранилища класса Premium, так и для кэширования хранилища класса Premium, существует два разных ограничения пропускной способности хранилища. Рассмотрим Standard_D8s_v3 виртуальную машину в качестве примера. Ниже приведена документация по [серии Dsv3](../dv3-dsv3-series.md) и Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Давайте выполним тест производительности на этой виртуальной машине и в сочетании с дисками, создающими операции ввода-вывода. Дополнительные сведения о тестировании производительности операций ввода-вывода в Azure см. в разделе [Тестирование производительности приложения на хранилище дисков Azure](disks-benchmarks.md). В средстве тестирования производительности можно увидеть, что сочетание ВМ и диска может достигать 22 800 операций ввода-вывода в секунду:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
