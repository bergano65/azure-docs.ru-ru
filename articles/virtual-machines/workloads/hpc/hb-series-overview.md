---
title: Обзор VM-серии HB - Виртуальные машины Azure Документы Майкрософт
description: Узнайте о предварительной поддержке размера VM серии HB в Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707772"
---
# <a name="hb-series-virtual-machines-overview"></a>Обзор виртуальных машин серии HB

Для максимальной производительности высокопроизводительных вычислений (HPC) приложения на AMD EPYC требуется продуманный подход к локальности памяти и размещению процессов. Ниже мы описываем архитектуру AMD EPYC и нашу реализацию на Azure для приложений СПК. Мы будем использовать термин "pNUMA" для обозначения физического домена NUMA и "vNUMA" для обозначения виртуализированного домена NUMA.

Физически, HB-серия составляет 2 и 32-ядерный процессор EPYC 7551 в общей сложности 64 физических ядер. Эти 64 ядра разделены на 16 доменов pNUMA (8 на розетку), каждый из которых состоит из четырех ядер и известен как "Комплекс процессоров" (или "CCX"). Каждый CCX имеет свой собственный кэш L3, который является, как ОС будет видеть pNUMA/vNUMA границы. Пара соседних CCXs имеет доступ к двум каналам физического DRAM (32 ГБ DRAM в серверах серии HB).

Чтобы обеспечить работу гипервизора Azure, не мешая VM, мы оставляем за собой физический домен pNUMA 0 (первый CCX). Затем мы назначаем домены pNUMA 1-15 (остальные единицы CCX) для VM. ВМ увидите:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`сердечники на ВМ

ВМ, сам, не знает, что pNUMA 0 не было дано ему. VM понимает pNUMA 1-15 как vNUMA 0-14, с 7 vNUMA на vSocket 0 и 8 vNUMA на vSocket 1. Хотя это асимметрично, ваша ОС должна загружаться и работать нормально. Позже в этом руководстве мы проинструктируем, как лучше всего запускать mpI-приложения на этом асимметричном макете NUMA.

Процесс прикрепления будет работать на HB-серии VMs, потому что мы подвергаем основной кремния как есть для гостя VM. Мы настоятельно рекомендуем процесс привязки для оптимальной производительности и согласованности.

Подробнее об [архитектуре AMD EPYC](https://bit.ly/2Epv3kC) и [архитектуре с несколькими чипами](https://bit.ly/2GpQIMb) можно на LinkedIn. Для получения более [подробной](https://bit.ly/2T3AWZ9)информации см.

На следующей диаграмме показано сегрегацию ядер, зарезервированных для Azure Hypervisor и HB-серии VM.

![Сегрегация ядер, зарезервированных для Azure Hypervisor и HB-серии VM](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Характеристики оборудования

| Спецификации HW                | HB-серия VM                     |
|----------------------------------|----------------------------------|
| Ядра                            | 60 (SMT отключен)                |
| ЦП                              | AMD EPYC 7551                   |
| Частота процессора (не AVX)          | 2,55 ГГц (одиночные и все ядра)   |
| Память                           | 4 ГБ/ядро (всего 240)            |
| Локальный диск                       | 700 ГБ NVMe                      |
| Infiniband;                       | 100 Гб EDR Mellanox ConnectX-5 |
| Сеть                          | 50 Gb Ethernet (40 Gb, пригодный для удобого) Azure второй Gen SmartNIC |

## <a name="software-specifications"></a>Спецификации программного обеспечения

| Спецификации SW           |HB-серия VM           |
|-----------------------------|-----------------------|
| Максимальный размер mpI вакансии            | 6000 ядер (100 виртуальных наборов машин) 12000 ядер (200 виртуальных наборов машин)  |
| Поддержка MPI                 | MVAPICH2, OpenMPI, MPICH, Платформа MPI, Intel MPI  |
| Дополнительные рамки       | Единая коммуникация X, libfabric, PGAS |
| Поддержка хранения azure       | Стд и Премиум (максимум 4 диска) |
| Поддержка ОС для SRIOV RDMA   | CentOS/RHEL 7.6, SLES 12 SP4, WinServer 2016  |
| Поддержка Azure CycleCloud    | Да                         |
| Поддержка пакетов Azure         | Да                         |

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше о размерах HPC VM для [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) и [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) в Azure.

* Узнайте больше о [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) в Azure.
