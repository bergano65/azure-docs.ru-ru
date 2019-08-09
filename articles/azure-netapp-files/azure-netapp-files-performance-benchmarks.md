---
title: Результаты теста производительности для Azure NetApp Files | Документация Майкрософт
description: Описание результатов тестирования производительности для Azure NetApp Files на уровне тома.
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
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881754"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Результаты теста производительности для Azure NetApp Files

В этой статье описываются результаты тестирования производительности для Azure NetApp Files на уровне тома. 

## <a name="sample-application-used-for-the-tests"></a>Пример приложения, используемый для тестов

Тесты производительности выполнялись с примером приложения, использующего Azure NetApp Files. Приложение имеет следующие характеристики. 

* Приложение на основе Linux, созданное для облака
* Можно линейно масштабировать с добавленными виртуальными машинами для увеличения мощности вычислений по мере необходимости.
* Требует быстрого доступа к Data Lake
* Содержит шаблоны ввода-вывода, иногда случайные и иногда последовательные 
    * Случайный шаблон требует низкой задержки для больших объемов операций ввода-вывода. 
    * Для последовательного шаблона требуется большой объем пропускной способности. 

## <a name="about-the-workload-generator"></a>О генераторе рабочей нагрузки

Результаты берутся из файлов сводки Вдбенч. [Вдбенч](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) — это служебная программа командной строки, которая создает рабочие нагрузки дискового ввода-вывода для проверки производительности хранилища. Используемая конфигурация "клиент-сервер" масштабируется.  Он включает один смешанный основной/клиент и 14 выделенных виртуальных машин клиента.

## <a name="about-the-tests"></a>О тестах

Тесты были разработаны для обнаружения ограничений, которые может иметь образец приложения, и времени отклика, которое определяется ограничениями.  

Были выполнены следующие тесты: 

* 100% 8 — КИБ случайное чтение
* 100% 8 — КИБ случайная запись
* 100% 64-КИБ последовательное чтение
* 100% 64-КИБ последовательная запись
* 50% 64-КИБ последовательный чтение, 50% 64-КИБ последовательная запись
* 50% 8 — КИБ случайное чтение, 50% 8 — КИБ случайная запись

## <a name="bandwidth"></a>Пропускная способность

Azure NetApp Files предлагает несколько [уровней обслуживания](azure-netapp-files-service-levels.md). Каждый уровень обслуживания имеет разный объем пропускной способности для каждого Тиб подготовленной емкости (квота тома). Ограничение пропускной способности для тома подготавливается на основе сочетания уровня обслуживания и квоты тома. Ограничение пропускной способности относится только к одному фактору при определении фактического объема пропускной способности, которая будет реализована.  

В настоящее время 4 500 MiB — это самая высокая пропускная способность, достигнутая рабочей нагрузкой на один том в тестировании.  На уровне службы Premium квота тома 70,31 Тиб обеспечит достаточную пропускную способность, чтобы реализовать эту пропускную способность в соответствии с приведенными ниже вычислениями. 

![Формула пропускной способности](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Квота и уровень обслуживания](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Рабочие нагрузки с большим объемом пропускной способности

Тест пропускной способности использовал Вдбенч и сочетание виртуальных машин хранилища 12xD32s v3. Пример тома в тесте достиг следующих номеров пропускной способности:

![Проверка пропускной способности](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Рабочие нагрузки с интенсивным вводом-выводом

Тест ввода-вывода использовал Вдбенч и сочетание виртуальных машин хранилища 12xD32s v3. Пример тома в тесте достиг следующих номеров операций ввода-вывода:

![Тест ввода-вывода](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Задержка

Расстояние между тестовыми виртуальными машинами и Azure NetApp Filesным томом влияет на производительность операций ввода-вывода.  На приведенной ниже диаграмме сравниваются кривые ответов операций ввода-вывода и задержки для двух разных наборов виртуальных машин.  Один набор виртуальных машин приближается Azure NetApp Files, а другой набор — еще дальше.  Увеличенная задержка для дальнейшего набора виртуальных машин влияет на количество операций ввода-вывода в секунду, достигнутых на заданном уровне параллелизма.  В любом случае чтение с тома может превышать 300 000 операций ввода-вывода, как показано ниже: 

![Пример задержки](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Сводка

Рабочие нагрузки, учитывающие задержку (базы данных), могут иметь время ответа в течение одной миллисекунды. Для одного тома может быть превышена производительность транзакций 300 тыс операций ввода-вывода в секунду.

Приложения с учетом пропускной способности (для потоковой передачи и создания образа) могут иметь пропускную способность 4.5 гиб/с.

## <a name="example-scripts"></a>Примеры скриптов

Приведенные ниже примеры сценариев предназначены только для демонстрационных целей.  Они не предназначены для использования в производственных целях.  

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
