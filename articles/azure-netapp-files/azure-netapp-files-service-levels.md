---
title: Уровни обслуживания для Azure NetApp Files | Документация Майкрософт
description: Описывает производительность пропускной способности для уровней обслуживания Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832585"
---
# <a name="service-levels-for-azure-netapp-files"></a>Уровни обслуживания для Azure NetApp Files
Уровни обслуживания являются атрибутом пула емкости. Уровни обслуживания определяются и дифференцируются по разрешенной максимальной пропускной способности для объема в пуле емкости на основе квоты, присвоенной объему.

## <a name="supported-service-levels"></a>Поддерживаемые уровни обслуживания

Файлы Azure NetApp поддерживают три уровня обслуживания: *Ultra,* *Premium*и *Standard*. 

* <a name="Ultra"></a>Ультра хранилище

    Уровень хранения Ultra обеспечивает до 128 MiB/s пропускной памяти на 1 TiB от установленной квоты объема. 

* <a name="Premium"></a>Премиум-хранилище

    Уровень хранения Premium обеспечивает до 64 MiB/s пропускной выгоды на 1 TiB от установленной квоты объема. 

* <a name="Standard"></a>Стандартное хранилище

    Стандартный уровень хранения обеспечивает до 16 MiB/s пропускной выгоды на 1 TiB от установленной квоты объема.

## <a name="throughput-limits"></a>Ограничения пропускной способности

Предельный объем пропускной воды определяется сочетанием следующих факторов:
* Уровень обслуживания пула емкости, к которому относится объем
* Квота, присвоенная тому,  

Эта концепция проиллюстрирована на диаграмме ниже:

![Иллюстрация уровня обслуживания](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

В приведенном выше примере объем из пула емкости с уровнем хранения Premium, который присваивается 2 TiB квоты, будет присвоен предел пропускной способности 128 МВ/с (2 TiB - 64 МБ/с). Этот сценарий применяется независимо от размера пула емкости или фактического потребления объема.

В приведенном выше примере 2 объем из пула емкости с уровнем хранения Premium, на который присваивается 100 GiB квоты, будет присвоен лимит пропускной способности 6,25 МВ/с (0,09765625 TiB 64 МБ/с). Этот сценарий применяется независимо от размера пула емкости или фактического потребления объема.

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о ценах на разные уровни обслуживания см. на странице [Цены на службу Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- Просмотрите [модель затрат для файлов Azure NetApp](azure-netapp-files-cost-model.md) для расчета потребления емкости в пуле емкости 
- [Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)
- Ознакомьтесь [с соглашением об уровне обслуживания (SLA) для файлов NetApp Azure](https://azure.microsoft.com/support/legal/sla/netapp/)