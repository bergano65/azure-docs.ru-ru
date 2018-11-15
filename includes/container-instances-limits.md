---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 44bdaec78e1fad574f29a5945b07041b588aaff8
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572638"
---
| Ресурс | Ограничение по умолчанию |
| --- | :--- |
| Групп контейнеров на [подписку](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Число контейнеров в группе контейнеров | 60 |
| Число томов в группе контейнеров | 20 |
| Портов на каждый IP-адрес | 5 |
| Созданных контейнеров в час |300<sup>1</sup> |
| Созданных контейнеров в 5 мин | 100<sup>1</sup> |
| Удаленных контейнеров в час | 300<sup>1</sup> |
| Удаленных контейнеров в 5 мин | 100<sup>1</sup> |
| Несколько контейнеров в группе контейнеров | Только Linux<sup>2</sup> |
| Тома файлов Azure | Только Linux<sup>2</sup> |
| Тома GitRepo | Только Linux<sup>2</sup> |
| Секретные тома | Только Linux<sup>2</sup> |

<sup>1</sup> Отправьте [запрос в службу поддержки Azure][azure-support] на увеличение лимита.<br />
<sup>2</sup> Поддержка этой функции в Windows планируется.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
