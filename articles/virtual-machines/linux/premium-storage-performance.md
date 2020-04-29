---
title: 'Хранилище Azure класса Premium: проектирование производительности на виртуальных машинах Linux | Документация Майкрософт'
description: Разработка высокопроизводительных приложений с помощью управляемых дисков SSD Azure класса Premium. Хранилище Premium обеспечивает поддержку дисков с высокой производительностью и малой задержкой для интенсивных рабочих нагрузок ввода-вывода на виртуальных машинах Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9940ee4cfce9721ac65f2b3cf1469e180adfa098
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79267148"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Хранилище Azure класса "Премиум": разработка, обеспечивающая повышенную производительность
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Иногда проблема, которая выглядит как недостаточная производительность дисков, на самом деле вызвана узким местом в сети. В таких ситуациях следует улучшить [производительность сети](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Если вы хотите протестировать производительность диска, см. статью о [тестировании диска](disks-benchmarks.md).
>
> Если виртуальная машина поддерживает ускоренную сеть, необходимо включить эту функцию. Если она не включена, ее можно включить на уже развернутых виртуальных машинах под управлением [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) и [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Прежде чем начать, если вы не знакомы с хранилищем класса Premium, сначала прочитайте поле [Выбор типа диска Azure для виртуальных машин IaaS](disks-types.md) и [целевых показателей масштабируемости для учетных записей хранилища BLOB-объектов класса Premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Если вы хотите протестировать производительность диска, см. статью о [тестировании диска](disks-benchmarks.md).

Дополнительные сведения о доступных типах дисков: [Выбор типа диска](disks-types.md)  

Если вы пользователь SQL Server, см. статьи с рекомендациями по оптимизации производительности SQL Server:

* [Рекомендации по повышению производительности SQL Server на виртуальных машинах Azure](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Хранилище Azure класса «Премиум» обеспечивает максимальную производительность SQL Server в виртуальных машинах Azure](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)