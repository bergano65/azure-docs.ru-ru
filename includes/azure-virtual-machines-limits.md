---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "67185645"
---
| Resource | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| [Виртуальных машин](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) на облачную службу<sup>1</sup> |50 |50 |
| Входных конечных точек на облачную службу<sup>2</sup> |150 |150 |

<sup>1</sup> Виртуальные машины, созданные с помощью классической модели развертывания, а не Azure Resource Manager, автоматически сохраняются в облачной службе. Для балансировки нагрузки и обеспечения доступности можно добавить дополнительные виртуальные машины в эту облачную службу. 

<sup>2</sup>Входные конечные точки позволяют обмениваться данными с виртуальной машиной за пределами облачной службы, в которой находится эта виртуальная машина. Виртуальные машины, размещенные в одной и той же облачной службе или виртуальной сети, могут автоматически взаимодействовать друг с другом. Дополнительные сведения см. в статье [Настройка конечных точек виртуальной машины](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
