---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3295c7e677e4c9bf7bff27614fe5fa4fa3200570
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334651"
---
| Ресурс | Ограничение |
| --- | --- |
| [Рабочие и веб-роли для развертывания](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Входные конечные точки экземпляра](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) на развертывание |25 |
| [Входные конечные точки](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) на развертывание |25 |
| [Внутренние конечные точки](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) на развертывание |25 |
| [Сертификаты размещенной службы](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) на развертывание |199 |

<sup>1</sup> Каждая облачная служба Azure с веб-ролями или рабочей ролью может иметь два развертывания: одно для рабочей среды и одно для промежуточного хранения. Это ограничение относится к числу уникальных ролей, то есть к конфигурации. Это ограничение не относится к количеству экземпляров на роль, то есть к масштабированию.

