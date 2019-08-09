---
title: Рекомендации по тестированию производительности для Azure NetApp Files | Документация Майкрософт
description: Содержит рекомендации по тестированию производительности и метрики с помощью Azure NetApp Files.
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
ms.openlocfilehash: 1969b3c237a4133df6f53bd6426ca4d50581cbcb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881732"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Рекомендации по тестированию производительности для Azure NetApp Files

В этой статье приводятся рекомендации по тестированию производительности и метрик с помощью Azure NetApp Files.

## <a name="overview"></a>Обзор

Чтобы понять характеристики производительности Azure NetApp Filesного тома, можно использовать средство с открытым исходным кодом [FIO](https://github.com/axboe/fio) для выполнения серии тестов для моделирования различных рабочих нагрузок. FIO можно установить в операционных системах на базе Linux и Windows.  Это отличное средство для получения быстрого моментального снимка операций ввода-вывода и пропускной способности для тома.

### <a name="vm-instance-sizing"></a>Размер экземпляра виртуальной машины

Для получения наилучших результатов убедитесь, что для выполнения тестов используется экземпляр виртуальной машины, соответствующий размеру. В следующих примерах используется экземпляр Standard_D32s_v3. Дополнительные сведения о размерах экземпляров виртуальных машин см. в статьях [размеры виртуальных машин Windows в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) для виртуальных машин под управлением Windows и [размеры виртуальной машины Linux в Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) для ВМ под управлением Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Изменение размера тома Azure NetApp Files

Убедитесь, что выбран правильный уровень обслуживания и квота тома для ожидаемого уровня производительности. Дополнительные сведения см. в разделе [уровни обслуживания для Azure NetApp Files](azure-netapp-files-service-levels.md) .

### <a name="virtual-network-vnet-recommendations"></a>Рекомендации по виртуальной сети (VNet)

Тестирование производительности следует выполнять в той же виртуальной сети, что и Azure NetApp Files. В приведенном ниже примере демонстрируется рекомендация.

![Рекомендации по виртуальной сети](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Установка FIO

FIO доступен в двоичном формате как для Linux, так и для Windows. Следуйте указаниям в разделе двоичные пакеты в [FIO](https://github.com/axboe/fio) , чтобы установить для выбранной платформы.

## <a name="fio-examples-for-iops"></a>Примеры FIO для операций ввода-вывода 

В примерах FIO в этом разделе используется следующая настройка:
* Размер экземпляра виртуальной машины: D32s_v3
* Уровень и размер службы пула ресурсов: Premium или 50 тиб
* Размер квоты тома: 48 тиб

В следующих примерах показаны FIO случайные операции чтения и записи.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO Размер блока 8 КБ 100% случайных операций чтения

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Выходные данные: 68k прочитанные операции ввода-вывода

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO Размер блока 8 КБ 100% случайных записей

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Выходные данные: 73k записи ввода-вывода

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Примеры FIO для пропускной способности

В примерах этого раздела показаны FIO последовательные операции чтения и записи.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO Размер блока 64k 100% последовательных операций чтения

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Выходные данные: отображается пропускная способность 11,8 Гбит/с

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO Размер блока 64k 100% последовательных операций записи

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Выходные данные: отображается пропускная способность 12,2 Гбит/с

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Метрики тома

Данные о производительности Azure NetApp Files доступны через счетчики Azure Monitor. Счетчики доступны с помощью портал Azure и REST API запросов GET. 

Вы можете просматривать исторические данные по следующим данным:
* Средняя задержка чтения 
* Средняя задержка записи 
* Чтение операций ввода-вывода (в среднем)
* Запись операций ввода-вывода (в среднем)
* Логический размер тома (среднее)
* Размер моментального снимка тома (среднее)

### <a name="using-azure-monitor"></a>Использование Azure Monitor 

Доступ к счетчикам Azure NetApp Files для каждого тома можно получить на странице метрики, как показано ниже.

![Метрики Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Вы также можете создать панель мониторинга в Azure Monitor для Azure NetApp Files, перейдя на страницу метрики, отфильтровать NetApp и указав интересующие вас счетчики томов: 

![Панель мониторинга Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Доступ к Azure Monitor API

Доступ к счетчикам Azure NetApp Files можно получить с помощью вызовов REST API. См [. раздел Поддерживаемые метрики с Azure Monitor. Microsoft. NetApp/нетаппаккаунтс/капаЦитипулс/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) для счетчиков пулов и томов емкости.

В следующем примере показан URL-адрес GET для просмотра размера логического тома.

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Следующие шаги

- [Service levels for Azure NetApp Files](azure-netapp-files-service-levels.md) (Уровни обслуживания для для службы Azure NetApp Files)
- [Показатели производительности для Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)