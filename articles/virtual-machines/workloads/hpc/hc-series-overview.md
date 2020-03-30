---
title: Обзор VM серии HC - Виртуальные машины Azure Документы Майкрософт
description: Узнайте о предварительной поддержке vM-размера серии HC в Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: a4cd74c9c85ee7413cde9f0fb4cf3ffb54c9b3d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906741"
---
# <a name="hc-series-virtual-machine-overview"></a>HC-серии виртуальный обзор машины

Для максимальной производительности приложений СПК на процессорах Intel Xeon Scalable требуется продуманный подход к размещению процессов на этой новой архитектуре. Здесь мы планируем реализовать его на VMs-ми-классах серии Azure HC для приложений HPC. Мы будем использовать термин "pNUMA" для обозначения физического домена NUMA и "vNUMA" для обозначения виртуализированного домена NUMA. Аналогичным образом, мы будем использовать термин "pCore" для обозначения физических ядер процессора, и "vCore" для обозначения виртуализированных ядер процессора.

Физически, HC-сервер 2 и 24-ядерный процессор Intel Xeon Platinum 8168 в общей сложности 48 физических ядер. Каждый процессор является одним доменом pNUMA и имеет единый доступ к шести каналам DRAM. Процессоры Intel Xeon Platinum имеют 4x больше l2 кэша, чем в предыдущих поколениях (256 КБ/> 1 МБ/ядро), а также сокращение кэша L3 по сравнению с предыдущими процессорами Intel (2,5 МБ/ядра -> 1,375 МБ/ядро).

Вышетопология переносится и на конфигурацию гипервизора серии HC. Чтобы обеспечить пространство для работы гипервизора Azure, не мешая VM, мы оставляем pCores 0-1 и 24-25 (то есть первые 2 pCores на каждой розетке). Затем мы присваиваем доменам pNUMA все оставшиеся ядра для VM. Таким образом, ВМ увидит:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`сердечники на ВМ

ВМ не знает, что pCores 0-1 и 24-25 не были даны ему. Таким образом, он разоблачает каждый vNUMA, как если бы он родной было 22 ядер.

Процессоры Intel Xeon Platinum, Gold и Silver также представляют 2D-сеть 2D-сеток для связи внутри и снаружи в розетку процессора. Мы настоятельно рекомендуем процесс привязки для оптимальной производительности и согласованности. Процесс прикрепления будет работать на HC-серии VMs, потому что основной кремний подвергается как-это для гостя VM. Чтобы узнать больше, смотрите [архитектуру Intel Xeon SP.](https://bit.ly/2RCYkiE)

На следующей диаграмме показано сегрегацию ядер, зарезервированных для Azure Hypervisor и HC-серии VM.

![Сегрегация ядер, зарезервированных для Azure Hypervisor и HC-серии VM](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Характеристики оборудования

| Спецификации оборудования          | HC-серия VM                     |
|----------------------------------|----------------------------------|
| Ядра                            | 44 (отключен ат-ХТ)                 |
| ЦП                              | Intel Xeon Platinum 8168        |
| Частота процессора (не AVX)          | 3,7 ГГц (одноядро), 2,7-3,4 ГГц (все ядра) |
| Память                           | 8 ГБ/ядро (всего 352)            |
| Локальный диск                       | 700 ГБ NVMe                      |
| Infiniband;                       | 100 Гб EDR Mellanox ConnectX-5 |
| Сеть                          | 50 Gb Ethernet (40 Gb, пригодный для удобого) Azure второй Gen SmartNIC |

## <a name="software-specifications"></a>Спецификации программного обеспечения

| Спецификации программного обеспечения     | HC-серия VM          |
|-----------------------------|-----------------------|
| Максимальный размер mpI вакансии            | 13200 ядер (300 ВМ в одном VMSS с singlePlacementGroup'true) |
| Поддержка MPI                 | MVAPICH2, OpenMPI, MPICH, Платформа MPI, Intel MPI  |
| Дополнительные рамки       | Единая коммуникация X, libfabric, PGAS |
| Поддержка хранения azure       | Стд и Премиум (максимум 4 диска) |
| Поддержка ОС для SRIOV RDMA   | CentOS/RHEL 7.6, SLES 12 SP4, WinServer 2016 |
| Поддержка Azure CycleCloud    | Да                         |
| Поддержка пакетов Azure         | Да                         |

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше о размерах HPC VM для [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) и [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) в Azure.

* Узнайте больше о [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) в Azure.
