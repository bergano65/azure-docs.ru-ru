---
title: Результаты теста теста на производительность для файлов NetApp Azure (ru) Документы Майкрософт
description: Описывает результаты тестовых тестов для файлов Azure NetApp на уровне объема.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1d6b43110046f26d8c8070b19587366588eee7b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68881754"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Результаты тестов производительности для Azure NetApp Files

В этой статье описываются результаты тестовых тестов производительности файлов Azure NetApp на уровне объема. 

## <a name="sample-application-used-for-the-tests"></a>Пример применения, используемого для тестов

Тесты производительности были запущены с помощью образца приложения с использованием файлов Azure NetApp. Приложение имеет следующие характеристики: 

* Приложение на основе Linux, построенное для облака
* Может масштабироваться линейно с добавлением виртуальных машин (VM) для увеличения вычислительной мощности по мере необходимости
* Требует быстрой доступности озера данных
* Имеет i/O шаблоны, которые иногда являются случайными, а иногда и последовательными 
    * Случайная схема требует низкой задержки для больших количеств вв/причин. 
    * Последовательный шаблон требует большого количества пропускной способности. 

## <a name="about-the-workload-generator"></a>О генераторе рабочей нагрузки

Результаты приходят из резюме Vdbench файлов. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) — это утилита командной строки, которая генерирует рабочие нагрузки на ввоза/вво-диск для проверки производительности хранилища. Используемая конфигурация клиента и сервера масштабируема.  Она включает в себя один смешанный мастер / клиент и 14 выделенных клиентов VMs.

## <a name="about-the-tests"></a>О тестах

Тесты были разработаны для определения пределов, которые может иметь применение образца, и времени отклика, которое кривых до предела.  

Были проведены следующие тесты: 

* 100% 8-KiB случайного чтения
* 100% 8-KiB случайных написать
* 100% 64-KiB последовательное чтение
* 100% 64-KiB последовательной записи
* 50% 64-KiB последовательное чтение, 50% 64-KiB последовательного написать
* 50% 8-KiB случайного чтения, 50% 8-KiB случайных написать

## <a name="bandwidth"></a>Пропускная способность

Лазурные файлы NetApp предлагает несколько [уровней обслуживания.](azure-netapp-files-service-levels.md) Каждый уровень обслуживания предлагает различное количество пропускной способности на TiB предусмотренной емкости (квота объема). Предел пропускной способности для объема предоставляется на основе сочетания уровня обслуживания и квоты объема. Ограничение пропускной способности является лишь одним из факторов, определяющих фактическое количество пропускной способности, которая будет реализована.  

В настоящее время 4500 MiB является самой высокой пропускной темой, которая была достигнута за счет рабочей нагрузки против одного объема в тестировании.  С уровнем обслуживания Premium, квота объема 70.31 TiB обеспечит достаточную пропускную способность для того чтобы осуществить эту пропускную способность в вычисление ниже: 

![Формула пропускной способности](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Квота и уровень обслуживания](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Интенсивные рабочие нагрузки

В тесте на пропускную столы использовались Vdbench и комбинация V3-ми vMs-хранилища V3 12xD32s. Объем выборки в тесте достиг следующих показателей пропускной связи:

![Тест на пропускную выгоду](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Вво-O-интенсивные нагрузки

В тесте I/O использовались Vdbench и комбинация V3-ми по хранению V3 12xD32s. Объем выборки в тесте достиг следующих номеров вв./о:

![Тест на вик-ви](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Задержка

Расстояние между тестовыми ВМ и объемом файлов NetApp Azure влияет на производительность ввоза/ввоза.  На приведенной ниже диаграмме сравниваются кривые реакции IOPS и задержки для двух различных наборов VM.  Один набор VMs находится рядом с файлами Azure NetApp, а другой — дальше.  Увеличение задержки для дальнейшего набора ВМ влияет на количество IOPS, достигнутых на данном уровне параллелизма.  Несмотря на это, читает против объема может превышать 300000 IOPS, как показано ниже: 

![Исследование задержки](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Сводка

Рабочие нагрузки с задержкой (базы данных) могут иметь одномиллисекундное время отклика. Производительность транзакций может быть более 300k IOPS для одного тома.

Чувствительные к пропускной информации приложения (для потоковой передачи и визуализации) могут иметь пропускную выгоду 4,5GiB/s.

## <a name="example-scripts"></a>Примеры скриптов

Следующие примеры скрипты предназначены только для демонстрационных целей.  Они не должны использоваться в производственных целях.  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
