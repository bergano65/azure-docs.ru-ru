---
title: Серия NP-Series — виртуальные машины Azure
description: Спецификации виртуальных машин серии NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 2606881cec5527084c3c2788d40f8bdfd8abf5a3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100418042"
---
# <a name="np-series-preview"></a>Серии NP-Series (Предварительная версия)

Виртуальные машины серии NP работают на базе [Ксилинкс U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGAs для ускорения рабочих нагрузок, включая определение машинного обучения, кодирование видео и анализ базы данных & Analytics. Виртуальные машины серии NP также работают на базе процессоров Intel Xeon 8171M (Skylake) со всеми основными тактами скорости Turbo 3,2 ГГц.


[Хранилище класса Premium](premium-storage-performance.md): поддерживается<br>
[Кэширование хранилища класса Premium](premium-storage-performance.md): поддерживается<br>
[Динамическая миграция](maintenance-and-updates.md): не поддерживается<br>
[Обновления с сохранением памяти](maintenance-and-updates.md): не поддерживается<br>
Поддержка создания виртуальных машин: поколение 1<br>
[Ускоренная сеть](../virtual-network/create-vm-accelerated-networking-cli.md): поддерживается<br>
[Временные диски ОС](ephemeral-os-disks.md): не поддерживаются <br>
<br>

| Размер | vCPU | Память: ГиБ | Временное хранилище (SSD): ГиБ | ППВМ | Память FPGA: гиб | Максимальное число дисков данных | Максимальное число сетевых карт/ожидаемая пропускная способность сети (Мбит/с) | 
|---|---|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы
Ознакомьтесь с [заметками о выпуске среды выполнения ксилинкс (КСРТ)](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf) , чтобы получить полный список поддерживаемых операционных систем.

Во время предварительной версии программы Microsoft Azure инженерные группы будут использовать конкретные инструкции по установке драйверов.

## <a name="other-sizes"></a>Остальные размеры

- [Универсальные](sizes-general.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [Оптимизированные для GPU](sizes-gpu.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.
