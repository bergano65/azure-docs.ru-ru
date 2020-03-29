---
title: 'Хранилище Azure уровня "Премиум": проектирование для обеспечения высокой производительности виртуальных машин Windows | Документация Майкрософт'
description: Разрабатываем высокопроизводительные приложения с использованием управляемых дисков ССР премиум-класса Azure. Хранилище Premium обеспечивает поддержку дисков с высокой производительностью и малой задержкой для интенсивных рабочих нагрузок ввода-вывода на виртуальных машинах Azure.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 12fb94bb4f98bde5c70343f18762cefe1ab120f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371345"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Хранилище Azure класса "Премиум": разработка, обеспечивающая повышенную производительность
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Иногда проблема, которая выглядит как недостаточная производительность дисков, на самом деле вызвана узким местом в сети. В таких ситуациях следует улучшить [производительность сети](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Если вы хотите, чтобы тест вашего диска, смотрите нашу статью о [бенчмаркинг диска](disks-benchmarks.md).
>
> Если виртуальная машина поддерживает ускоренную сеть, необходимо включить эту функцию. Если она не включена, ее можно включить на уже развернутых виртуальных машинах под управлением [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) и [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Перед тем, как начать работу, если вы новичок в Premium Storage, сначала прочитайте [тип диска Azure для IaaS VMs](disks-types.md) и [целевые показатели масштабируемости для учетных записей премиум-страницы.](../../storage/blobs/scalability-targets-premium-page-blobs.md)

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Если вы хотите, чтобы тест вашего диска, смотрите нашу статью о [бенчмаркинг диска](disks-benchmarks.md).

Подробнее об доступных типах дисков: [Выберите тип диска](disks-types.md)  

Если вы пользователь SQL Server, см. статьи с рекомендациями по оптимизации производительности SQL Server:

* [Рекомендации по производительности для сервера S'L в виртуальных машинах Azure](sql/virtual-machines-windows-sql-performance.md)
* [Хранилище Azure класса «Премиум» обеспечивает максимальную производительность SQL Server в виртуальных машинах Azure](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)