---
title: Поддержка добавления виртуальных машин Azure в существующую группу доступности | Документы Майкрософт
description: Поддержка добавления виртуальных машин Azure в существующую группу доступности.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3954df389516aa7199022d713dc63d62dda961ae
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155458"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Поддержка добавления виртуальных машин Azure в существующую группу доступности

Иногда вы можете столкнуться с ограничениями при добавлении новых виртуальных машин в существующую группу доступности. На следующей диаграмме показано, какие серии виртуальных машин можно объединить в одной группе доступности.

Ниже приводится матрица поддержки для объединения различных типов виртуальных машин:

Серии и группа доступности|Вторая виртуальная машина|Объект|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Первая виртуальная машина|||||||
|Объект||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Все остальные серии не могут находиться в одной и той же группе доступности, так как для них требуется определенное оборудование.

Размер виртуальной машины A8/A9 не может быть смешан из-за необходимости в выделенной внутренней сети RDMA.
