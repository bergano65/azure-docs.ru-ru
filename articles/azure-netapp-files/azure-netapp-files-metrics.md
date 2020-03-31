---
title: Метрики для службы Azure NetApp Files | Документация Майкрософт
description: В этой статье описываются метрики для Azure NetApp Files.
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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: c8e3b616dee1ab4e6bb6e77c6a8bab5661d4e20b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460438"
---
# <a name="metrics-for-azure-netapp-files"></a>Метрики для Azure NetApp Files

Azure NetApp Files предоставляет метрики по выделенной памяти, фактическому использованию хранилища, объему IOPS и задержке. Проанализировав эти метрики, вы сможете узнать больше о производительности томов и шаблонах использования учетных записей NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Использование метрик для пулов емкости

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Бассейн, выделенный на размер объема*  
    Общая квота объема (GiB) в пуле данных емкости (т.е. общая сумма подготовленных размеров объемов в пуле емкости).  
    Этот размер — это размер, выбранный при создании тома.  
- *Бассейн Потребляемый размер*  
    Общее логическое пространство (GiB), используемое в разных объемах в пуле емкости.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Показатели использования для томов

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Объем потребляемый размер*   
    Общее логическое пространство, используемое в томе (GiB).  
    Сюда можно отнести логическое пространство используемое активными файлами системы и моментальными снимками.  
- *Размер снимка объема*   
   Инкрементное логическое пространство, используемое снимками в томе.  

## <a name="performance-metrics-for-volumes"></a>Показатели производительности для томов

- *AverageReadLatency*   
    Среднее время чтения от объема в миллисекундах.
- *СредняяЗаписьЛа*   
    Среднее время записей из объема в миллисекундах.
- *ReadIops*   
    Количество считываемых до объема в секунду.
- *WriteIops*   
    Количество записок в объем в секунду.

## <a name="next-steps"></a>Дальнейшие действия

* [Общие сведения об иерархии хранилища Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)
* [Создание тома для Azure NetApp Files](azure-netapp-files-create-volumes.md)
