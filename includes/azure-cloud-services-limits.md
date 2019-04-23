---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59805002"
---
| Ресурс | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| [Веб- и рабочих ролей в развертывании](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Входных конечных точек экземпляров](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) на развертывание |25 |25 |
| [Входные конечные точки](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) на развертывание |25 |25 |
| [Внутренние конечные точки](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) на развертывание |25 |25 |
| [Сертификаты размещенной службы](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) на развертывание |199 |199 |

<sup>1</sup>каждая облачная служба Azure с рабочей или веб-роли может иметь два развертывания, один для рабочей среды и одно для промежуточной. Данное ограничение относится к количеству отдельных ролей, то есть конфигурации. Это ограничение не относится число экземпляров каждой роли, то есть масштабирование.

