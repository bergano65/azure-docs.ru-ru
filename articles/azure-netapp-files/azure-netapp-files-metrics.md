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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 7cf382f511d2ba8452d77bf207f36b749cb31e94
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848794"
---
# <a name="metrics-for-azure-netapp-files"></a>Метрики для Azure NetApp Files

Служба Azure NetApp Files предоставляет метрики для выделенного хранилища, фактического использования емкости хранилища, пропускной способности тома, операций ввода-вывода в секунду и задержки. Проанализировав эти метрики, вы сможете узнать больше о производительности томов и шаблонах использования учетных записей NetApp.  

## <a name="capacity_pools"></a>Использование метрик для пулов емкости

<!-- 
- *Volume pool allocated size*  
    The size (GiB) of the provisioned capacity pool  
--> 
- *Volume pool allocated used* (Используемый размер выделенного пула для тома)  
    Общая квота тома (гиб) в заданном пуле емкости (т. е. Общее количество подготовленных объемов томов в пуле емкости)  
    Размер, выбранный в процессе создания тома.  
- *Volume pool total logical size* (Общий логический размер пула для тома)  
    Общий объем логического пространства (гиб), используемого в томах в пуле емкости  
<!-- 
- *Volume pool total snapshot size*  
    The total of incremental logical space used by the snapshots  
-->

## <a name="volumes"></a>Показатели использования для томов

<!-- 
- *Volume allocated size*   
    The volume size (quota) provisioned in GiB  
--> 
- *Volume logical size*  (Логический размер тома)  
    Общее логическое пространство, используемое в томе (гиб)  
    Сюда можно отнести логическое пространство используемое активными файлами системы и моментальными снимками.  
- *Volume snapshot size*  (Размер моментальных снимков в томе)  
   Добавочное логическое пространство, используемое моментальными снимками в томе  

## <a name="performance-metrics-for-volumes"></a>Метрики производительности для томов

- *аверажереадлатенци*   
    Среднее время чтения с тома (в миллисекундах)
- *аверажеврителатенци*   
    Среднее время записи с тома в миллисекундах
- *реадиопс*   
    Число операций чтения в томе в секунду
- *вритеиопс*   
    Число операций записи в том в секунду

## <a name="next-steps"></a>Следующие шаги

* [Общие сведения об иерархии хранилища Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)
* [Создание тома для Azure NetApp Files](azure-netapp-files-create-volumes.md)
