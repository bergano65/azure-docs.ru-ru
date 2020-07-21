---
title: Хранилище Azure класса Premium — проектирование для обеспечения высокой производительности
description: Разработка высокопроизводительных приложений с использованием управляемых дисков SSD (цен. категория "Премиум"). Хранилище Premium обеспечивает поддержку дисков с высокой производительностью и малой задержкой для интенсивных рабочих нагрузок ввода-вывода на виртуальных машинах Azure.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 46c917a2d27f5efaa1e902db0d5da5375578b8b8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526047"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Хранилище Azure класса "Премиум": разработка, обеспечивающая повышенную производительность
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Иногда проблема, которая выглядит как недостаточная производительность дисков, на самом деле вызвана узким местом в сети. В таких ситуациях следует улучшить [производительность сети](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Если вы хотите протестировать производительность диска, см. статью [Тестирование производительности диска](disks-benchmarks.md).
>
> Если виртуальная машина поддерживает ускоренную сеть, необходимо включить эту функцию. Если она не включена, ее можно включить на уже развернутых виртуальных машинах под управлением [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) и [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Если вы не знакомы с хранилищем класса Premium, перед началом работы ознакомьтесь со статьями [Какие типы дисков доступны в Azure?](disks-types.md) и [Целевые показатели масштабируемости и производительности для учетных записей хранилища BLOB-объектов класса Premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Если вы хотите протестировать производительность диска, см. статью [Тестирование производительности диска](disks-benchmarks.md).

См. дополнительные сведения о доступных типах дисков: [Выбор типа диска](disks-types.md)  

Если вы пользователь SQL Server, см. статьи с рекомендациями по оптимизации производительности SQL Server:

* [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [Хранилище Azure класса «Премиум» обеспечивает максимальную производительность SQL Server в виртуальных машинах Azure](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)
