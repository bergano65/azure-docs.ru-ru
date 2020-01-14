---
title: Обзор Хранилища дисков Azure для виртуальных машин под управлением Linux
description: Обзор управляемых дисков Azure, которые отвечают за управление учетными записями хранения, используемыми для виртуальных машин Linux.
author: roygara
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2b25da174399b092fe821a46b235d7a2bd14572b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355893"
---
# <a name="introduction-to-azure-managed-disks"></a>Общие сведения об управляемых дисках Azure

Управляемые диски Azure — это тома хранилища на уровне блоков, управляемые Azure и используемые с виртуальными машинами Azure. Управляемые диски подобны физическому диску на локальном сервере, но при этом они виртуализированные. Все, что вам необходимо сделать, — это указать размер и тип управляемых дисков, а также выполнить их подготовку. После подготовки Azure выполнит остальную часть работы.

Доступные типы дисков: диски ценовой категории "Ультра", твердотельные накопители (SSD) ценовой категории "Премиум", диски SSD и жесткие диски (HDD) ценовой категории "Стандартный". Дополнительные сведения о типе каждого отдельного диска см. в статье, посвященной [выбору типа диска для виртуальных машин IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Выбор типа диска для виртуальных машин IaaS](disks-types.md)