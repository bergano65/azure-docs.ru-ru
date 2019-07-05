---
title: Тестирование производительности тома и метрик, использующих службу файлов Azure NetApp производительности | Документация Майкрософт
description: Предоставляет рекомендации по производительности тома и метрик, использующих службу файлов Azure NetApp тестирование производительности.
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
ms.date: 06/26/2019
ms.author: b-juche
ms.openlocfilehash: 12ae9e313655924f11799152b5e58b77776c135c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478814"
---
# <a name="benchmark-testing-for-volume-performance-and-metrics-using-azure-netapp-files"></a>Тестирование для определения метрик и производительности тома с использованием Azure NetApp Files

Эта статья содержит рекомендации по производительности тома и метрик, использующих службу файлов Azure NetApp тестирование производительности.

## <a name="overview"></a>Обзор

Чтобы понять характеристики производительности тома службы файлов Azure NetApp, можно использовать средства с открытым кодом [FIO](https://github.com/axboe/fio) для выполнения ряда тестов производительности для моделирования различных рабочих нагрузок. FIO можно установить на обоих Linux и Windows на основе операционных систем.  Он является отличным инструментом, который быстро получить представление операций ввода-ВЫВОДА и пропускной способности для тома.

### <a name="vm-instance-sizing"></a>Выбора размера экземпляра виртуальной Машины

Для получения наилучших результатов убедитесь, что вы используете экземпляр виртуальной машины (VM), которая нужна для настройки размера для выполнения тестов. В следующих примерах используется экземпляр Standard_D32s_v3. Дополнительные сведения о размеров экземпляров виртуальных Машин, см. в разделе [Windows для размеров виртуальных машин в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) для виртуальных машин на основе Windows и [размеры виртуальных машин Linux в Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) для виртуальных машин под управлением Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Azure размера тома файлов NetApp

Убедитесь, что выбрано правильной службе уровень и тома размером квоты ожидаемый уровень производительности. См. в разделе [уровни обслуживания для службы файлов Azure NetApp](azure-netapp-files-service-levels.md) Дополнительные сведения.

### <a name="virtual-network-vnet-recommendations"></a>Рекомендации по виртуальной сети (VNet)

Вам необходимо выполнить тестирование в той же виртуальной сети Azure NetApp файлы производительности. В приведенном ниже примере показаны рекомендации:

![Рекомендации по виртуальной сети](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Установка FIO

FIO доступен в двоичный формат для Windows и Linux. Перейдите к разделу двоичные пакеты в [FIO](https://github.com/axboe/fio) для установки на платформе по своему усмотрению.

## <a name="fio-examples-for-iops"></a>Примеры FIO для операций ввода-ВЫВОДА 

FIO примеры в этом разделе используются следующие настройки:
* Размер экземпляра виртуальной Машины: D32s_v3
* Уровень обслуживания пула емкостей и размер: "Премиум" / 50 (тиб)
* Размер тома квоты: 48 Тиб

В следующих примерах FIO случайных операций чтения и записи.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: произвольное чтение 100% размера блока 8 КБ

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Выходные данные: 68k чтение операций ввода-ВЫВОДА отображается

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: размер 100% случайные записи блока 8 КБ

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Выходные данные: 73k записи отображается операций ввода-ВЫВОДА

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Примеры FIO для пропускной способности

В примерах этого раздела показано FIO последовательный, чтение и запись.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 100% размера: последовательные операции чтения блока 64 КБ

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Выходные данные: 11.8 Гбит/с пропускной способности отображаются

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64k блокировать размер последовательных 100% операций записи.

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Выходные данные: 12.2 Гбит/с пропускной способности отображаются

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Метрики объема

Azure данные производительности NetApp файлы будут доступны через Azure Monitor счетчики. Счетчики доступны через портал Azure и запросы REST API GET. 

Вы можете просмотреть исторические данные для следующие сведения:
* Средняя задержка чтения 
* Средняя задержка записи 
* Чтение операций ввода-ВЫВОДА (среднее)
* Записи операций ввода-ВЫВОДА (среднее)
* Логический размер тома (среднее)
* Размер моментального снимка тома (среднее)

### <a name="using-azure-monitor"></a>Использование Azure Monitor 

Доступны счетчики NetApp службы файлов Azure на основе каждого тома на странице метрик, как показано ниже:

![Метрики Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Также можно создать панели мониторинга в Azure Monitor для службы файлов Azure NetApp, переход на страницу метрики, фильтрация NetApp и указав интересующие счетчики тома: 

![Панель мониторинга Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Доступ к Azure Monitor API

Можно получить доступ к счетчикам NetApp службы файлов Azure с помощью вызовов REST API. См. в разделе [поддерживается метрики Azure Monitor: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) счетчиков для пулов емкости и тома.

В следующем примере показано получение URL-адреса для просмотра размер логического тома:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Дальнейшие действия

- [Service levels for Azure NetApp Files](azure-netapp-files-service-levels.md) (Уровни обслуживания для для службы Azure NetApp Files)
- [Показатели производительности для службы файлов Azure NetApp](azure-netapp-files-performance-benchmarks.md)