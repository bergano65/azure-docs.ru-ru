---
title: Производительность размера виртуальной машины серии HC
description: Узнайте о результатах тестирования производительности для размеров виртуальных машин серии HC в Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/10/2020
ms.author: amverma
ms.openlocfilehash: 0d63d9770dacf6a200e8b81e8d47d9f807a8a448
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603450"
---
# <a name="hc-series-virtual-machine-sizes"></a>Размеры виртуальных машин серии HC

На размерах серии HC было запущено несколько тестов производительности. Ниже приведены некоторые результаты этого тестирования производительности.

| Рабочая нагрузка                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM триады                                    | 190 ГБ/с (Intel MLC AVX-512)  |
| Высокопроизводительная Linpack (ХПЛ)                  | 3520 гигафлопс (Рпеак), 2970 гигафлопс (Рмакс) |
| Задержка & RDMA, пропускная способность                        | 1,05 микросекунд, 96,8 ГБ/с   |
| FIO на локальном SSD NVMe                           | 1,3 ГБ/с, операций записи 900 МБ/с |  
| ИОР на 4 SSD (цен. категория "Премиум") Azure (управляемые диски P30, конфигурацию RAID0) * *  | 780 МБ/с, 780 МБ/операций записи |

## <a name="mpi-latency"></a>Задержка MPI

Выполняется тестирование задержки MPI из набора микротестирования осу. Примеры сценариев в [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hc.png" alt-text="MPI с задержкой в Azure HC.":::

## <a name="mpi-bandwidth"></a>Пропускная способность MPI

Выполняется тестирование пропускной способности MPI из набора микротестирования осу. Примеры сценариев в [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hc.png" alt-text="Пропускная способность MPI в Azure HC.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

[Пакет Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) имеет много тестов InfiniBand, таких как задержка (ib_send_lat) и пропускная способность (ib_send_bw). Ниже приведен пример команды.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с последними объявлениями и некоторыми примерами высокопроизводительных вычислений (HPC) и результатами в [блогах сообщества разработчиков Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Более высокоуровневое архитектурное представление выполнения рабочих нагрузок HPC см. в статье [высокопроизводительные вычисления (HPC) в Azure](/azure/architecture/topics/high-performance-computing/).
