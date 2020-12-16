---
title: Производительность виртуальных машин и дисков — Windows
description: Узнайте больше о том, как виртуальные машины и их подключенные диски работают в сочетании с производительностью Windows.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: dec8b0cc33a9fffa7cac1ac9261b3c38ef5a6449
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584125"
---
# <a name="virtual-machine-and-disk-performance-windows"></a>Производительность виртуальных машин и дисков (Windows)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Кэшированные ограничения виртуальной машины в сравнении с кэшем
 Виртуальные машины, для которых включено хранилище класса Premium и включено кэширование хранилища класса Premium, имеют два разных ограничения пропускной способности хранилища. Давайте продолжим рассмотрение Standard_D8s_v3 виртуальной машины в качестве примера. Ниже приведена документация по [серии Dsv3](../dv3-dsv3-series.md) и Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Давайте выполним тест производительности для этой виртуальной машины и диска, которые будут создавать операции ввода-вывода, и вы сможете узнать все о том, как выполнить тестирование операций ввода-вывода хранилища [в Azure.](disks-benchmarks.md) В средстве тестирования производительности можно увидеть, что сочетание виртуальных машин и дисков способно достичь 22 800 операций ввода-вывода в секунду:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]