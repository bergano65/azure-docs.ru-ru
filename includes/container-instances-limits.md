---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 055fbc652d0d72925cccfae40efff8b8eef2a8c2
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904422"
---
| Ресурс | Ограничение по умолчанию |
| --- | :--- |
| Группы контейнеров уровня "Стандартный" для каждого региона на [подписку](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Выделенные группы контейнеров SKU для каждого региона на [подписку](../articles/billing-buy-sign-up-azure-subscription.md) | 0<sup>1</sup> |
| Число контейнеров в группе контейнеров | 60 |
| Число томов в группе контейнеров | 20 |
| Портов на каждый IP-адрес | 5 |
| Размер журнала экземпляра контейнера запущенный экземпляр | 4 МБ |
| Размер журнала экземпляра контейнера остановленный экземпляр | 16 КБ или 1000 строк |
| Созданных контейнеров в час |300<sup>1</sup> |
| Созданных контейнеров в 5 мин | 100<sup>1</sup> |
| Удаленных контейнеров в час | 300<sup>1</sup> |
| Удаленных контейнеров в 5 мин | 100<sup>1</sup> |


<sup>1</sup> Чтобы запросить увеличение лимита, создайте [Поддержка Azure][azure-support].<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
