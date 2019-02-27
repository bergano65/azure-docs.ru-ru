---
title: Обзор управляемого диска Хранилища дисков Azure для виртуальных машин на платформе Windows | Документация Майкрософт
description: Обзор управляемых дисков Azure, которые отвечают за управление учетными записями хранения, используемыми для виртуальных машин Azure на платформе Windows
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.workload: storage
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/11/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 85b2dcb73024ce786b78436b7070ad2e9a96e1d4
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328476"
---
# <a name="introduction-to-azure-managed-disks"></a>Общие сведения об управляемых дисках Azure

Управляемый диск Azure — это виртуальный жесткий диск (VHD). Его можно представить как физический диск на локальном сервере, но который является виртуализированным. Управляемые диски Azure хранятся в виде страничных BLOB-объектов, которые являются произвольным объектом хранения ввода-вывода в Azure. Эти диски называются управляемыми, так как это абстракция на основе страничных BLOB-объектов, а также контейнеров больших двоичных объектов и учетных записей хранения Azure. В случае с управляемыми дисками вам нужно только подготовить диск к работе, а Azure позаботится об остальном.

Если вы решите использовать управляемые диски Azure с вашими рабочими нагрузками, Azure создаст диск и будет управлять им. Доступные типы дисков — это диски SSD (цен. категория "Ультра") (предварительная версия), SSD (цен. категория "Премиум"), SSD (цен. категория "Стандартный") и жесткие диски (HDD) (цен. категория "Стандартный"). Дополнительные сведения о типе каждого отдельного диска см. в статье, посвященной [выбору типа диска для виртуальных машин IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Выбор типа диска для виртуальных машин IaaS](disks-types.md)