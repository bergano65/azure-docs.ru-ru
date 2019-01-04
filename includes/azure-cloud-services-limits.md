---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: eb1fe7f83ed83efe078be0aadf26cc7db6f498e2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52886218"
---
| Ресурс | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| [Количество веб- или рабочих ролей на одно развертывание](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Входные конечные точки экземпляра](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) на развертывание |25 |25 |
| [Входные конечные точки](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) на развертывание |25 |25 |
| [Внутренние конечные точки](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) на развертывание |25 |25 |
| [Сертификаты размещенной службы](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) на развертывание |199 |199 |

<sup>1</sup>Каждая облачная служба с рабочими и веб-ролями может иметь два развертывания: одно для производственной среды и одно для промежуточной. Необходимо также иметь в виду, что данное ограничение относится к количеству отдельных ролей (настройка), а не к количеству экземпляров для роли (масштабирование).

