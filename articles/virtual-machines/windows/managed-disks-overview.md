---
title: Обзор Хранилища дисков Azure
description: Обзор управляемых дисков Azure, отвечающих за управление учетными записями хранения, которые используются для виртуальных машин Azure.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4b3a66ce00582a3ef05e77f65acdc46fbde8ce72
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82148081"
---
# <a name="introduction-to-azure-managed-disks"></a>Общие сведения об управляемых дисках Azure

Управляемые диски Azure — это тома хранилища на уровне блоков, управляемые Azure и используемые с виртуальными машинами Azure. Управляемые диски подобны физическому диску на локальном сервере, но при этом они виртуализированы. Все, что вам необходимо сделать, — это указать размер и тип управляемых дисков, а также выполнить их подготовку. После подготовки Azure выполнит остальную часть работы.

Доступные типы дисков: диски ценовой категории "Ультра", твердотельные накопители (SSD) ценовой категории "Премиум", диски SSD и жесткие диски (HDD) ценовой категории "Стандартный". Дополнительные сведения о типе каждого отдельного диска см. в статье, посвященной [выбору типа диска для виртуальных машин IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Выбор типа диска для виртуальных машин IaaS](disks-types.md)
