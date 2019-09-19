---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "67185661"
---
| Resource | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| [Рабочие и веб-роли для развертывания](../articles/cloud-services/cloud-services-choose-me.md) <sup>1</sup> |25 |25 |
| [Входные конечные точки экземпляра](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) на развертывание |25 |25 |
| [Входные конечные точки](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) на развертывание |25 |25 |
| [Внутренние конечные точки](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) на развертывание |25 |25 |
| [Сертификаты размещенной службы](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) на развертывание |199 |199 |

<sup>1</sup> Каждая облачная служба Azure с веб-ролями или рабочей ролью может иметь два развертывания: одно для рабочей среды и одно для промежуточного хранения. Это ограничение относится к числу уникальных ролей, то есть к конфигурации. Это ограничение не относится к количеству экземпляров на роль, то есть к масштабированию.

