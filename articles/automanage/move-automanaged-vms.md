---
title: Перемещение виртуальной машины Azure для управления ими в разных регионах
description: Узнайте, как переместить управляемую виртуальную машину между регионами.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.author: alsin
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e880992143bf79a5a99cc9830957f83167b6b46
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808408"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>Перемещение виртуальной машины Azure для управления учетными данными в другой регион
В этой статье описывается, как включить Автоуправление на виртуальной машине при ее перемещении в другой регион. Может потребоваться переместить виртуальные машины в другой регион по ряду причин. Например, чтобы воспользоваться преимуществами нового региона Azure, чтобы удовлетворить внутренние требования к политике и управлению, а также в ответ на требования к планированию емкости. Эти виртуальные машины, которые вы перемещаете, могут быть в настоящее время управляемыми, и вам может потребоваться, чтобы они оставались управляемыми после перемещения.

## <a name="prerequisites"></a>Предварительные требования
* Убедитесь, что целевой регион [поддерживается системой управления](./automanage-virtual-machines.md#prerequisites).
* Убедитесь, что область рабочей области Log Analytics, регион учетной записи службы автоматизации и целевой регион [— это все](https://docs.microsoft.com/azure/automation/how-to/region-mappings)регионы, поддерживаемые в сопоставлениях регионов.

## <a name="prepare-your-automanaged-vms-for-moving"></a>Подготовка управляемых виртуальных машин для перемещения
Отключение автоуправления на управляемых виртуальных машинах. Это можно сделать, выбрав виртуальные машины в колонке "Управление" и выбрав **Отключить Автоуправление** в колонке "Управление".

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>Перенесите свои управляемые виртуальные машины и повторно включите управление
Дополнительные сведения о перемещении виртуальных машин см. в этой [статье](https://docs.microsoft.com/azure/resource-mover/tutorial-move-region-virtual-machines).

После перемещения виртуальных машин в регионы вы можете снова включить для них функцию управления. Подробные сведения доступны [здесь](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal).

## <a name="next-steps"></a>Дальнейшие действия
* [Дополнительные сведения об управлении Azure](./automanage-virtual-machines.md)
* [Просмотр часто задаваемых вопросов об управлении Azure](./faq.md)