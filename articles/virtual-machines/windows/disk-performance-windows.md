---
title: Производительность диска и виртуальной машины
description: Узнайте больше о том, как виртуальные машины и их подключенные диски работают в сочетании с производительностью.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 88cf9c28a9d325d617f4b049015f0cd238a2fb31
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016465"
---
# <a name="virtual-machine-and-disk-performance"></a>Производительность диска и виртуальной машины
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Кэшированные ограничения виртуальной машины в сравнении с кэшем
 Виртуальные машины, для которых включено хранилище класса Premium и включено кэширование хранилища класса Premium, имеют два разных ограничения пропускной способности хранилища. Давайте продолжим рассмотрение Standard_D8s_v3 виртуальной машины в качестве примера. Ниже приведена документация по [серии Dsv3](../dv3-dsv3-series.md) и Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Давайте выполним тест производительности для этой виртуальной машины и диска, которые будут создавать операции ввода-вывода, и вы сможете узнать все о том, как выполнить тестирование операций ввода-вывода хранилища [в Azure.](disks-benchmarks.md) В средстве тестирования производительности можно увидеть, что сочетание виртуальных машин и дисков способно достичь 22 800 операций ввода-вывода в секунду:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]