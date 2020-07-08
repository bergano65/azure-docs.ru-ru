---
author: rothja
ms.service: cloud-services
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 390460cf6a22c164ee41a9b6679c7b7f2979d8ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85838875"
---
| Ресурс | Ограничение |
| --- | --- |
| [Рабочие и веб-роли для развертывания](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Входные конечные точки экземпляра](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) на развертывание |25 |
| [Входные конечные точки](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) на развертывание |25 |
| [Внутренние конечные точки](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) на развертывание |25 |
| [Сертификаты размещенной службы](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) на развертывание |199 |

<sup>1</sup> Каждая облачная служба Azure с веб-ролями или рабочей ролью может иметь два развертывания: одно для рабочей среды и одно для промежуточного хранения. Это ограничение относится к числу уникальных ролей, то есть к конфигурации. Это ограничение не относится к количеству экземпляров на роль, то есть к масштабированию.

