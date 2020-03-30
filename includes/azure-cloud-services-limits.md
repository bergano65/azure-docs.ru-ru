---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3295c7e677e4c9bf7bff27614fe5fa4fa3200570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334651"
---
| Ресурс | Ограничение |
| --- | --- |
| [Веб или рабочие роли в развертывании](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Конечные точки ввода экземпляров](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) в развертывание |25 |
| [Конечные точки ввода](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) в развертывание |25 |
| [Внутренние конечные точки](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) на развертывание |25 |
| [Сертификаты размещенной службы](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) на развертывание |199 |

<sup>1</sup> Каждая облачная служба Azure с веб-функциями или ролями сотрудников может иметь два развертывания: одно для производства и одно для постановки. Это ограничение относится к числу различных ролей, т.е. к конфигурации. Это ограничение не относится к количеству экземпляров на роль, т.е. к масштабированию.

