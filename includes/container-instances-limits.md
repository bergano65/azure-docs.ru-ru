---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 33a82a55b03cde79d2d80826041ee6d43565476a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334815"
---
| Ресурс | Ограничение |
| --- | :--- |
| Стандартные группы контейнеров sku в регионе за [подписку](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Выделенные группы контейнеров sku в регионе за [подписку](../articles/billing-buy-sign-up-azure-subscription.md) | 0<sup>1</sup> |
| Число контейнеров в группе контейнеров | 60 |
| Число томов в группе контейнеров | 20 |
| Портов на каждый IP-адрес | 5 |
| Размер журнала экземпляра контейнера запущенный экземпляр | 4 МБ |
| Размер журнала экземпляра контейнера остановленный экземпляр | 16 КБ или 1000 строк |
| Созданных контейнеров в час |300<sup>1</sup> |
| Созданных контейнеров в 5 мин | 100<sup>1</sup> |
| Удаленных контейнеров в час | 300<sup>1</sup> |
| Удаленных контейнеров в 5 мин | 100<sup>1</sup> |


<sup>1</sup>Чтобы увеличить лимит, отправьте [запрос в службу поддержки Azure][azure-support].<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
