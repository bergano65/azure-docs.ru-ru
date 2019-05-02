---
title: Обзор управляемого диска Хранилища дисков Azure для виртуальных машин на платформе Windows | Документация Майкрософт
description: Обзор управляемых дисков Azure, которые отвечают за управление учетными записями хранения, используемыми для виртуальных машин Azure на платформе Windows
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.workload: storage
ms.tgt_pltfrm: vm-windows
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: fb1ee8556935b141dfee6a18c96ecafb476aa584
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725812"
---
# <a name="introduction-to-azure-managed-disks"></a>Общие сведения об управляемых дисках Azure

Управляемый диск Azure — это виртуальный жесткий диск (VHD). Его можно представить как физический диск на локальном сервере, но который является виртуализированным. Управляемые диски Azure хранятся в виде страничных BLOB-объектов, которые являются произвольным объектом хранения ввода-вывода в Azure. Эти диски называются управляемыми, так как это абстракция на основе страничных BLOB-объектов, а также контейнеров больших двоичных объектов и учетных записей хранения Azure. В случае с управляемыми дисками вам нужно только подготовить диск к работе, а Azure позаботится об остальном.

Если вы решите использовать управляемые диски Azure с вашими рабочими нагрузками, Azure создаст диск и будет управлять им. Доступные типы дисков — это диски SSD (цен. категория "Ультра") (предварительная версия), SSD (цен. категория "Премиум"), SSD (цен. категория "Стандартный") и жесткие диски (HDD) (цен. категория "Стандартный"). Дополнительные сведения о типе каждого отдельного диска см. в статье, посвященной [выбору типа диска для виртуальных машин IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Выбор типа диска для виртуальных машин IaaS](disks-types.md)