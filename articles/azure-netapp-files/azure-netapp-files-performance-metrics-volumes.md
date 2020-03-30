---
title: Рекомендуемые тесты на тестовую производительность - Файлы Azure NetApp
description: Узнайте о рекомендациях по тестированию эталонов для производительности и метрик с помощью файлов Azure NetApp.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: 8f354152c23dd7ad0413f27585d724f8070ca003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551528"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Рекомендации по тестам производительности для Azure NetApp Files

В этой статье содержатся рекомендации по тестированию объема и метрик с использованием файлов Azure NetApp.

## <a name="overview"></a>Обзор

Чтобы понять характеристики производительности объема файлов Azure NetApp, можно использовать инструмент с открытым исходным кодом [FIO](https://github.com/axboe/fio) для запуска ряда тестовых показателей для имитации различных рабочих нагрузок. FIO может быть установлена как на операционных системах на базе Linux, так и на Windows.  Это отличный инструмент, чтобы получить быстрый снимок как IOPS и пропускной их объем для объема.

### <a name="vm-instance-sizing"></a>Размер ыикса VM

Для достижения наилучших результатов убедитесь, что вы используете виртуальный экземпляр машины (VM), который соответствует размеру для выполнения тестов. В следующих примерах используется Standard_D32s_v3 экземпляр. Для получения дополнительной информации о размерах экземпляров VM см. [Размеры для виртуальных машин Windows в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) для виртуальных компьютеров на базе Windows и [размеры для виртуальных машин Linux в Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) для виртуальных компьютеров на базе Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Размер объема файлов Azure NetApp

Убедитесь, что вы выбираете правильный уровень обслуживания и размер квоты объема для ожидаемого уровня производительности. Для получения дополнительной информации можно [ознакомиться с уровнями обслуживания для файлов NetApp Azure.](azure-netapp-files-service-levels.md)

### <a name="virtual-network-vnet-recommendations"></a>Рекомендации виртуальной сети (VNet)

Вы должны выполнить тест в том же VNet, что и файлы NetApp Azure. Приведенный ниже пример демонстрирует рекомендацию:

![Рекомендации VNet](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Установка FIO

FIO доступен в двоичном формате как для Linux, так и для Windows. Следуйте разделу двоичные пакеты в [FIO,](https://github.com/axboe/fio) чтобы установить для платформы по вашему выбору.

## <a name="fio-examples-for-iops"></a>Примеры FIO для IOPS 

Примеры FIO в этом разделе используют следующую настройку:
* Размер экземпляра VM: D32s_v3
* Уровень и размер обслуживания пула емкости: Премиум / 50 TiB
* Размер квоты: 48 TiB

Следующие примеры показывают, FIO случайных читает и пишет.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k размер блока 100% случайных считывает

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Выход: 68k читать IOPS отображается

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k блок размер 100% случайных пишет

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Выход: 73k написать IOPS отображается

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Примеры FIO для пропускной способности

Примеры в этом разделе показывают, что FIO последовательно читает и пишет.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64k размер блока 100% последовательных читает

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Выход: отображаемый 11,8 Гбит/с пропускной

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64k блок размер 100% последовательных пишет

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Выход: отображается 12,2 Гбит/с пропускной пролимк

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Показатели объема

Данные о производительности файлов Azure NetApp доступны через счетчики Azure Monitor. Счетчики доступны через портал Azure и запросы REST API GET. 

Исторические данные можно просмотреть по следующим данным:
* Средняя задержка чтения 
* Средняя задержка записи 
* Читать IOPS (в среднем)
* Написать IOPS (средний)
* Логический размер объема (средний)
* Размер снимка объема (средний)

### <a name="using-azure-monitor"></a>Использование Azure Monitor 

Вы можете получить доступ к счетчикам файлов Azure NetApp на основе объема со страницы Метрики, как показано ниже:

![Метрики Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Вы также можете создать панель мониторинга в Azure Monitor для файлов NetApp Azure, перейдя на страницу Метрики, отфильтровав для NetApp и указав счетчики громкости, представляющие интерес: 

![Панель мониторинга Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Доступ к API Azure Monitor

Вы можете получить доступ к счетчикам файлов Сети Azure с помощью вызовов REST API. Смотрите [поддерживаемые метрики с помощью Azure Monitor: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) для счетчиков для пулов и объемов емкости.

В следующем примере показан URL GET для просмотра логического размера громкости:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Дальнейшие действия

- [Уровни обслуживания для Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Тесты производительности для Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)