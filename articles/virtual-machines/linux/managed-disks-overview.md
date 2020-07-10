---
title: Общие сведения о хранилище дисков
description: Обзор управляемых дисков Azure, которые обрабатывают учетные записи хранения при использовании виртуальных машин.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d3932047422571399ad0562b2f9f2d19c3e6799b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145304"
---
# <a name="introduction-to-azure-managed-disks"></a>Общие сведения об управляемых дисках Azure

Управляемые диски Azure — это тома хранилища на уровне блоков, управляемые Azure и используемые с виртуальными машинами Azure. Управляемые диски подобны физическому диску на локальном сервере, но при этом они виртуализированные. Все, что вам необходимо сделать, — это указать размер и тип управляемых дисков, а также выполнить их подготовку. После подготовки Azure выполнит остальную часть работы.

Доступные типы дисков: диски ценовой категории "Ультра", твердотельные накопители (SSD) ценовой категории "Премиум", диски SSD и жесткие диски (HDD) ценовой категории "Стандартный". Дополнительные сведения о типе каждого отдельного диска см. в статье, посвященной [выбору типа диска для виртуальных машин IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Выбор типа диска для виртуальных машин IaaS](disks-types.md)