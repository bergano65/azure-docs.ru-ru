---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 90dc5a067c05cadb3d5e102435b2e3d3de803e28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334758"
---
| Ресурс | Ограничение |
| --- | --- |
| Число ВМ на [подписку](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> на регион. |
| Общее количество ядер виртуальных машин на [подписку](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> на регион. Обратитесь в службу поддержки для увеличения лимита. |
| Общий объем ядер Azure Spot VM за [подписку](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> на регион. Обратитесь в службу поддержки для увеличения лимита. |
| VM на серии, такие как Dv2 и F, ядра на [подписку](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> на регион. Обратитесь в службу поддержки для увеличения лимита. |
| [Наборы доступности](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) на подписку |2000 на регион. |
| Количество виртуальных машин на одну группу доступности | 200 |
| Сертификатов на подписку |Без ограничений<sup>2</sup> |

<sup>1</sup> Ограничения по умолчанию варьируются в зависимости от типа категории предложений, таких как Free Trial и Pay-As-You-Go, а также по сериям, таким как Dv2, F и G. Например, по умолчанию для подписок Enterprise Agreement 350.

<sup>2</sup> С помощью менеджера ресурсов Azure сертификаты хранятся в Хранилище ключей Azure. Количество сертификатов не ограничено для подписки. На развертывание установлен лимит сертификатов в 1 МБ, который состоит либо из одного VM, либо из набора доступности.

> [!NOTE]
> Виртуальные ядра машины имеют региональный общий лимит. Они также имеют ограничение для региональных на размер серии, такие как Dv2 и F. Эти ограничения применяются отдельно. Например, рассмотрим подписку с ограничением до 30 ядер виртуальных машин с восточной части США, 30 ядер серии A и 30 ядер серии D. Эта подписка может развернуть 30 A1 VMs, или 30 D1 VMs, или сочетание этих двух не более 30 ядер. Примером комбинации является 10 VMs A1 и 20 VMs D1.  
> <!-- -->
> 
