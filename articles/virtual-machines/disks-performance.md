---
title: Производительность диска и виртуальной машины
description: Узнайте больше о том, как виртуальные машины и их подключенные диски работают в сочетании с производительностью.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 5e9f6ecc733eccf317e3013752ee2f5b0586ea78
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540764"
---
# <a name="virtual-machine-and-disk-performance"></a>Производительность диска и виртуальной машины
[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Кэшированные ограничения виртуальной машины в сравнении с кэшем
Для виртуальных машин, включенных как для хранилища класса Premium, так и для кэширования хранилища класса Premium, существует два разных ограничения пропускной способности хранилища. Рассмотрим Standard_D8s_v3 виртуальную машину в качестве примера. Ниже приведена документация по [серии Dsv3](dv3-dsv3-series.md) и Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-2.md)]

Давайте выполним тест производительности на этой виртуальной машине и в сочетании с дисками, создающими операции ввода-вывода. Дополнительные сведения о тестировании производительности операций ввода-вывода в Azure см. в разделе [Тестирование производительности приложения на хранилище дисков Azure](disks-benchmarks.md). В средстве тестирования производительности можно увидеть, что сочетание ВМ и диска может достигать 22 800 операций ввода-вывода в секунду:

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-3.md)]
