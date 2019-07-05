---
title: Показатели производительности для службы файлов Azure NetApp | Документация Майкрософт
description: Описывает результаты тестов производительности производительности для NetApp службы файлов Azure на уровне тома.
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 14081daf1f45a84bc8ad19bf0239db1281d9e624
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449500"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Тесты производительности для Azure NetApp Files

Этой статье описываются результаты тестов производительности производительности для NetApp службы файлов Azure на уровне тома. 

## <a name="sample-application-used-for-the-tests"></a>Пример приложения, используемого для тестов

С помощью примера приложения, использующих службу файлов Azure NetApp выполнялись тесты производительности. Приложение имеет следующие характеристики: 

* Приложение под управлением Linux, предназначенных для облака
* Можно линейно зависеть от добавленных виртуальных машин (ВМ) для повышения вычислительной мощности, при необходимости
* Требуется быстрая доступность data lake
* Содержит шаблоны ввода-вывода, которые иногда находятся иногда последовательных и случайных 
    * Шаблон случайных требует низкой задержкой для большого количества операций ввода-вывода. 
    * Шаблон последовательного требует большого объема пропускной способности. 

## <a name="about-the-workload-generator"></a>О генератор рабочей нагрузки

Результаты поступают из файлов сводных Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) — это программа командной строки, которая создает рабочих нагрузок дискового ввода-вывода для проверки производительности хранилища. Клиент сервер конфигурацию, используемую масштабируется.  Он включает один смешанной master/клиент и 14 выделенные клиентские виртуальные машины.

## <a name="about-the-tests"></a>О тестах

Тесты предназначены для определения ограничения, которые могут иметь пример приложения и время отклика, кривых в установленных пределах.  

Выполнялись следующие тесты: 

* 100% 8-Киб произвольного чтения
* 100% 8 Киб произвольной записи
* 100% 64-Киб последовательного чтения
* 100% 64 Киб последовательной записи
* 50% 64-Киб последовательного чтения, 50% 64 Киб последовательной записи
* 50% 8-Киб произвольного чтения, произвольной записи 8 Киб 50%

## <a name="bandwidth"></a>Пропускная способность

Служба файлов Azure NetApp предоставляет несколько [уровней обслуживания](azure-netapp-files-service-levels.md). Каждый уровень службы предлагает различный объем пропускной способности сети / Тиб подготовленной емкости (квота на количество). Ограничение пропускной способности для тома подготавливается на основе сочетания уровня обслуживания и квота на количество. Ограничение пропускной способности применяется только один фактор при определении фактический объем пропускной способности, которая будет обеспечиваться.  

В настоящее время 4500 MiB является наивысшей производительности, что выполнено рабочую нагрузку для одного тома в тестировании.  С помощью уровня обслуживания "премиум" Квота на количество из 70.31 Тиб будет подготовить пропускную способность для реализации этой пропускная способность для каждого вычисления ниже: 

![Формула пропускной способности](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Квоты и уровня обслуживания](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Рабочие нагрузки с интенсивным использованием пропускной способности

Тест пропускной способности используется Vdbench и сочетание 12xD32s V3 хранилища виртуальных машин. Пример тома в тесте удалось добиться следующие номера пропускной способности.

![Тест пропускной способности](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Рабочие нагрузки ввода-вывода

Операций ввода-вывода тестировании применялась Vdbench и сочетание 12xD32s V3 хранилища виртуальных машин. Пример тома в тесте удалось добиться следующих чисел ввода/вывода.

![Операций ввода-вывода тестов](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Задержка

Расстояние между тестовых виртуальных машин и тома службы файлов Azure NetApp оказывает влияние на производительность операций ввода-вывода.  На представленной ниже диаграмме сравниваются операций ввода-ВЫВОДА и кривые задержки ответа для двух разных наборов виртуальных машин.  Один набор виртуальных машин приближается к службе файлов Azure NetApp и другой — дальнейшие немедленно.  Повышенная задержка для дальнейшего набор виртуальных машин оказывает влияние на объем операций ввода-ВЫВОДА, реализовать на данном уровне параллелизма.  В любом случае операций чтения в томе может превышать 300 000 операций ввода-ВЫВОДА, как показано ниже: 

![Пример использования задержки](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Сводка

Рабочих нагрузок с небольшими задержками (баз данных) может иметь время ответа одной миллисекунды. Производительность транзакций может быть более чем 300 тысяч операций ввода-ВЫВОДА для одного тома.

Приложений, чувствительных к пропускной способности (для потоковой передачи и работы с образами) может иметь 4.5GiB / с пропускной способностью прибл.

## <a name="example-scripts"></a>Примеры скриптов

Ниже приведены примеры скриптов находятся только в целях демонстрации.  Они не должна использоваться в производственных целях.  

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
