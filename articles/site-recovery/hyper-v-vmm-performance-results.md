---
title: Результаты теста репликации виртуальных машин Hyper-V в облаках VMM на дополнительный сайт с помощью службы Azure Site Recovery | Документация Майкрософт
description: В этой статье содержатся сведения о тестировании производительности для репликации виртуальных машин Hyper-V на дополнительный сайт с помощью Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: a7413b2dcb24a42092eb2af9816b1d29a8306e19
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377223"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Результаты теста репликации виртуальных машин Hyper-V в облаках VMM на дополнительный сайт


В этой статье содержатся результаты тестирования производительности при репликации виртуальных машин Hyper-V в облаках System Center Virtual Machine Manager (VMM) в дополнительный центр обработки данных.

## <a name="test-goals"></a>Цели тестирования

Цель тестирования — проверить, как ведет себя Site Recovery при репликации устойчивого состояния.

- Репликация устойчивого состояния происходит, когда виртуальные машины завершили начальную репликацию и выполняют синхронизацию небольших изменений.
- Производительность важно измерять с помощью устойчивого состояния, так как это как раз то состояние, в котором большинство виртуальных машин остается до тех пор, пока не возникает непредвиденный простой.
- Тестовое развертывание включает два локальных сайта, на каждом из которых имеется сервер VMM. Такое тестовое развертывание является типичным для развертывания головного офиса и филиала, в котором первый выступает в качестве основного сайта, а филиал — как дополнительный сайт или сайт восстановления.

## <a name="what-we-did"></a>Результат

Вот что мы сделали в ходе теста:

1. Создали виртуальные машины, использующие шаблоны VMM.
2. Запустили виртуальные машины и отслеживали базовые показатели производительности каждые 12 часов.
3. Создали облака на основном сервере VMM и сервере восстановления VMM.
4. Настроили защиту облака в Azure Site Recovery, включая сопоставление исходного облака и облака восстановления.
5. Включили защиту для виртуальных машин и предоставили им разрешения на выполнение начальной репликации.
6. Ожидание стабилизации системы в течение нескольких часов.
7. Собрали метрики производительности за каждые 12 часов, чтобы проверить, что в течение этого времени все виртуальные машины остаются в ожидаемом состоянии репликации.
8. Определили разницу между базовыми метриками производительности и метриками производительности репликации.


## <a name="primary-server-performance"></a>Производительность основного сервера

* Реплика Hyper-V (используемая Site Recovery) асинхронно отслеживает изменения в файле журнала с минимальным задействованием ресурсов хранилища на основном сервере.
* Реплика Hyper-V использует самоподдерживающий кэш памяти, чтобы свести к минимуму временные затраты на выполнение операций ввода-вывода в секунду для отслеживания. Она сохраняет операции записи в VHDX в памяти и записывает их в файл журнала перед отправкой журнала на сайт восстановления. Окончательная запись данных на диск также происходит в случае, если операции записи сталкиваются с предопределенным ограничением.
* На диаграмме ниже показаны временные затраты на выполнение операций ввода-вывода в секунду для репликации. Мы видим, что временные затраты на выполнение операций ввода-вывода в секунду для репликации составляют приблизительно 5%, что довольно мало.

  ![Основные результаты](./media/hyper-v-vmm-performance-results/IC744913.png)

Реплика Hyper-V использует память на основном сервере для оптимизации производительности диска. Как показано на следующей диаграмме, временные затраты на использование памяти на всех серверах в основном кластере являются граничными. Временные затраты на использование памяти представлены в виде процента памяти, используемого репликацией, по отношению к общему объему памяти, который имеется на сервере Hyper-V.

![Основные результаты](./media/hyper-v-vmm-performance-results/IC744914.png)

Временные затраты на использование ресурсов ЦП репликой Hyper-V являются минимальными. Как показано на диаграмме ниже, временные затраты на репликацию находятся в диапазоне 2–3 %.

![Основные результаты](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Производительность сервера-получателя

Реплика Hyper-V использует небольшой объем памяти на сервере восстановления, чтобы оптимизировать число операций сохранения. На диаграмме ниже представлены обобщенные данные об использовании памяти на сервере восстановления. Временные затраты на использование памяти представлены в виде процента памяти, используемого репликацией, по отношению к общему объему памяти, который имеется на сервере Hyper-V.

![Дополнительные результаты](./media/hyper-v-vmm-performance-results/IC744916.png)

Объем операций ввода-вывода на сайте восстановления зависит от количества операций записи на основном сайте. Рассмотрим общее количество операций ввода-вывода на сайте восстановления по сравнению с общим количеством операций ввода-вывода и операций записи на основном сайте. На диаграмме показано, что итоговое количество операций ввода-вывода в секунду на сайте восстановления:

* приблизительно в 1,5 раза больше количества операций записи на основном сайте;
* составляет приблизительно 37 % от общего количества операций ввода-вывода в секунду на основном сайте.

![Дополнительные результаты](./media/hyper-v-vmm-performance-results/IC744917.png)

![Дополнительные результаты](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Влияние на использование сети

Пропускная способность сети между основным узлом и узлом восстановления в среднем составила 275 МБ/с (с включенным сжатием данных) по отношению к имеющейся пропускной способности сети в 5 ГБ/с.

![Результаты по использованию сети](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Влияние на производительность виртуальной машины

Важным аспектом является влияние репликации на выполнение рабочей нагрузки, назначенной виртуальным машинам. Если основной сайт правильно подготовлен для репликации, то не должно быть никакого влияния на рабочую нагрузку. Упрощенный механизм отслеживания реплики Hyper-V гарантирует, что рабочие нагрузки, выполняющиеся на виртуальных машинах, не будут затронуты репликацией устойчивого состояния. Это как раз иллюстрируется на следующих диаграммах.

На этой диаграмме показаны операции ввода-вывода в секунду, выполняемые виртуальными машинами с разными рабочими нагрузками до и после включения репликации. Можно заметить, что никаких различий между ними не наблюдается.

![Результаты по воздействию реплики](./media/hyper-v-vmm-performance-results/IC744920.png)

На следующей диаграмме показана пропускная способность виртуальных машин, на которых выполняются разные рабочие нагрузки до и после включения репликации. Можно видеть, что репликация не оказывает существенного влияния.

![Воздействие реплики, результаты](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Заключение

Результаты ясно показывают, что масштабирование Site Recovery в сочетании с репликой Hyper-V выполняется с минимальными временными затратами для большого кластера. Site Recovery обеспечивает простоту развертывания, репликации, управления и мониторинга. Реплика Hyper-V предоставляет необходимую инфраструктуру для успешного масштабирования репликации. 

## <a name="test-environment-details"></a>Сведения о тестовой среде

### <a name="primary-site"></a>Первичный сайт

* Основной сайт включает кластер, содержащий пять серверов Hyper-V, на которых выполняются 470 виртуальных машин.
* Виртуальные машины выполняют разную рабочую нагрузку и на них на всех включена защита Site Recovery.
* Хранилище для узла кластера предоставляется сетью хранения данных iSCSI. Модель — Hitachi HUS130.
* Каждый сервер кластера оборудован четырьмя сетевыми картами (NIC) со скоростью передачи данных 1 Гбит/с.
* Две из которых подключены к частной сети iSCSI, а другие две — к внешней корпоративной сети. Одна из внешних сетей зарезервирована исключительно для кластерных взаимодействий.

![Основные требования к оборудованию](./media/hyper-v-vmm-performance-results/IC744922.png)

| Сервер | ОЗУ | Модель | Процессор | Количество процессоров | Сетевая карта | Программное обеспечение |
| --- | --- | --- | --- | --- | --- | --- |
| Серверы Hyper-V в кластере: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 имеет 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 ГГц |4 |1 Гбит/с х 4 |Windows Server Datacenter 2012 R2 (64-разрядная версия) + роль Hyper-V |
| Сервер VMM |2 | | |2 |1 Гбит/с |Windows Server Database 2012 R2 (64-разрядная версия) + VMM 2012 R2 |

### <a name="secondary-site"></a>Вторичный сайт

* Дополнительный сайт включает отказоустойчивый кластер с шестью узлами.
* Хранилище для узла кластера предоставляется сетью хранения данных iSCSI. Модель — Hitachi HUS130.

![Основные характеристики оборудования](./media/hyper-v-vmm-performance-results/IC744923.png)

| Сервер | ОЗУ | Модель | Процессор | Количество процессоров | Сетевая карта | Программное обеспечение |
| --- | --- | --- | --- | --- | --- | --- |
| Серверы Hyper-V в кластере: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2,30 ГГц |2 |1 Гбит/с х 4 |Windows Server Datacenter 2012 R2 (64-разрядная версия) + роль Hyper-V |
| ESTLAB HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 ГГц |4 | |Windows Server Datacenter 2012 R2 (64-разрядная версия) + роль Hyper-V |
| ESTLAB HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 ГГц |2 | |Windows Server Datacenter 2012 R2 (64-разрядная версия) + роль Hyper-V |
| Сервер VMM |2 | | |2 |1 Гбит/с |Windows Server Database 2012 R2 (64-разрядная версия) + VMM 2012 R2 |

### <a name="server-workloads"></a>Рабочие нагрузки сервера

* Для целей тестирования мы выбрали рабочие нагрузки, которые обычно используются в сценариях корпоративных клиентов.
* Мы используем [IOMeter](http://www.iometer.org) со сводной таблицей характеристик рабочей нагрузки для моделирования.
* Все профили IOMeter настроены на запись случайных байтов, чтобы смоделировать худшие варианты моделей записи для рабочих нагрузок.

| Рабочая нагрузка | Размер ввода-вывода (КБ) | % доступа | % чтения | Количество необработанных операций ввода-вывода | Модель операций ввода-вывода |
| --- | --- | --- | --- | --- | --- |
| Файл-сервер |4<br />8<br />16<br />32<br />64 |60 %<br />20%<br />5%<br />5%<br />10 % |80 %<br />80 %<br />80 %<br />80 %<br />80 % |8<br />8<br />8<br />8<br />8 |Все 100 % случайные |
| SQL Server (том 1)<br />SQL Server (том 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100 % случайные<br />100% последовательностей |
| Exchange |32 |100% |67 % |8 |100 % случайные |
| Рабочая станция/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |100 % случайные (в обоих случаях) |
| Файловый веб-сервер |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Все 75 % случайные |

### <a name="vm-configuration"></a>Конфигурация виртуальной машины

* 470 виртуальных машин в основном кластере.
* Все виртуальные машины с диском VHDX.
* В таблице представлены сводные сведения о виртуальных машинах, выполняющих рабочие нагрузки. Все они созданы с помощью шаблонов VMM.

| Рабочая нагрузка | Число виртуальных машин | Минимальный объем ОЗУ (ГБ) | Максимальный объем ОЗУ (ГБ) | Размер логического диска (ГБ) на виртуальную машину | Максимальное значение IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Файловый сервер |50 |1 |2 |552 |22 |
| VDI |149 |0,5 |1 |80 |6 |
| Веб-сервер |149 |0,5 |1 |80 |6 |
| ВСЕГО |470 | | |96,83 ТБ |4108 |

### <a name="site-recovery-settings"></a>Параметры Azure Site Recovery

* Site Recovery был настроен на защиту типа "локальная среда — локальная среда".
* На сервере VMM имеется четыре облака, в которых находятся серверы кластера Hyper-V и их виртуальные машины.

| Основное облако VMM | Защищенные виртуальные машины | Частота репликации | Дополнительные точки восстановления |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 мин |Отсутствуют |
| PrimaryCloudRpo30s |47 |30 с |Отсутствуют |
| PrimaryCloudRpo30sArp1 |47 |30 с |1 |
| PrimaryCloudRpo5m |235 |5 мин |Отсутствуют |

### <a name="performance-metrics"></a>Метрики производительности

В таблице ниже перечислены метрики производительности и счетчики, которые оценивались в развертывании.

| Метрика | Счетчик |
| --- | --- |
| ЦП |\Processor(_Total)\% Processor Time |
| Available memory |\Memory\Available MBytes |
| IOPS |\PhysicalDisk(_Total)\Disk Transfers/sec |
| VM read (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| VM write (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Write Operations/S |
| VM read throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Read Bytes/sec |
| VM write throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Write Bytes/sec |

## Next steps

[Set up replication](hyper-v-vmm-disaster-recovery.md)\Процессор (_общий объем ресурсов)\` загруженности процессораation of Hyper-V VMs in VMM clouds to a secondary site with Azure Site Recovery | Microsoft Docs
description: This article provides information about performance testing for replication of Hyper-V VMs in VMM clouds to a secondary site using Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi

---
# Test results for Hyper-V replication to a secondary site


This article provides the results of performance testing when replicating Hyper-V VMs in System Center Virtual Machine Manager (VMM) clouds, to a secondary datacenter.

## Test goals

The goal of testing was to examine how Site Recovery performs during steady state replication.

- Steady state replication occurs when VMs have completed initial replication, and are synchronizing delta changes.
- It’s important to measure performance using steady state, because it’s the state in which most VMs remain, unless unexpected outages occur.
- The test deployment consisted of two on-premises sites, with a VMM server in each site. This test deployment is typical of a head office/branch office deployment, with head office acting as the primary site, and the branch office as the secondary or recovery site.

## What we did

Here's what we did in the test pass:

1. Created VMs using VMM templates.
2. Started VMs, and captured baseline performance metrics over 12 hours.
3. Created clouds on the primary and recovery VMM servers.
4. Configured replication in Site Recovery, including mapping between source and recovery clouds.
5. Enabled protection for VMs, and allowed them to complete initial replication.
6. Waited a couple of hours for system stabilization.
7. Captured performance metrics over 12 hours, where all VMs remained in an expected replication state for those 12 hours.
8. Measured the delta between the baseline performance metrics, and the replication performance metrics.


## Primary server performance

* Hyper-V Replica (used by Site Recovery) asynchronously tracks changes to a log file, with minimum storage overhead on the primary server.
* Hyper-V Replica utilizes self-maintained memory cache to minimize IOPS overhead for tracking. It stores writes to the VHDX in memory, and flushes them into the log file before the time that the log is sent to the recovery site. A disk flush also happens if the writes hit a predetermined limit.
* The graph below shows the steady state IOPS overhead for replication. We can see that the IOPS overhead due to replication is around 5%, which is quite low.

  ![Primary results](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V Replica uses memory on the primary server, to optimize disk performance. As shown in the following graph, memory overhead on all servers in the primary cluster is marginal. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Primary results](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V Replica has minimum CPU overhead. As shown in the graph, replication overhead is in the range of 2-3%.

![Primary results](./media/hyper-v-vmm-performance-results/IC744915.png)

## Secondary server performance

Hyper-V Replica uses a small amount of memory on the recovery server, to optimize the number of storage operations. The graph summarizes the memory usage on the recovery server. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744916.png)

The amount of I/O operations on the recovery site is a function of the number of write operations on the primary site. Let’s look at the total I/O operations on the recovery site in comparison with the total I/O operations and write operations on the primary site. The graphs show that the total IOPS on the recovery site is

* Around 1.5 times the write IOPS on the primary.
* Around 37% of the total IOPS on the primary site.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744917.png)

![Secondary results](./media/hyper-v-vmm-performance-results/IC744918.png)

## Effect on network utilization

An average of 275 Mb per second of network bandwidth was used between the primary and recovery nodes (with compression enabled), against an existing bandwidth of 5 Gb per second.

![Results network utilization](./media/hyper-v-vmm-performance-results/IC744919.png)

## Effect on VM performance

An important consideration is the impact of replication on production workloads running on the virtual machines. If the primary site is adequately provisioned for replication, there shouldn’t be any impact on the workloads. Hyper-V Replica’s lightweight tracking mechanism ensures that workloads running in the virtual machines are not impacted during steady-state replication. This is illustrated in the following graphs.

This graph shows IOPS performed by virtual machines running different workloads, before and after replication was enabled. You can observe that there is no difference between the two.

![Replica effect results](./media/hyper-v-vmm-performance-results/IC744920.png)

The following graph shows the throughput of virtual machines running different workloads, before and after replication was enabled. You can observe that replication has no significant impact.

![Results replica effects](./media/hyper-v-vmm-performance-results/IC744921.png)

## Conclusion

The results clearly show that Site Recovery, coupled with Hyper-V Replica, scales well with minimum overhead for a large cluster. Site Recovery provides simple deployment, replication, management and monitoring. Hyper-V Replica provides the necessary infrastructure for successful replication scaling. 

## Test environment details

### Primary site

* The primary site has a cluster containing five Hyper-V servers, running 470 virtual machines.
* The VMs run different workloads, and all have Site Recovery protection enabled.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.
* Each cluster server has four network cards (NICs) of one Gbps each.
* Two of the network cards are connected to an iSCSI private network, and two are connected to an external enterprise network. One of the external networks is reserved for cluster communications only.

![Primary hardware requirements](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 has 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Secondary site

* The secondary site has a six-node failover cluster.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.

![Primary hardware specification](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Server workloads

* For test purposes we picked workloads commonly used in enterprise customer scenarios.
* We use [IOMeter](http://www.iometer.org) with the workload characteristic summarized in the table for simulation.
* All IOMeter profiles are set to write random bytes to simulate worst-case write patterns for workloads.

| Workload | I/O size (KB) | % Access | %Read | Outstanding I/Os | I/O pattern |
| --- | --- | --- | --- | --- | --- |
| File Server |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |All 100% random |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% random<br />100% sequential |
| Exchange |32 |100% |67% |8 |100% random |
| Workstation/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Both 100% random |
| Web File Server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |All 75% random |

### VM configuration

* 470 VMs on the primary cluster.
* All VMs with VHDX disk.
* VMs running workloads summarized in the table. All were created with VMM templates.

| Workload | # VMs | Minimum RAM (GB) | Maximum RAM (GB) | Logical disk size (GB) per VM | Maximum IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| File Server |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Web server |149 |.5 |1 |80 |6 |
| TOTAL |470 | | |96.83 TB |4108 |

### Site Recovery settings

* Site Recovery was configured for on-premises to on-premises protection
* The VMM server has four clouds configured, containing the Hyper-V cluster servers and their VMs.

| Primary VMM cloud | Protected VMs | Replication frequency | Additional recovery points |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 mins |None |
| PrimaryCloudRpo30s |47 |30 secs |None |
| PrimaryCloudRpo30sArp1 |47 |30 secs |1 |
| PrimaryCloudRpo5m |235 |5 mins |None |

### Performance metrics

The table summarizes the performance metrics and counters that were measured in the deployment.

| Metric | Counter |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Доступная память |\Память\Доступный объем в МБ |
| Операций ввода-вывода в сек. |\Диск\Физический диск(_общий объем ресурсов) в секунду |
| Операции чтения (IOPS) виртуальной машины в секунду |\Хипер-в виртуального запоминающего устройства (\<VHD >) \реад операций в секунду |
| Операции записи (IOPS) виртуальной машины в секунду |\Хипер-в виртуальных устройств хранения данных\<(VHD >) \врите операций/с |
| Пропускная способность операций чтения виртуальной машины |Виртуальное запоминающее устройство \хипер-в\<(VHD >) \реад байт/с |
| Пропускная способность операций записи виртуальной машины |Виртуальное запоминающее устройство \хипер-в\<(VHD >) \врите байт/с |

## <a name="next-steps"></a>Следующие шаги

[Настройка аварийного восстановления для виртуальных машин Hyper-V на дополнительный локальный сайт](hyper-v-vmm-disaster-recovery.md)
