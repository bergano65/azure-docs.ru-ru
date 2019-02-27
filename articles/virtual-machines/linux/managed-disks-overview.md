---
title: Обзор управляемого диска Хранилища дисков Azure для виртуальных машин на платформе Linux | Документация Майкрософт
description: Обзор управляемых дисков Azure, которые отвечают за управление учетными записями хранения, используемыми для виртуальных машин Linux.
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/11/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d8394121019338b53c87f7229d0ccf52fbdf8f21
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327033"
---
# <a name="introduction-to-azure-managed-disks"></a>Общие сведения об управляемых дисках Azure

Управляемый диск Azure — это виртуальный жесткий диск (VHD). Его можно представить как физический диск на локальном сервере, но который является виртуализированным. Управляемые диски Azure хранятся в виде страничных BLOB-объектов, которые являются произвольным объектом хранения ввода-вывода в Azure. Эти диски называются управляемыми, так как это абстракция на основе страничных BLOB-объектов, а также контейнеров больших двоичных объектов и учетных записей хранения Azure. В случае с управляемыми дисками вам нужно только подготовить диск к работе, а Azure позаботится об остальном.

Если вы решите использовать управляемые диски Azure с вашими рабочими нагрузками, Azure создаст диск и будет управлять им. Доступные типы дисков: диски SSD (цен. категория "Ультра") в предварительной версии, SSD (цен. категория "Премиум"), SSD (цен. категория "Стандартный") и жесткие диски (HDD) (цен. категория "Стандартный"). Дополнительные сведения о типе каждого отдельного диска см. в статье, посвященной [выбору типа диска для виртуальных машин IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Выбор типа диска для виртуальных машин IaaS](disks-types.md)
