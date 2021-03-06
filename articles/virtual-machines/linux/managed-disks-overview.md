---
title: Обзор управляемого диска Хранилища дисков Azure для виртуальных машин на платформе Linux
description: Обзор управляемых дисков Azure, которые отвечают за управление учетными записями хранения, используемыми для виртуальных машин Linux.
author: roygara
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 11/06/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7e9a5e1b1e1b0c879a1264573e7073e70a1e95fc
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035865"
---
# <a name="introduction-to-azure-managed-disks"></a>Общие сведения об управляемых дисках Azure

Управляемый диск Azure — это виртуальный жесткий диск (VHD). Его можно представить как физический диск на локальном сервере, но который является виртуализированным. Управляемые диски Azure хранятся в виде страничных BLOB-объектов, которые являются произвольным объектом хранения ввода-вывода в Azure. Эти диски называются управляемыми, так как это абстракция на основе страничных BLOB-объектов, а также контейнеров больших двоичных объектов и учетных записей хранения Azure. В случае с управляемыми дисками вам нужно только подготовить диск к работе, а Azure позаботится об остальном.

Если вы решите использовать управляемые диски Azure с вашими рабочими нагрузками, Azure создаст диск и будет управлять им. Доступные типы дисков: диски SSD (цен. категория "Ультра"), SSD (цен. категория "Премиум"), SSD (цен. категория "Стандартный") и жесткие диски (HDD) (цен. категория "Стандартный"). Дополнительные сведения о типе каждого отдельного диска см. в статье, посвященной [выбору типа диска для виртуальных машин IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Выбор типа диска для виртуальных машин IaaS](disks-types.md)
