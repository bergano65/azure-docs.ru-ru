---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335022"
---
| Ресурс | Ограничение |
| --- | --- |
| [Виртуальных машин](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) на облачную службу<sup>1</sup> |50 |
| Входных конечных точек на облачную службу<sup>2</sup> |150 |

<sup>1</sup> Виртуальные машины, созданные с помощью классической модели развертывания, а не Azure Resource Manager, автоматически сохраняются в облачной службе. Для балансировки нагрузки и обеспечения доступности можно добавить дополнительные виртуальные машины в эту облачную службу. 

<sup>2</sup>Входные конечные точки позволяют обмениваться данными с виртуальной машиной за пределами облачной службы, в которой находится эта виртуальная машина. Виртуальные машины, размещенные в одной и той же облачной службе или виртуальной сети, могут автоматически взаимодействовать друг с другом. Дополнительные сведения см. в статье [Настройка конечных точек для виртуальной машины](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
