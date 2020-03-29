---
title: Размеры Azure VM - Хранение (ru) Документы Майкрософт
description: Перечисляет различные оптимизированные размеры хранилища, доступные для виртуальных машин в Azure. Перечисляет информацию о количестве vCPUs, дисков данных и NICs, а также пропускную способность хранилища и пропускной способности сети для размеров в этой серии.
services: virtual-machines
documentationcenter: ''
author: sasha-melamed
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 5a611dc288fc18a14f6000689f9f9b49d4b3feb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913342"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Размеры виртуальных машин, оптимизированных для операций в хранилище

Оптимизированные размеры VM-хранилища обеспечивают высокую пропускную память и иск-истоговый объем данных и идеально подходят для баз данных больших данных, Баз данных, баз данных, данных, складирования данных и больших транзакционных баз данных.  Примеры включают Кассандра, MongoDB, Cloudera, и Redis. В этой статье содержится информация о количестве vCPUs, дисков данных и NICs, а также локальной пропускной способности и пропускной способности сети для каждого оптимизированного размера.

[Lsv2-серия](lsv2-series.md) имеет высокую пропускную силу, низкую задержку, непосредственно отображаются локальные NVMe хранения работает на [процессоре AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) со всеми ядра импульс 2,55 ГГц и максимальный импульс 3,0 ГГц. Виртуальные машины серии Lsv2 могут иметь размеры от 8 до 80 виртуальных ЦП в одновременных многопоточных конфигурациях.  Отводится 8 ГиБ памяти на виртуальный ЦП, а также одно устройство NVMe SSD M.2 объемом 1,92 ТБ на 8 виртуальных ЦП, до 19,2 ТБ (10 x 1,92 ТБ) на L80s v2.

## <a name="other-sizes"></a>Остальные размеры

- [Общее назначение](sizes-general.md)
- [Оптимизированные для вычислений](sizes-compute.md)
- [Оптимизирована память](sizes-memory.md)
- [Оптимизированные для GPU](sizes-gpu.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.

Узнайте, как оптимизировать производительность виртуальных машин серии Lsv2 для [Windows](windows/storage-performance.md) или [Linux.](linux/storage-performance.md)
