---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2b6ba1dcddd435c42ad864b8e87175d0e98c9b3a
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279902"
---
| Ресурс | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| [Количество веб- или рабочих ролей на одно развертывание](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Входные конечные точки экземпляра](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) на развертывание |25 |25 |
| [Входные конечные точки](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) на развертывание |25 |25 |
| [Внутренние конечные точки](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) на развертывание |25 |25 |

<sup>1</sup>Каждая облачная служба с рабочими и веб-ролями может иметь два развертывания: одно для производственной среды и одно для промежуточной. Необходимо также иметь в виду, что данное ограничение относится к количеству отдельных ролей (настройка), а не к количеству экземпляров для роли (масштабирование).

