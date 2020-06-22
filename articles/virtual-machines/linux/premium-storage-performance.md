---
title: Хранилище Azure класса Premium. Разработка высокопроизводительных приложений на виртуальных машинах Linux | Документация Майкрософт
description: Разработка высокопроизводительных приложений с использованием управляемых дисков SSD (цен. категория "Премиум"). Хранилище Premium обеспечивает поддержку дисков с высокой производительностью и малой задержкой для интенсивных рабочих нагрузок ввода-вывода на виртуальных машинах Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 91968a9701c46114d56b30ec6e3a93cfa76bf023
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022169"
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
