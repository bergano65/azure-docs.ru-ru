---
title: Поддержка добавления виртуальных машин Azure в существующую группу доступности | Документы Майкрософт
description: Поддержка добавления виртуальных машин Azure в существующую группу доступности.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 7a5e97b66fec040b4ec32caa8d58cf9b50169a33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60443710"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Поддержка добавления виртуальных машин Azure в существующую группу доступности

Иногда вы можете столкнуться с ограничениями при добавлении новых виртуальных машин в существующую группу доступности. На следующей диаграмме показано, какие серии виртуальных машин можно объединить в одной группе доступности.

Ниже приводится матрица поддержки для объединения различных типов виртуальных машин:

Серии и группа доступности|Вторая виртуальная машина|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Первая виртуальная машина|||||||
|A||OК|OК|OК|OК|OК|
|Av2||OК|OК|OК|OК|OК|
|D||OК|OК|OК|OК|OК|
|Dv2||OК|OК|OК|OК|OК|
|Dv3||OК|OК|OК|OК|OК|

Все остальные серии не могут находиться в одной и той же группе доступности, так как для них требуется определенное оборудование.

Размер виртуальной Машины a8 и A9 нельзя смешивать из-за потребности в выделенных серверной сети RDMA.
