---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: acf5a7e8d7feebaac3d82629c5b561a22112de0f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553987"
---
| Ресурс | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| [Веб- и рабочих ролей в развертывании](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Входных конечных точек экземпляров](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) на развертывание |25 |25 |
| [Входные конечные точки](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) на развертывание |25 |25 |
| [Внутренние конечные точки](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) на развертывание |25 |25 |
| [Сертификаты размещенной службы](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) на развертывание |199 |199 |

<sup>1</sup>каждая облачная служба Azure с рабочей или веб-роли может иметь два развертывания, один для рабочей среды и одно для промежуточной. Данное ограничение относится к количеству отдельных ролей, то есть конфигурации. Это ограничение не относится число экземпляров каждой роли, то есть масштабирование.

