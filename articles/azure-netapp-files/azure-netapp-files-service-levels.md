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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75832585"
---
# <a name="service-levels-for-azure-netapp-files"></a>Уровни обслуживания для Azure NetApp Files
Уровни обслуживания являются атрибутами пула ресурсов. Уровни обслуживания определяются и отличаются максимально допустимой пропускной способностью для тома в пуле ресурсов на основе квоты, назначенной тому.

## <a name="supported-service-levels"></a>Поддерживаемые уровни обслуживания

Azure NetApp Files поддерживает три уровня обслуживания: *Ultra*, *Premium*и *Standard*. 

* <a name="Ultra"></a>Ultra Storage

    Уровень Ultra Storage предоставляет до 128 MiB/с пропускной способностью в 1 Тиб назначенной квоты тома. 

* <a name="Premium"></a>Хранилище класса Premium

    Уровень хранилища Premium предоставляет до 64 MiB/с пропускной способностью в 1 Тиб назначенной квоты тома. 

* <a name="Standard"></a>Хранилище уровня "Стандартный"

    Уровень хранилища "Стандартный" предоставляет до 16 MiB/с пропускной способностью в 1 Тиб назначенной квоты тома.

## <a name="throughput-limits"></a>Ограничения пропускной способности

Ограничение пропускной способности для тома определяется сочетанием следующих факторов.
* Уровень обслуживания пула емкости, к которому относится том
* Квота, назначенная тому  

Эта концепция показана на схеме ниже.

![Иллюстрация уровня обслуживания](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

В примере 1 для тома из пула емкости с уровнем хранилища Premium, которому назначено 2 Тиб квоты, будет назначен предел пропускной способности 128 MiB/с (2 Тиб * 64 MiB/с). Этот сценарий применим независимо от размера пула емкости или фактического потребления объема данных.

В примере 2 для тома из пула емкости с уровнем хранилища Premium, которому назначено 100 гиб квоты, будет назначено ограничение пропускной способности 6,25 MiB/s (0,09765625 Тиб * 64 MiB/s). Этот сценарий применим независимо от размера пула емкости или фактического потребления объема данных.

## <a name="next-steps"></a>Дальнейшие шаги

- Сведения о ценах на разные уровни обслуживания см. на странице [Цены на службу Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- Расчет потребления емкости в пуле мощностей см. в статье [модель стоимости для Azure NetApp Files](azure-netapp-files-cost-model.md) . 
- [Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)
- См. [соглашение об уровне обслуживания (SLA) для Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)