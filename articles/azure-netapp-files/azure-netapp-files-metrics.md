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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460438"
---
# <a name="metrics-for-azure-netapp-files"></a>Метрики для Azure NetApp Files

Azure NetApp Files предоставляет метрики для выделенного хранилища, фактического использования хранилища, операций ввода-вывода томов и задержки. Проанализировав эти метрики, вы сможете узнать больше о производительности томов и шаблонах использования учетных записей NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Использование метрик для пулов емкости

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Пул, выделенный для размера тома*  
    Суммарная квота тома (гиб) в заданном пуле емкости (то есть общая сумма подготовленных объемов томов в пуле емкости).  
    Этот размер равен размеру, выбранному при создании тома.  
- *Размер использованного пула*  
    Общий объем логического пространства (гиб), используемого на томах в пуле емкости.  
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
- *Объем использованного объема тома*   
    Общее логическое пространство, используемое в томе (гиб).  
    Сюда можно отнести логическое пространство используемое активными файлами системы и моментальными снимками.  
- *Размер моментального снимка тома*   
   Добавочное логическое пространство, используемое моментальными снимками в томе.  

## <a name="performance-metrics-for-volumes"></a>Метрики производительности для томов

- *аверажереадлатенци*   
    Среднее время чтения с тома в миллисекундах.
- *аверажеврителатенци*   
    Среднее время записи с тома в миллисекундах.
- *реадиопс*   
    Количество операций чтения в томе в секунду.
- *вритеиопс*   
    Число операций записи в том в секунду.

## <a name="next-steps"></a>Дальнейшие шаги

* [Общие сведения об иерархии хранилища Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)
* [Создание тома для Azure NetApp Files](azure-netapp-files-create-volumes.md)
