---
title: Производительность диска и виртуальной машины
description: Узнайте больше о том, как виртуальные машины и их подключенные диски работают в сочетании с производительностью.
author: albecker1
ms.author: albecker
ms.date: 09/25/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 18c07fceb65623c286b31398314e2b6f124955a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540043"
---
# <a name="virtual-machine-and-disk-performance"></a>Производительность диска и виртуальной машины
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Кэшированные ограничения виртуальной машины в сравнении с кэшем
 Виртуальные машины, для которых включено хранилище класса Premium и включено кэширование хранилища класса Premium, имеют два разных ограничения пропускной способности хранилища. Давайте продолжим рассмотрение Standard_D8s_v3 виртуальной машины в качестве примера. Ниже приведена документация по [серии Dsv3](../dv3-dsv3-series.md) и Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]
